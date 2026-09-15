# 矩阵与坐标变换

## 1. 为什么变流器控制需要矩阵

并网变流器中常见的坐标链为：

$$
abc
\xrightarrow{\mathrm{Clarke}}
\alpha\beta
\xrightarrow{\mathrm{Park}}
dq
$$

要理解这条链，首先需要把矩阵看成线性变换，而不只是数字表格。矩阵乘向量表示按既定规则重新组合输入坐标；矩阵相乘表示把多个变换合并；逆矩阵表示撤销一个可逆变换。

本笔记只建立理解 Clarke/Park、PLL 和 dq 控制所需的矩阵基础，不展开一般线性空间、Jordan 标准形或张量理论。

## 2. 向量是对象在坐标系中的表示

写成：

$$
\mathbf{x}=
\begin{bmatrix}
3\\
4
\end{bmatrix}
$$

更准确的含义是：某个对象在当前两条坐标轴上的分量分别为 3 和 4。向量的物理对象与它的坐标表示需要区分。

同一个电压空间向量可以写成：

$$
\begin{bmatrix}
v_\alpha\\
v_\beta
\end{bmatrix}
\quad\text{或}\quad
\begin{bmatrix}
v_d\\
v_q
\end{bmatrix}
$$

二者通常不是两组不同的物理电压，而是同一个对象在静止坐标系和旋转坐标系中的不同坐标。

## 3. 矩阵乘向量

设：

$$
A=
\begin{bmatrix}
2&3\\
4&5
\end{bmatrix},
\qquad
\mathbf{x}=
\begin{bmatrix}
1\\
2
\end{bmatrix}
$$

则：

$$
A\mathbf{x}=
\begin{bmatrix}
2\times1+3\times2\\
4\times1+5\times2
\end{bmatrix}
=
\begin{bmatrix}
8\\
14
\end{bmatrix}
$$

“行乘列”是计算规则。更重要的解释是，每一行都定义一个新的输出坐标：

$$
y_1=2x_1+3x_2,
\qquad
y_2=4x_1+5x_2
$$

因此：

$$
A\mathbf{x}=\mathbf{y}
$$

表示按照矩阵 $A$ 的规则把输入坐标重新组合成输出坐标。

## 4. 矩阵尺寸说明输入和输出维数

若：

$$
A\in\mathbb{R}^{m\times n},
\qquad
\mathbf{x}\in\mathbb{R}^{n\times1}
$$

则：

$$
A\mathbf{x}\in\mathbb{R}^{m\times1}
$$

也就是：

$$
(m\times n)(n\times1)=m\times1
$$

其中，$n$ 列表示矩阵需要 $n$ 个输入坐标，$m$ 行表示矩阵产生 $m$ 个输出坐标。例如二维 Clarke 矩阵是 $2\times3$，它接收三个相坐标并输出两个平面坐标。

尺寸检查也是工程推导中最直接的错误检测方法之一。若相邻矩阵的内侧维数不同，两个变换就不能按当前顺序复合。

## 5. 矩阵相乘表示连续变换

若：

$$
\mathbf{y}=A\mathbf{x}
$$

又有：

$$
\mathbf{z}=B\mathbf{y}
$$

则：

$$
\mathbf{z}=B(A\mathbf{x})=(BA)\mathbf{x}
$$

所以矩阵乘法可以把连续执行的线性变换合并成一个变换。在：

$$
\mathbf{v}_{\alpha\beta}=C\mathbf{v}_{abc}
$$

$$
\mathbf{v}_{dq}=P\mathbf{v}_{\alpha\beta}
$$

中，合并结果为：

$$
\mathbf{v}_{dq}=PC\mathbf{v}_{abc}
$$

变换顺序从右向左读：先执行 $C$，再执行 $P$。

## 6. 为什么顺序不能交换

矩阵乘法一般不满足交换律：

$$
AB\neq BA
$$

顺序不同表示执行过程不同，结果通常也不同。在 Clarke/Park 链中，$C$ 把三维相坐标映射到二维平面，$P$ 再在二维平面内旋转坐标。除了物理意义不同，$C\in\mathbb{R}^{2\times3}$ 与 $P\in\mathbb{R}^{2\times2}$ 的尺寸也决定了 $PC$ 有定义，而 $CP$ 没有定义。

## 7. 单位矩阵与逆矩阵

二维单位矩阵为：

$$
I=
\begin{bmatrix}
1&0\\
0&1
\end{bmatrix}
$$

它满足：

$$
I\mathbf{x}=\mathbf{x}
$$

因此单位矩阵表示什么都不改变的变换。

若 $A$ 可逆，则存在 $A^{-1}$ 使：

$$
A^{-1}A=AA^{-1}=I
$$

若：

$$
\mathbf{y}=A\mathbf{x}
$$

则可通过：

$$
\mathbf{x}=A^{-1}\mathbf{y}
$$

恢复原坐标。逆矩阵的核心直觉就是撤销 $A$ 所做的变换。

例如：

$$
A=
\begin{bmatrix}
2&0\\
0&3
\end{bmatrix},
\qquad
A^{-1}=
\begin{bmatrix}
1/2&0\\
0&1/3
\end{bmatrix}
$$

先沿两个方向分别放大，再按相反比例缩小，就恢复原坐标。

## 8. 什么时候不存在逆矩阵

若两个不同输入满足：

$$
\mathbf{x}_1\neq\mathbf{x}_2,
\qquad
A\mathbf{x}_1=A\mathbf{x}_2
$$

则输出无法判断原来来自哪个输入，说明变换丢失了不可恢复的信息，因此不存在唯一逆变换。

例如：

$$
\begin{bmatrix}
1&0\\
0&0
\end{bmatrix}
\begin{bmatrix}
x\\
y
\end{bmatrix}
=
\begin{bmatrix}
x\\
0
\end{bmatrix}
$$

原来的 $y$ 被完全抹去，不能由输出唯一恢复。

对于二阶方阵：

$$
A=
\begin{bmatrix}
a&b\\
c&d
\end{bmatrix}
$$

若 $ad-bc\neq0$，则：

$$
A^{-1}=
\frac{1}{ad-bc}
\begin{bmatrix}
d&-b\\
-c&a
\end{bmatrix}
$$

本阶段只需掌握：

$$
\det(A)\neq0
\Longleftrightarrow
A\text{ 可逆}
$$

## 9. 坐标变换不改变物理对象

设同一个物理向量为 $\mathbf{v}$，它在旧、新坐标系中的表示分别为 $[\mathbf{v}]_{\mathrm{old}}$ 和 $[\mathbf{v}]_{\mathrm{new}}$。可以定义：

$$
[\mathbf{v}]_{\mathrm{new}}
=T[\mathbf{v}]_{\mathrm{old}}
$$

若 $T$ 可逆，则：

$$
[\mathbf{v}]_{\mathrm{old}}
=T^{-1}[\mathbf{v}]_{\mathrm{new}}
$$

两个坐标列向量可以不同，但物理对象 $\mathbf{v}$ 没有改变。具体矩阵是 $T$ 还是 $T^{-1}$，取决于如何定义新旧基底和变换方向，因此引用公式时必须同时确认约定。

## 10. 主动旋转与被动坐标变换

二维主动逆时针旋转矩阵为：

$$
R(\theta)=
\begin{bmatrix}
\cos\theta&-\sin\theta\\
\sin\theta&\cos\theta
\end{bmatrix}
$$

### 10.1 主动旋转

坐标轴固定，让向量本身逆时针旋转 $\theta$：

$$
\mathbf{v}'=R(\theta)\mathbf{v}
$$

### 10.2 被动坐标变换

物理向量不动，让坐标轴逆时针旋转 $\theta$。同一个向量在新坐标系中的坐标为：

$$
[\mathbf{v}]_{\mathrm{new}}
=R(-\theta)[\mathbf{v}]_{\mathrm{old}}
$$

坐标轴逆时针转动后，从新坐标系观察，原向量的坐标就像顺时针转动。常用 Park 变换可以按这种被动坐标变换理解。

## 11. 旋转矩阵的逆与转置

纯旋转不改变向量长度：

$$
\|R(\theta)\mathbf{v}\|=\|\mathbf{v}\|
$$

反方向旋转可以撤销原旋转：

$$
R^{-1}(\theta)=R(-\theta)
$$

旋转矩阵还满足：

$$
R^{-1}(\theta)=R^T(\theta)
$$

这种矩阵称为正交矩阵。

## 12. 时变坐标变换与额外动态项

若坐标变换本身随时间变化：

$$
\mathbf{x}'=T(t)\mathbf{x}
$$

求导必须使用乘积法则：

$$
\dot{\mathbf{x}}'
=\dot{T}\mathbf{x}+T\dot{\mathbf{x}}
$$

不能省略 $\dot{T}\mathbf{x}$。Park 矩阵含有 $\sin\theta(t)$ 和 $\cos\theta(t)$，所以它是时变矩阵。dq 动态方程中的 $\omega$ 交叉耦合项，正是旋转坐标系自身运动留下的结果之一；具体正负号取决于 Park 和 q 轴约定。

## 13. 常见误区

1. **矩阵只是数字表格。** 更有用的理解是，矩阵是线性变换在所选坐标中的表示。
2. **矩阵乘法只有行乘列这一层意义。** 行乘列是算法，本质是输出坐标的重新组合和变换的复合。
3. **矩阵相乘的顺序可以交换。** 一般 $AB\neq BA$，而且不同尺寸还可能使反向乘法没有定义。
4. **坐标数值改变代表物理对象改变。** 坐标变换通常只改变描述方式。
5. **非方阵也一定有普通逆矩阵。** 普通双侧逆只适用于满足条件的方阵；受约束子空间上的恢复需要单独说明。
6. **主动旋转和旋转坐标轴可以使用同一个符号直接代入。** 两者的角度符号相反，必须先说明变换含义。
7. **时变变换后的导数只需乘原矩阵。** 还必须包含 $\dot{T}\mathbf{x}$。

## 14. 工程检查清单

使用坐标变换公式前，至少确认：

- 输入、输出向量的排列顺序和矩阵尺寸；
- 变换表示主动旋转还是被动坐标变换；
- 角度正方向、相序和 q 轴方向；
- 正向与逆向变换能否在声明的条件下互相恢复；
- 变换是否随时间变化，以及求导时是否包含额外项；
- 归一化是否保持幅值、功率或其他内积。

## 15. 相关笔记

- [三角函数与复数](trigonometry-and-complex-numbers.md)
- [Clarke 与 Park 坐标变换](../electrical-engineering/clarke-and-park-transformations.md)
- [正弦稳态与相量](../electrical-engineering/sinusoidal-steady-state-and-phasors.md)
