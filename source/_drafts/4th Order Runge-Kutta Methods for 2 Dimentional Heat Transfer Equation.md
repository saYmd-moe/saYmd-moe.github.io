---
title: 4th Order Runge-Kutta Methods for 2 Dimentional Heat Transfer Equation
tags:
categories:
---

在本文中，你将看到：  
使用RK4求解二维热传导方程
<!-- more -->

# 一、四阶 Runge-Kutta 方法原理

Runge-Kutta方法是微分方程数值解法中最常用的一种方法，它的基本思想是将微分方程的初值问题转化为一系列的代数方程，然后求解这些代数方程，从而得到微分方程的近似解。

假设有如下一阶微分方程初值问题：

$$
\left\{
\begin{aligned}
dy/dt &= f(t, y(t)), t_0\leq t \leq t_N \\
y(t_0) &= y_0 
\end{aligned}
\right. \tag{1-1}
$$


为了对其使用数值方法，我们首先需要将微分方程转化为差分方程，首先根据微分中值定理：
$$
y(t_{i+1})-y(t_i)=y'(\xi_i)(t_{i+1}-t_i) \tag{1-2}
$$
令步长$h=t_{i+1}-t_i$，并引入平均斜率$k=y'(\xi_i)=f(\xi_i,\ y(\xi_i))$，有：
$$
y(t_{i+1})=y(t_i)+hk \tag{1-3}
$$

在区间上取四个点，以该四点的斜率$k_1,\ k_2,\ k_3,\ k_4$的加权平均值来求平均斜率$K$的近似值：
$$
K=c_1k_1+c_2k_2+c_3k_3+c_4k_4 \tag{1-4}
$$

取$k_1为t_{i}$点处的切线斜率
$$
\begin{aligned}
k_1&=f(t_i,y_i)\\
k_2&=f(t_i+a_2h,y_i+b_2hk_1)\\
k_3&=f(t_i+a_3h,y_i+b_3hk_2)\\
k_4&=f(t_i+a_4h,y_i+b_4hk_3)
\end{aligned} \tag{1-5}
$$

将$k_1,\ k_2,\ k_3,\ k_4$在$t_i$处泰勒展开，有：
$$
\begin{aligned}
k_1&=f(t_i,y_i)\\
k_2&=f(t_i,y_i)+a_2hf'_t(t_i,y_i)+b_2hk_1f'_y(t_i,y_i)\\
&+\frac{1}{2}(a_2h)^2f''_{tt}(t_i,y_i)+a_2hb_2hk_1f''_{ty}(t_i,y_i)\\
&+\frac{1}{2}(b_2h)^2k_1^2f''_{yy}(t_i,y_i)+O(h^3)\\
k_3&=\dots\\
k_4&=\dots
\end{aligned} \tag{1-6}
$$
可将$k_1,\ k_2,\ k_3,\ k_4$代入式(1-4)中，并与$y(t_{i+1})$在$t_i$处的泰勒展开式进行系数比较：
$$
y(t_{i+1})=y(t_i)+hy'(t_i)+\frac{1}{2}h^2y''(t_i)+\frac{1}{6}h^3y'''(t_i)+O(h^4) \tag{1-7}
$$
得到关于系数的方程组，选取合适的系数，使得$K$与$y(t_{i+1})$的展开式相同，即可得到四阶Runge-Kutta方法：
$$
\left\{
\begin{aligned}
y_{i+1}&=y_i+\frac{h}{6}(k_1+2k_2+2k_3+k_4)\\
k_1&=f(t_i,y_i)\\
k_2&=f(t_i+\frac{1}{2}h,y_i+\frac{1}{2}hk_1)\\
k_3&=f(t_i+\frac{1}{2}h,y_i+\frac{1}{2}hk_2)\\
k_4&=f(t_i+h,y_i+hk_3)
\end{aligned} \right. \tag{1-8}
$$

# 二、二维热传导方程

二维热传导方程为（假设热源项为常数）：
$$
\left\{
\begin{aligned}
&u=u(x,y,t)\\
&\frac{\partial u}{\partial t}=\alpha(\frac{\partial^2 u}{\partial x^2}+\frac{\partial^2 u}{\partial y^2})+Q\\
&u(x,y,0)=v_0(x,y)\\

\end{aligned}
\right. \tag{2-1}
$$

Runge-Kutta方法只处理了时间项，我们还需要对空间项进行离散化，使用$(Max_y+1,Max_x+1)$大小的矩阵来表示$t$时刻离散空间每一点的温度值，$u_{i,j}$表示$(x_j,y_i)$处的温度值：
$$
U=
\begin{bmatrix}
u_{0,0} & u_{0,1} & \dots & u_{0,Max_x}\\
u_{1,0} & u_{1,1} & \dots & u_{1,Max_x}\\
\vdots & \vdots & \ddots & \vdots\\
u_{Max_y,0} & u_{Max_y,1} & \dots & u_{Max_y,Max_x}\\
\end{bmatrix}
$$

采用中心差分格式，每一点处的温度值满足如下差分方程：
$$
\left\{
\begin{aligned}
\frac{\partial^2u_{i,j}}{\partial x^2}&=\frac{1}{\Delta x^2}(u_{i,j+1}-2u_{i,j}+u_{i,j-1})\\
\frac{\partial^2u_{i,j}}{\partial y^2}&=\frac{1}{\Delta y^2}(u_{i+1,j}-2u_{i,j}+u_{i-1,j})
\end{aligned}
\right. \tag{2-2}
$$

使用矩阵式表达式(2-2)，可得：
$$
\begin{aligned}
\frac{\partial^2}{\partial x^2}U=\frac{1}{\Delta x^2}U \cdot A \\
\frac{\partial^2}{\partial y^2}U=\frac{1}{\Delta y^2}B \cdot U \\
\end{aligned} \tag{2-3}
$$

其中$A,\ B$分别为$Max_x+1$阶和$Max_y+1$阶的三对角对称矩阵：
$$
A=
\begin{bmatrix}
-2 & 1 & \dots & 0 & 0\\
1 & -2 & 1 & \dots & 0\\
\vdots & \ddots & \ddots & \ddots & \vdots\\
0 & 0 & \dots & 1 & -2\\
\end{bmatrix}\\
B=
\begin{bmatrix}
-2 & 1 & \dots & 0 & 0\\
1 & -2 & 1 & \dots & 0\\
\vdots & \ddots & \ddots & \ddots & \vdots\\
0 & 0 & \dots & 1 & -2\\
\end{bmatrix}
$$

于是迭代公式可以写为：
$$
\left\{
\begin{aligned}
U_{n+1}&=U_n+\frac{h}{6}(K_1+2K_2+2K_3+K_4)\\
K_1&=a(\frac{1}{\Delta x^2}U_n \cdot A+\frac{1}{\Delta y^2}B \cdot U_n)+Q\\
K_2&=a\left[\frac{1}{\Delta x^2}(U_n+\frac{h}{2}K_1) \cdot A+\frac{1}{\Delta y^2}B \cdot (U_n+\frac{h}{2}K_1)\right]+Q\\
K_3&=a\left[\frac{1}{\Delta x^2}(U_n+\frac{h}{2}K_2) \cdot A+\frac{1}{\Delta y^2}B \cdot (U_n+\frac{h}{2}K_2)\right]+Q\\
K_4&=a\left[\frac{1}{\Delta x^2}(U_n+hK_3) \cdot A+\frac{1}{\Delta y^2}B \cdot (U_n+hK_3)\right]+Q\\
\end{aligned}
\right. \tag{2-4}
$$
其中$U,\ K_1,\ K_2,\ K_3,\ K_4$均为矩阵，利用矩阵进行计算可以大大减少需要迭代的次数，优化计算速度。