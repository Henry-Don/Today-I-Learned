# 三角函数与复数

## 1. 从单位圆到正弦和余弦

单位圆上的点可由角度 $\theta$ 表示为：

$$
(x,y)=(\cos\theta,\sin\theta)
$$

因此，正弦和余弦可以理解为同一个旋转点在两条正交轴上的投影。标准正弦量写作：

$$
x(t)=A\cos(\omega t+\phi)
$$

其中：

- $A$：峰值或幅值
- $\omega$：角频率，单位 rad/s
- $\phi$：初相位
- $f$：频率，单位 Hz
- $T$：周期

这些量满足：

$$
\omega=2\pi f,
\qquad
T=\frac{1}{f}=\frac{2\pi}{\omega}
$$

正弦与余弦只是相差 $90^\circ$ 的同一种周期函数：

$$
\sin\theta=\cos\left(\theta-\frac{\pi}{2}\right)
$$

$$
\cos\theta=\sin\left(\theta+\frac{\pi}{2}\right)
$$

工程上选用正弦还是余弦主要是约定；相量分析通常采用余弦参考。

## 2. 相位与相位差

对于两个同频信号：

$$
x_1(t)=A_1\cos(\omega t+\phi_1)
$$

$$
x_2(t)=A_2\cos(\omega t+\phi_2)
$$

定义 $x_2$ 相对 $x_1$ 的相位差为：

$$
\Delta\phi=\phi_2-\phi_1
$$

若 $\Delta\phi>0$，则 $x_2$ 相对 $x_1$ 超前；若 $\Delta\phi<0$，则 $x_2$ 滞后。因为：

$$
A\cos(\omega t+\phi)
=A\cos\left[\omega\left(t+\frac{\phi}{\omega}\right)\right]
$$

正相位对应波形在时间上的提前。

相位是圆周上的量，只在模 $2\pi$ 的意义下唯一：

$$
\theta\equiv\theta+2k\pi
$$

例如 $30^\circ$、$390^\circ$ 和 $-330^\circ$ 表示同一方向。

## 3. 复平面是二维坐标加旋转代数

定义虚数单位：

$$
j^2=-1
$$

一般复数写作：

$$
z=a+jb
$$

其中 $a=\Re(z)$，$b=\Im(z)$。在几何上，复数 $z$ 就是二维笛卡尔平面上的点 $(a,b)$。真正新增的不是另一个神秘空间，而是一套特别适合表达二维缩放和旋转的乘法结构。

二维旋转矩阵为：

$$
\begin{bmatrix}
a'\\
b'
\end{bmatrix}
=
\begin{bmatrix}
\cos\theta & -\sin\theta\\
\sin\theta & \cos\theta
\end{bmatrix}
\begin{bmatrix}
a\\
b
\end{bmatrix}
$$

同一个操作用复数可以写成：

$$
z'=e^{j\theta}z
$$

## 4. 欧拉公式

欧拉公式为：

$$
e^{j\theta}=\cos\theta+j\sin\theta
$$

将 $j\theta$ 代入指数函数的 Taylor 级数：

$$
e^{j\theta}
=1+j\theta-rac{\theta^2}{2!}
-j\frac{\theta^3}{3!}
+\frac{\theta^4}{4!}+\cdots
$$

把实部和虚部分组，分别得到余弦与正弦的级数，所以 $e^{j\theta}$ 表示单位圆上的旋转。

特别地：

$$
j=e^{j\pi/2}
$$

因此乘以 $j$ 表示逆时针旋转 $90^\circ$。

## 5. 幅值、相角与 atan2

对复数 $z=a+jb$，幅值为：

$$
|z|=\sqrt{a^2+b^2}
$$

相角为：

$$
\theta=\arg(z)
$$

不能简单使用 $\theta=\arctan(b/a)$，因为这样会丢失象限信息，并且在 $a=0$ 时失效。工程实现通常使用：

$$
\theta=\mathrm{atan2}(b,a)
$$

常见函数参数顺序是 $\mathrm{atan2}(y,x)$。对于复数，应代入 $y=b$ 和 $x=a$。

典型例子：

- $(1,1)\rightarrow45^\circ$
- $(-1,1)\rightarrow135^\circ$
- $(-1,-1)\rightarrow-135^\circ$
- $(1,-1)\rightarrow-45^\circ$

## 6. 复数乘法表示连续缩放与旋转

复数的极坐标形式为：

$$
z=re^{j\theta}=r\angle\theta
$$

若：

$$
z_1=r_1e^{j\theta_1},
\qquad
z_2=r_2e^{j\theta_2}
$$

则：

$$
z_1z_2=r_1r_2e^{j(\theta_1+\theta_2)}
$$

所以复数相乘时，幅值相乘、相角相加。它不是“角度相乘”，而是连续执行两次缩放与旋转。

## 7. 为什么一个瞬时实数不足以描述振荡

只知道某一瞬间 $x=0.5$，无法判断它对应单位圆上的 $+60^\circ$ 还是 $-60^\circ$，也不能据此判断下一瞬间上升还是下降。一个瞬时实数值不足以唯一确定完整振荡状态。

任意固定频率的实正弦可以写成：

$$
x(t)=a\cos\omega t+b\sin\omega t
$$

完整描述需要两个独立实数 $(a,b)$。同时，微分会在两个基函数之间转换：

$$
\frac{d}{dt}\cos\omega t=-\omega\sin\omega t
$$

$$
\frac{d}{dt}\sin\omega t=\omega\cos\omega t
$$

$\cos\omega t$ 和 $\sin\omega t$ 张成的二维实空间对微分封闭。一个复自由度恰好统一表示两个实自由度：

$$
2\text{ real degrees of freedom}
\longleftrightarrow
1\text{ complex degree of freedom}
$$

对理想单频正弦 $x(t)=A\cos(\omega t+\phi)$，定义正交分量：

$$
y(t)=A\sin(\omega t+\phi)
$$

因为：

$$
\dot{x}(t)=-A\omega\sin(\omega t+\phi)
$$

所以：

$$
y(t)=-\frac{1}{\omega}\dot{x}(t)
$$

完整旋转状态可写为：

$$
z(t)=x(t)+jy(t)
=x(t)-j\frac{\dot{x}(t)}{\omega}
$$

第二维不是另一股神秘物理量，而是补足相位和变化趋势所需的正交状态。

## 8. 为什么可以取实部或虚部

构造旋转量：

$$
z(t)=Ae^{j(\omega t+\phi)}
$$

采用余弦参考时：

$$
x(t)=\Re\{z(t)\}
$$

这表示从完整数学状态中读取选定的物理投影，并不表示完整复数信息“藏在实部里”。映射 $\Re:\mathbb{C}\to\mathbb{R}$ 是多对一的，单独取实部会丢失信息。

如果从正弦参考出发，也可以写成：

$$
x(t)=\Im\{Ae^{j(\omega t+\phi)}\}
$$

选择实部还是虚部是表示约定，不是物理定律。

## 9. Wrapped 与 unwrapped phase

常见相角主值区间包括 $(-\pi,\pi]$ 和 $[0,2\pi)$。将角度限制在某个固定区间称为 wrapped phase。

在 Bode 图、阻抗扫描等频率响应分析中，为了保持相位随频率连续，可以继续记录 $-190^\circ$、$-230^\circ$ 等超出主值区间的角度，这称为 unwrapped phase。

## 10. 常见误区

1. **复平面是普通二维坐标无法解决问题时才出现的。** 复平面在几何上仍是二维笛卡尔平面；关键是复数乘法提供了自然的旋转结构。
2. **复数乘法是角度相乘。** 正确规律是幅值相乘、相角相加。
3. **整个复数的信息都被压进了实部。** 实部只是投影，单独取实部会丢失方向和相位信息。
4. **虚部一定代表另一股真实物理量。** 虚部首先是正交坐标分量，其具体物理解释取决于模型和参考系。
5. **相角必须固定在某一个范围。** 主值区间只是表示约定，连续频率响应常使用展开相位。

## 11. 下一步接口

- [正弦稳态与相量](../electrical-engineering/sinusoidal-steady-state-and-phasors.md)
- [复功率与 BESS PCS 的 P-Q 能力](../electrical-engineering/complex-power.md)
- 后续：从二维旋转矩阵严格推导复数乘法
- 后续：解释 $e^{j\omega t}$ 为什么是微分算子的特征函数
- 后续：推导 Clarke/Park 变换与复数旋转的等价关系
