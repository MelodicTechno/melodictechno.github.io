---
title: 三种直线段扫描算法
date: 2025-01-08 21:54:03
tags: ["计算机图形学"]
excerpt: "三种直线段扫描算法的C++实现"
---

## 数值微分法

~~~cpp
void dDALine(int x0, int y0, int x1, int y1, int color) {
	int x;
	float dx, dy, y, k;
	dx = x1 - x0;
	dy = y1 - y0;
	k = dy / dx;
	y = y0;
	for (x = x0; x <= x1; x++) {
		drawPixel(x, round(y + 0.5), color);
		y = y + k;
	}
}
~~~

## 中点画线法

~~~cpp
#include <iostream>

// 输出像素信息
void drawPixel(int x, int y, int color) {
	// Draw a pixel at (x, y) with the specified color
	std::cout << "像素坐标：" << '(' << x << ',' << y << ')' << "颜色：" << color << std::endl;
}

// 重点画线算法
void middlePointLine(int x0, int y0, int x1, int y1, int color) {
	int a, b, d1, d2, d, x, y;
	a = y0 - y1;
	b = x1 - x0;
	d = 2 * a + b;
	std::cout << "判别值：" << d << std::endl;
	d1 = 2 * a;
	d2 = 2 * (a + b);
	x = x0;
	y = y0;
	drawPixel(x, y, color);
	while (x < x1) {
		if (d < 0) {
			x++;
			y++;
			d += d2;
		}
		else {
			x++;
			d += d1;
		}
		std::cout << "判别值：" << d << std::endl;
		drawPixel(x, y, color);
	}
}

void drawLine(int x0, int y0, int x1, int y1, int color) {
	std::cout << "画线，起点：" << '(' << x0 << ',' << y0 << ')' << "终点：" << '(' << x1 << ',' << y1 << ')' << "颜色：" << color << std::endl;
	middlePointLine(x0, y0, x1, y1, color);
}

int main() {
	std::cout << "演示中点画线算法" << std::endl;
	drawLine(0, 1, 7, 4, 1);
}
~~~

## bresenham算法

~~~cpp
// bresenham算法
void bresenhamLine(int x0, int y0, int x1, int y1, int color) {
	int x, y, dx, dy, e;
	dx = x1 - x0;
	dy = y1 - y0;
	e = -dx;
	x = x0;
	y = y0;
	for (int i = 0; i <= dx; i++) {
		drawPixel(x, y, color);
		x++;
		e = e + 2 * dy;
		if (e >= 0) {
			y++;
			e = e - 2 * dx;
		}
	}
}
~~~

## 效率比较

把三个算法做成header files only的library:

~~~cpp
#pragma once
#include <cmath>
#include <iostream>

void drawPixel(int x, int y, int color) {
	// 画点
	//std::cout << "像素点坐标：" << '(' << x << ',' << y << ')' << " 颜色：" << color << std::endl;
}

// 算法
void dDALine(int x0, int y0, int x1, int y1, int color) {
	int x;
	float dx, dy, y, k;
	dx = x1 - x0;
	dy = y1 - y0;
	k = dy / dx;
	y = y0;
	for (x = x0; x <= x1; x++) {
		drawPixel(x, round(y + 0.5), color);
		y = y + k;
	}
}

void middlePointLine(int x0, int y0, int x1, int y1, int color) {
	int a, b, d1, d2, d, x, y;
	a = y0 - y1;
	b = x1 - x0;
	d = 2 * a + b;
	d1 = 2 * a;
	d2 = 2 * (a + b);
	x = x0;
	y = y0;
	drawPixel(x, y, color);
	while (x < x1) {
		if (d < 0) {
			x++;
			y++;
			d += d2;
		}
		else {
			x++;
			d += d1;
		}
		drawPixel(x, y, color);
	}
}

// bresenham算法
void bresenhamLine(int x0, int y0, int x1, int y1, int color) {
	int x, y, dx, dy, e;
	dx = x1 - x0;
	dy = y1 - y0;
	e = -dx;
	x = x0;
	y = y0;
	for (int i = 0; i <= dx; i++) {
		drawPixel(x, y, color);
		x++;
		e = e + 2 * dy;
		if (e >= 0) {
			y++;
			e = e - 2 * dx;
		}
	}
}
~~~

然后测试：

~~~cpp
#include "CGLine.h"
#include <chrono>
#include <cstdlib>
#include <ctime>

int main() {
    // 设置随机种子
    std::srand(std::time(0));

    // 测试次数
    const int numTests = 1000;
    int x0, y0, x1, y1;
    int color = 1; // 颜色可以是一个常量，实际使用时可能是RGB等

    // 用于计时的变量
    std::chrono::high_resolution_clock::time_point start, end;
    std::chrono::duration<double> duration;

    // 测试DDA算法
    start = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < numTests; ++i) {
        x0 = std::rand() % 1000;
        y0 = std::rand() % 1000;
        x1 = std::rand() % 1000;
        y1 = std::rand() % 1000;
        dDALine(x0, y0, x1, y1, color);
    }
    end = std::chrono::high_resolution_clock::now();
    duration = std::chrono::duration_cast<std::chrono::duration<double>>(end - start);
    std::cout << "DDA算法运行时间: " << duration.count() << "秒\n";

    // 测试中点算法
    start = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < numTests; ++i) {
        x0 = std::rand() % 1000;
        y0 = std::rand() % 1000;
        x1 = std::rand() % 1000;
        y1 = std::rand() % 1000;
        middlePointLine(x0, y0, x1, y1, color);
    }
    end = std::chrono::high_resolution_clock::now();
    duration = std::chrono::duration_cast<std::chrono::duration<double>>(end - start);
    std::cout << "中点算法运行时间: " << duration.count() << "秒\n";

    // 测试Bresenham算法
    start = std::chrono::high_resolution_clock::now();
    for (int i = 0; i < numTests; ++i) {
        x0 = std::rand() % 1000;
        y0 = std::rand() % 1000;
        x1 = std::rand() % 1000;
        y1 = std::rand() % 1000;
        bresenhamLine(x0, y0, x1, y1, color);
    }
    end = std::chrono::high_resolution_clock::now();
    duration = std::chrono::duration_cast<std::chrono::duration<double>>(end - start);
    std::cout << "Bresenham算法运行时间: " << duration.count() << "秒\n";

    return 0;
}
~~~

结果：
~~~bash
DDA算法运行时间: 0.0060831秒
中点算法运行时间: 0.0004176秒
Bresenham算法运行时间: 0.0004899秒
~~~

我估计是因为：
DDA算法中，y与k必须用浮点数表示，而且每一步都要对y进行四舍五入后取整，故效率最差。
中点画线法合bresenham算法的时间复杂度相同，而且都可以摆脱浮点运算，但bresenham算法的乘法运算比中点画线法少，故bresenham的运行效率更高。
