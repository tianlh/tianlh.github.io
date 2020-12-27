---
layout: post
title: "线段和三角形求交"
date: 2020-12-27
author: Tianlh
header-img: "img/head-bg1.jpg"
tags:
  - 空间几何
  - 图形学
---

已知线段端点为P、Q，三角形顶点为A、B、C，求交点。

#### **直线与平面是否有交点**
当直线与平面法线垂直时无交点或在平面内。设平面法线为N，$\overrightarrow{PQ} * N = 0$时直线与平面平行。

#### **直线与平面的交点**
假设交点为M，显然平面内一点与法线垂直，有 $\overrightarrow{AM} * N = 0$
直线上一点可以表示为$M = P + t \overrightarrow{PQ}$，代入上式求得$ t = \frac{\overrightarrow{PA} * N}{\overrightarrow{PQ} * N} $，从而求得M的值。
```c++
vec3 AB = B - A;
vec3 AC = C - A;
vec3 N1 = AB.cross(AC);
vec3 PQ = Q - P;
float c1 = PQ.dot(N1);
if (c1 == 0) {
    return;
}
vec3 PA = A - P;
float c2 = PA.dot(N1);
float t = c2 / c1;
M = P + t * PQ;
```

#### **M是否在线段内**
判断是否 $0 \leq t \leq 1$，若是则在线段内，否则不在。

#### **M是否在三角形内**
- 方法一：可以判断M在 $\overrightarrow{AB}$与$\overrightarrow{AC}$，及$\overrightarrow{BA}$与$\overrightarrow{BC}$之间。令$N1 = \overrightarrow{AC} \times \overrightarrow{AM}$、$N2 = \overrightarrow{AM} \times \overrightarrow{AB}$，如果$N1 * N2 > 0$，则M在 $\overrightarrow{AB}$与$\overrightarrow{AC}$之间，同理可判断M在$\overrightarrow{BA}$与$\overrightarrow{BC}$之间。
- 方法二：M在三角形内，所以M可以表示为 $M = (1 - r1 - r2) * A + r1 * B + r2 * C$，即
\\[
{\begin{vmatrix}
X_{AB} & X_{AC} & X_{QP} \\\\ 
Y_{AB} & Y_{AC} & Y_{QP} \\\\ 
Z_{AB} & Z_{AC} & Z_{QP}
\end{vmatrix}}
{\begin{vmatrix}
r1 \\\\ 
r2 \\\\ 
t
\end{vmatrix}}
= 
\begin{vmatrix}
X_{AP}  \\\\ 
Y_{AP}  \\\\ 
Z_{AP} 
\end{vmatrix} 
\\]
根据克莱姆法则和混合积运算法则
\\[r1 = \frac{(AP, AC, QP)}{(AB, AC, QP)} = \frac{AC * (QP \times AP)}{QP * (AB \times AC)}\\]
\\[r2 = \frac{(AB, AP, QP)}{(AB, AC, QP)} = \frac{AB * (AP \times QP)}{QP * (AB \times AC)} =  \frac{-AB * (QP \times AP)}{QP * (AB \times AC)} \\]
```c++
float N2 = (PQ).cross(PA);
float r11 = AC.dot(N2);
float r12 = (-PQ).dot(N1);
float r21 = (-AB).dot(N2);
if (r12 == 0) {
    return false;
}
float c3 = r11 / r12;
if (c3 < 0 || c3 > 1) {
    return false;
}
float c4 = r21 / r12;
if (c4 < 0 || c4 > 1) {
    return false;
}
```

#### **克莱姆法则**
线性方程组可以用矩阵A与向量x的方程$Ax=B$来表示。如果A是可逆矩阵，那么方程有解：
\\[xi = \frac{Ai}{A}\\]
其中Ai是被列向量B取代了A的第i列的列向量后得到的矩阵。

#### **混合积运算法则**
- [混合积运算法则](https://baike.baidu.com/item/%E6%B7%B7%E5%90%88%E7%A7%AF/10564182?fr=aladdin)
