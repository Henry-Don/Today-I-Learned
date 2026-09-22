# Laplace 变换与传递函数

## 1. 从微分方程到 s 域

动态系统通常由微分方程描述。例如：

$$
L\frac{di}{dt}+Ri=u(t)
$$

时域方程保留了最直接的物理因果关系，但求解、组合和控制设计可能较繁琐。Laplace 变换的核心工程价值，是把微分运算转化为关于复变量 $s$ 的代数运算：

$$
\text{differential equation}
\longrightarrow
\text{algebraic equation in }s
$$

这为传递函数、极点零点、频率响应和控制器设计建立了统一语言。

## 2. Laplace 变换定义

单边 Laplace 变换定义为：

$$
F(s)
=
\mathcal{L}\{f(t)\}
=
\int_0^\infty f(t)e^{-st}\,dt
$$

其中：

$$
s=\sigma+j\omega
$$

$s$ 不是时间变量的简单替换，而是一个复频率变量。积分中的 $e^{-st}$ 用于检验并提取信号中不同增长、衰减和振荡成分。

Laplace 变换并非对任意函数和任意 $s$ 都收敛。当前阶段重点使用常见工程信号和有理传递函数，不展开完整的收敛域理论。

## 3. 导数性质与初始条件

一阶导数的变换为：

$$
\mathcal{L}
\left\{
\frac{df}{dt}
\right\}
=
sF(s)-f(0^-)
$$

二阶导数为：

$$
\mathcal{L}
\left\{
\frac{d^2f}{dt^2}
\right\}
=
s^2F(s)-sf(0^-)-\dot{f}(0^-)
$$

只有在零初始条件下，才可简化为：

$$
\frac{d}{dt}\longleftrightarrow s
$$

$$
\frac{d^2}{dt^2}\longleftrightarrow s^2
$$

因此，“Laplace 把导数变成乘以 $s$”是有条件的工程简写，不能遗漏初始条件项。

## 4. 为什么 e 的指数必须包含时间

表达式：

$$
e^3
$$

只是一个常数，约为 $20.085$。真正表示随时间增长的是：

$$
e^{3t}
$$

因为当 $t$ 增加时，指数 $3t$ 持续增加。

同理：

$$
e^{-3t}
$$

随时间指数衰减。增长或衰减来自指数中包含时间变量，而不是来自数字 $e$ 本身。

## 5. 复频率 s 的动态含义

最核心的统一表达为：

$$
e^{st}
=
e^{(\sigma+j\omega)t}
=
e^{\sigma t}e^{j\omega t}
$$

其中：

- $\sigma=\Re(s)$ 决定幅值随时间增长或衰减；
- $\omega=\Im(s)$ 决定旋转或振荡的角速度。

因此：

| 条件 | 动态含义 |
| --- | --- |
| $\sigma<0$ | 幅值指数衰减 |
| $\sigma=0$ | 幅值保持不变 |
| $\sigma>0$ | 幅值指数增长 |
| $\omega=0$ | 不产生振荡 |
| $\omega\neq0$ | 产生旋转或振荡 |

## 6. 为什么复指数表示旋转

由欧拉公式：

$$
e^{j\omega t}
=
\cos\omega t+j\sin\omega t
$$

其模为：

$$
\left|e^{j\omega t}\right|=1
$$

相角为：

$$
\angle e^{j\omega t}=\omega t
$$

所以它在复平面中沿单位圆匀速旋转。实部和虚部分别是余弦与正弦投影，因此固定半径的持续旋转对应持续振荡。

更一般地：

$$
e^{(\sigma+j\omega)t}
=
e^{\sigma t}e^{j\omega t}
$$

可理解为：

- $e^{\sigma t}$ 控制旋转半径；
- $e^{j\omega t}$ 控制旋转角度。

于是 $\sigma<0$ 对应向原点螺旋收缩，$\sigma=0$ 对应固定圆周旋转，$\sigma>0$ 对应向外螺旋扩张。

## 7. s 平面直觉

s 平面的横轴为：

$$
\Re(s)=\sigma
$$

纵轴为：

$$
\Im(s)=\omega
$$

例如：

$$
s=-3+j10
$$

对应模态：

$$
e^{-3t}e^{j10t}
$$

它以 $10\ \mathrm{rad/s}$ 的角速度振荡，同时振幅包络按 $e^{-3t}$ 衰减。

连续时间系统中，左半平面与衰减、右半平面与增长、虚部与振荡频率的联系，正是以后读取极点位置的基础。

## 8. 与相量表达的关系

固定频率正弦稳态可写成：

$$
Ae^{j\phi}e^{j\omega t}
$$

其中：

$$
Ae^{j\phi}
$$

是固定复幅值，保存幅值 $A$ 和初相位 $\phi$；而：

$$
e^{j\omega t}
$$

是所有同频稳态量共享的时间旋转因子。

因此，固定的是复幅值 $Ae^{j\phi}$，而不是“实部固定”。相量法提取公共旋转后，保留的正是这个固定复幅值。

Laplace 表达进一步允许：

$$
Ae^{j\phi}e^{(\sigma+j\omega)t}
$$

其实部为：

$$
x(t)
=
Ae^{\sigma t}\cos(\omega t+\phi)
$$

四个参数分别表示：

- $A$：初始幅值尺度；
- $\phi$：初始相位；
- $\sigma$：增长或衰减率；
- $\omega$：振荡角频率。

相量主要描述 $\sigma=0$ 的固定频率正弦稳态，而 Laplace 语言可以同时表达暂态包络和振荡。

## 9. 为什么线性系统自然产生 e 的 st 次方

设：

$$
x(t)=e^{st}
$$

则：

$$
\frac{dx}{dt}=se^{st}
$$

$$
\frac{d^2x}{dt^2}=s^2e^{st}
$$

求导不会改变函数形状，只会多乘一个 $s$ 或 $s^2$。因此，把 $e^{st}$ 代入线性常系数微分方程后，可以把微分方程转化为关于 $s$ 的代数方程。

这就是指数模态、特征方程、极点和自然响应之间联系的数学根源。

## 10. 从 RL 动态推导传递函数

考虑：

$$
L\frac{di}{dt}+Ri=u(t)
$$

进行 Laplace 变换：

$$
L[sI(s)-i(0^-)]
+RI(s)
=
U(s)
$$

定义传递函数时采用零初始条件：

$$
i(0^-)=0
$$

因此：

$$
(Ls+R)I(s)=U(s)
$$

得到：

$$
G(s)
=
\frac{I(s)}{U(s)}
=
\frac{1}{Ls+R}
$$

这个表达式把输入电压到输出电流的动态映射压缩成了一个有理函数。

## 11. 传递函数定义与边界

对线性时不变系统，在零初始条件下定义：

$$
G(s)=\frac{Y(s)}{U(s)}
$$

传递函数描述输入到输出的动态关系，但它不是完整内部状态描述。

同一个内部系统从不同输入到不同输出，可能得到不同传递函数。某些不可控或不可观的内部模态也可能不出现在特定输入输出通道中。

因此：

$$
\text{transfer function}
\neq
\text{完整内部模型}
$$

它最适合描述明确输入输出通道下的 LTI 动态。

## 12. 标准一阶形式

RL plant：

$$
G(s)=\frac{1}{Ls+R}
$$

提取 $R$：

$$
G(s)
=
\frac{1/R}{1+(L/R)s}
$$

定义：

$$
\tau=\frac{L}{R},
\qquad
K=\frac{1}{R}
$$

得到标准一阶形式：

$$
G(s)=\frac{K}{\tau s+1}
$$

其中 $K$ 是 DC 增益，$\tau$ 是时间常数。

## 13. DC 增益

令 $s=0$：

$$
G(0)=K
$$

对 RL plant：

$$
G(0)=\frac{1}{R}
$$

DC 增益描述零频率或常值输入下的稳态输入输出比例。它不直接说明响应有多快；速度由极点和时间常数决定。

## 14. 传递函数与状态空间

状态空间模型为：

$$
\dot{\mathbf{x}}
=
A\mathbf{x}+B\mathbf{u}
$$

$$
\mathbf{y}
=
C\mathbf{x}+D\mathbf{u}
$$

在零初始条件下：

$$
s\mathbf{X}
=
A\mathbf{X}+B\mathbf{U}
$$

所以：

$$
\mathbf{X}
=(sI-A)^{-1}B\mathbf{U}
$$

代入输出方程：

$$
\mathbf{Y}
=
\left[
C(sI-A)^{-1}B+D
\right]\mathbf{U}
$$

因此：

$$
G(s)
=
C(sI-A)^{-1}B+D
$$

传递函数和状态空间不是两套互不相关的理论，而是同一个 LTI 动态系统的不同表示：

- 传递函数突出输入输出与频域特性；
- 状态空间保留内部状态，适合 MIMO、耦合和模态分析。

## 15. Laplace 与频率响应

传递函数中的 $s$ 是一般复频率：

$$
s=\sigma+j\omega
$$

当系统稳定，并关注正弦稳态响应时，沿虚轴取：

$$
s=j\omega
$$

得到：

$$
G(j\omega)
$$

它描述不同频率正弦输入的稳态幅值缩放和相位偏移。

因此，频率响应是传递函数在虚轴上的取值，而不是整个 Laplace 域。

## 16. 常见误区

1. **Laplace 变换就是把 $t$ 换成 $s$。** 它是积分变换，不是变量替换。
2. **导数永远可以直接换成 $s$。** 严格表达还包含初始条件项。
3. **$e^3$ 表示指数增长。** $e^3$ 是常数，$e^{3t}$ 才随时间增长。
4. **$e^{j\omega t}$ 的幅值不断变化。** 它的模恒为 1，变化的是相角。
5. **此前相量中固定的是实部。** 固定的是复幅值 $Ae^{j\phi}$。
6. **传递函数包含系统全部内部信息。** 它是零初始条件下特定输入输出通道的描述。
7. **令 $s=j\omega$ 就等于完整 Laplace 分析。** 这只是在虚轴上考察频率响应。
8. **DC 增益越大就表示响应越快。** 稳态增益与动态速度是不同属性。

## 17. 相关笔记

- [三角函数与复数](trigonometry-and-complex-numbers.md)
- [微分方程与动态系统基础](differential-equations-and-dynamic-systems.md)
- [矩阵与坐标变换](matrices-and-coordinate-transformations.md)
- [正弦稳态与相量](../electrical-engineering/sinusoidal-steady-state-and-phasors.md)
- [极点、零点、带宽与稳态误差](../control/poles-zeros-bandwidth-and-steady-state-error.md)
- [并网变流器电流动态](../../power-electronics/converter-current-dynamics.md)
