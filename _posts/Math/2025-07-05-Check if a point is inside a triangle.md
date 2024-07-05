---
title: Check if a point is inside a triangle 三角形内部の点の判別 
date: 2024-07-04 22:03 +0900
categories: [Math]
tags: [Math, Image Processing, C, JP]
author: Youkoutaku
math: true
img_path: 
image:
  path: /src/math/triangle/2025-07-05-triangle.png
  alt: 
---

## 三角形の線の方程式
まず、与えられた三角形の各頂点 $(x_1, y_1)$, $(x_2,y_2)$,$(y_1,y_2)$ を通る直線の方程式を求めます。

1. 頂点 $(x_1, y_1)$ から $(x_2, y_2)$ への直線の方程式：
    
    $$(y_2 - y_1)(x-x_1) - (x_2 - x_1)(y-y_1)=0$$
    
2. 頂点 $(x_2, y_2)$ から $(x_3, y_3)$ への直線の方程式：
    
    $$(y_3 - y_2)(x-x_2) - (x_3 - x_2)(y-y_2)=0$$
    
3. 頂点 $(x_3, y_3)$ から $(x_1, y_1)$ への直線の方程式：
    
    $$(y_1 - y_3)(x-x_3) - (x_1 - x_3)(y-y_3) =0$$

これらの方程式は，ベクトルの外積の意味があります。

## 外積による位置判別
ベクトル：

- $a=(x_2 - x_1, y_2 - y_1)$

- $b=(x_3 - x_2, y_3 - y_2)$

- $c=(x_1 - x_3, y_1 - y_3)$

- $p=(x - x_1, y - y_1)$

- $q=(x - x_2, y - y_2)$

- $h=(x - x_3, y - y_3)$

外積の式：

1. 
	
	$$L_1=p \times a =(y_2 - y_1)(x-x_1) - (x_2 - x_1)(y-y_1)$$
    
2. 
    
    $$L_2=q \times b =(y_3 - y_2)(x-x_2) - (x_3 - x_2)(y-y_2)$$
    
3. 
    
    $$L_3=h \times c=(y_1 - y_3)(x-x_3) - (x_1 - x_3)(y-y_3)$$
    

外積の性質により，ベクトルの位置を判別できます。

![](/src/math/triangle/2025-07-05-triangle3.png)

点$(x,y)$が三角形内部にある時，三つの外積の符号が同じです。

![](/src/math/triangle/2025-07-05-triangle.png)

点$(x,y)$が三角形内部にある時，三つの外積の符号が同じではありません。

![](/src/math/triangle/2025-07-05-triangle2.png)

---

外積の式をそれぞれ整理した一般化の式：

- $L_1 = (y_2 - y_1)x + (x_1 - x_2)y + (x_2 y_1 - x_1 y_2)$
- $L_2 = (y_3 - y_2)x + (x_2 - x_3)y + (x_3 y_2 - x_2 y_3)$
- $L_3 = (y_1 - y_3)x + (x_3 - x_1)y + (x_1 y_3 - x_3 y_1)$

これらの値 $L_1, L_2, L_3$ の符号が全て正または全て負であれば、点 $(x,y)$ は三角形の内部にあります。一方、符号が混在している場合、点は三角形の外部にあります。

---

## Code
```c
int isInsideTriangle(int x, int y, int x1, int y1, int x2, int y2, int x3, int y3)
{
    // Calculate cross product
    float L1 = (y2 - y1) * x + (x1 - x2) * y + (x2 * y1 - x1 * y2);
    float L2 = (y3 - y2) * x + (x2 - x3) * y + (x3 * y2 - x2 * y3);
    float L3 = (y1 - y3) * x + (x3 - x1) * y + (x1 * y3 - x3 * y1);

    // Check if the point lies on the same side of all lines
    return (L1 >= 0 && L2 >= 0 && L3 >= 0) || (L1 <= 0 && L2 <= 0 && L3 <= 0);

}
```
