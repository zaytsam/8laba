# 8laba
# -*- coding: Windows-1251 -*-
import tkinter as tk
from tkinter import filedialog, simpledialog
import math
class Arc:
    def __init__(self, x, y, radius, start_angle, extent):
        self.x = x
        self.y = y
        self.radius = radius
        self.start_angle = start_angle
        self.extent = extent
        self.color = 'black'
    def check_intersection(self, other):
        dx = self.x - other.x
        dy = self.y - other.y
        distance = math.sqrt(dx ** 2 + dy ** 2)
        if distance > (self.radius + other.radius) or distance < abs(self.radius - other.radius):
            return False
        return True
    def rotate_around_center(self, angle_deg):
        self.start_angle += angle_deg
        self.start_angle %= 360
    def set_color(self, color):
        self.color = color
class App:
    def __init__(self, root):
        self.root = root
        self.root.title("Работа с дугами")
        self.canvas = tk.Canvas(root, width=400, height=400, bg='white')
        self.canvas.pack()
        self.result_text = tk.Text(root, height=3, width=50)
        self.result_text.pack(pady=5)
        self.result_text.config(state=tk.DISABLED)
        self.arcs = []
        self.colors = ['red', 'green', 'blue', 'orange', 'purple', 'cyan', 'magenta', 'yellow']
        self.create_widgets()
    def create_widgets(self):
        frame = tk.Frame(self.root)
        frame.pack(pady=10)
        tk.Button(frame, text="Загрузить из файла", command=self.load_from_file).pack(side=tk.LEFT, padx=5)
        tk.Button(frame, text="Проверить пересечение", command=self.check_intersection).pack(side=tk.LEFT, padx=5)
        tk.Button(frame, text="Раскрасить", command=self.color_arcs).pack(side=tk.LEFT, padx=5)
        tk.Button(frame, text="Повернуть дугу", command=self.rotate_arc).pack(side=tk.LEFT, padx=5)
    def load_from_file(self):
        filename = filedialog.askopenfilename()
        try:
            with open(filename, 'r') as f:
                for line in f:
                    data = line.strip().split()
                    if len(data) >= 5:
                        x, y, radius, start_angle, extent = map(float, data[:5])
                        self.arcs.append(Arc(x, y, radius, start_angle, extent))
            self.draw_arcs()
            self.clear_result()
        except:
            pass
    def draw_arcs(self):
        self.canvas.delete("all")
        for arc in self.arcs:
            x0 = arc.x - arc.radius
            y0 = arc.y - arc.radius
            x1 = arc.x + arc.radius
            y1 = arc.y + arc.radius
            self.canvas.create_arc(x0, y0, x1, y1,
                                   start=arc.start_angle,
                                   extent=arc.extent,
                                   outline=arc.color,
                                   style=tk.ARC,
                                   width=2)
            self.canvas.create_oval(arc.x - 2, arc.y - 2, arc.x + 2, arc.y + 2, fill='red')
    def check_intersection(self):
        if len(self.arcs) < 2:
            self.show_result("Недостаточно дуг для проверки пересечения")
            return
        intersection_found = False
        for i in range(len(self.arcs)):
            for j in range(i + 1, len(self.arcs)):
                if self.arcs[i].check_intersection(self.arcs[j]):
                    intersection_found = True
                    break
            if intersection_found:
                break
        if intersection_found:
            self.show_result("Дуги пересекаются")
        else:
            self.show_result("Дуги не пересекаются")
    def color_arcs(self):
        for i, arc in enumerate(self.arcs):
            arc.set_color(self.colors[i % len(self.colors)])
        self.draw_arcs()
        self.clear_result()
    def rotate_arc(self):
        if not self.arcs:
            return
        angle = simpledialog.askfloat("Поворот", "Введите угол поворота в градусах:")
        if angle is None:
            return
        self.arcs[0].rotate_around_center(angle)
        self.draw_arcs()
        self.clear_result()
    def show_result(self, text):
        self.result_text.config(state=tk.NORMAL)
        self.result_text.delete(1.0, tk.END)
        self.result_text.insert(tk.END, text)
        self.result_text.config(state=tk.DISABLED)
    def clear_result(self):
        self.result_text.config(state=tk.NORMAL)
        self.result_text.delete(1.0, tk.END)
        self.result_text.config(state=tk.DISABLED)
if __name__ == "__main__":
    root = tk.Tk()
    app = App(root)
    root.mainloop()
