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
        return distance < (self.radius + other.radius)
    def rotate_around_start(self, angle_deg):
        angle_rad = math.radians(angle_deg)
        start_x = self.x + self.radius * math.cos(math.radians(self.start_angle))
        start_y = self.y - self.radius * math.sin(math.radians(self.start_angle))

        dx = self.x - start_x
        dy = self.y - start_y

        new_dx = dx * math.cos(angle_rad) - dy * math.sin(angle_rad)
        new_dy = dx * math.sin(angle_rad) + dy * math.cos(angle_rad)

        self.x = start_x + new_dx
        self.y = start_y + new_dy
        self.start_angle += angle_deg
    def set_color(self, color):
        self.color = color
class App:
    def __init__(self, root):
        self.root = root
        self.root.title("Работа с дугами")
        self.canvas = tk.Canvas(root, width=400, height=400, bg='white')
        self.canvas.pack()
        self.arcs = []
        self.colors = ['red', 'green', 'blue', 'orange', 'purple']
        self.create_widgets()
    def create_widgets(self):
        frame = tk.Frame(self.root)
        frame.pack(pady=10)
        tk.Button(frame, text="Загрузить из файла", command=self.load_from_file).pack(side=tk.LEFT, padx=5)
        tk.Button(frame, text="Проверить пересечение", command=self.check_intersection).pack(side=tk.LEFT, padx=5)
        tk.Button(frame, text="Раскрасить", command=self.color_arcs).pack(side=tk.LEFT, padx=5)
        tk.Button(frame, text="Повернуть", command=self.rotate_arc).pack(side=tk.LEFT, padx=5)
    def load_from_file(self):
        filename = filedialog.askopenfilename()
        try:
            with open(filename, 'r') as f:
                for line in f:
                    data = line.strip().split()
                    x, y, radius, start_angle, extent = map(float, data)
                    self.arcs.append(Arc(x, y, radius, start_angle, extent))
            self.draw_arcs()
        except:
            print("Ошибка при загрузке файла")
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
                                   style=tk.ARC)
    def check_intersection(self):
        if len(self.arcs) >= 2:
            if self.arcs[0].check_intersection(self.arcs[1]):
                print("Дуги пересекаются")
            else:
                print("Дуги не пересекаются")
        else:
            print("Недостаточно дуг для проверки")
    def color_arcs(self):
        for i, arc in enumerate(self.arcs):
            arc.set_color(self.colors[i % len(self.colors)])
        self.draw_arcs()
    def rotate_arc(self):
        if self.arcs:
            angle = simpledialog.askfloat("Поворот", "Введите угол поворота в градусах:")
            if angle is not None:
                self.arcs[0].rotate_around_start(angle)
                self.draw_arcs()
if __name__ == "__main__":
    root = tk.Tk()
    app = App(root)
    root.mainloop()
