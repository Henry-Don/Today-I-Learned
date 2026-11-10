# 采样、混叠与精确 ZOH 离散系统

采样规定观察与控制的节拍；离散化建立采样时刻的数学递推。连续 plant 在两个采样时刻之间仍然演化。

## 1. 采样与混叠

### 1.1 采样频率与闭环带宽

均匀理想采样 $x[k]=x(kT_s)$，$f_s=1/T_s$。$T_s=100\ \mu\mathrm s$ 对应 10 kHz，不代表电流环能达到 10 kHz 带宽；带宽还受到 plant、保持、反馈、执行能力和延迟限制。

电流采样要区分连续电流 $i(t)$、理想取值 $i[k]=i(kT_s)$ 和经传感器、ADC 及标定后的测量值 $i_m[k]$。例如某时刻真实电流为 10.000 A，理想样本也是 10.000 A，实际测量可以报 10.012 A。连续电流在两个采样点之间仍按电路方程演化。 [ADC 与软件标定](../electrical-engineering/measurement-noise-quantization-and-jitter.md#23-从模拟信号adc-编码到软件标定)

### 1.2 不同连续频率产生同一序列

采样率为 1000 Hz 时：

$$
\cos(2\pi1100k/1000)
=\cos(2\pi100k/1000+2\pi k)
=\cos(2\pi100k/1000).
$$

因此 100 Hz 与 1100 Hz 无法仅靠该样本序列区分。对实余弦，还存在正负频率折叠；实际混叠频率需折回 $[0,f_s/2]$。

无损 LCL 的约 1949.24 Hz 模态若被激发、被传感器观察到，且没有充分抗混叠滤波，在 2000 Hz 采样下可表现为 $|2000-1949.24|=50.76$ Hz。这是样本中的表象，物理谐振没有变成 50.76 Hz。

### 1.3 Nyquist 条件及其限制

理想严格带限信号在适当重建条件下需 $f_s>2f_{\max}$，Nyquist 频率为 $f_s/2$。该条件避免经典基带频谱重叠，不保证反馈裕度或硬件追踪能力。抗混叠滤波必须作用在采样前，采样后不能一般地恢复已混在一起的频率。

## 2. 精确零阶保持离散化

### 2.1 从一个周期内的连续解出发

**零阶保持的输入。** 设 $T_s=1\ \mathrm{ms}$，执行端在周期起点依次采用 2、4、3 V 的命令。

| 连续时间区间 | 等效执行电压 |
| --- | --- |
| $[0,1)\ \mathrm{ms}$ | 2 V |
| $[1,2)\ \mathrm{ms}$ | 4 V |
| $[2,3)\ \mathrm{ms}$ | 3 V |

“零阶”指每段用零次多项式表示输入。即使电压保持恒定，RL 电流仍沿连续方程演化。若求解器取 $h=0.1\ \mathrm{ms}$，一个控制周期可含十个数值步。采样规定读取时刻，控制时序规定命令何时生效，保持器规定两次更新之间的输入形状。执行端若在其他时刻载入命令，应按实际时序划分保持区间。

**积分因子推导。** 由矩阵指数求导及 $A$ 与其指数可交换，可得

$$
\frac{d}{dt}\bigl(e^{-At}x(t)\bigr)
=e^{-At}(\dot x-Ax)=e^{-At}Bu(t).
$$

记 $t_k=kT_s$。在 $t_k$ 到 $t_{k+1}$ 积分后左乘 $e^{At_{k+1}}$，得到

$$
x(t_{k+1})=e^{AT_s}x(t_k)
+\int_{t_k}^{t_{k+1}}e^{A(t_{k+1}-s)}Bu(s)\,ds.
$$

在 ZOH 假设下可把 $u[k]$ 提出积分。换元后得到采样点的状态递推。

对定常 $\dot x=Ax+Bu$，令输入在 $kT_s\le t<(k+1)T_s$ 保持为 $u[k]$，由连续完整解得到：

$$
x[k+1]=e^{AT_s}x[k]
+\int_0^{T_s}e^{A(T_s-\tau)}B\,d\tau\,u[k]
=\Phi x[k]+\Gamma u[k],
$$

$$
\Phi=e^{AT_s},\qquad \Gamma=\int_0^{T_s}e^{A\tau}B\,d\tau.
$$

$\Phi$ 为 $n\times n$、$\Gamma$ 为 $n\times m$。前者传播原有状态，后者累积周期内恒定输入；可记为 $A_d,B_d$，但不是原连续 $A,B$。

### 2.2 与 Euler 的区别及奇异矩阵边界

Euler 只有 $\Phi_E=I+AT_s$、$\Gamma_E=BT_s$。精确 ZOH 对给定 LTI 和区间常值输入的采样时刻无此截断误差，不自动包含硬件非线性、执行延迟或 PWM 开关波形。

只有 $A$ 可逆时才能用 $\Gamma=A^{-1}(\Phi-I)B$。通用积分式不要求可逆，也可由增广矩阵指数取得：

$$
\exp\left(T_s\begin{bmatrix}A&B\\0&0\end{bmatrix}\right)
=\begin{bmatrix}\Phi&\Gamma\\0&I\end{bmatrix}.
$$

例如 $A=0$ 时 $\Phi=I$、$\Gamma=T_sB$，直接求逆反而失效。

### 2.3 RL 的输入累积算例

先写成 $\dot i=-ai+bu$，其中 $a=R/L>0$、$b=1/L$。第 $k$ 个区间电压保持为 $u[k]$，将 [RL 常值输入解析解](../mathematics/numerical-integration-and-solver-stability.md#14-rl-解析解为什么出现指数) 的起点移到 $kT_s$，得到

$$
i(kT_s+\Delta t)=\frac ba u[k]
+\left(i[k]-\frac ba u[k]\right)e^{-a\Delta t},
\qquad 0\le\Delta t\le T_s.
$$

在 $\Delta t=T_s$ 处合并输入项，得 $i[k+1]=e^{-aT_s}i[k]+\frac ba(1-e^{-aT_s})u[k]$。因此 $e^{-aT_s}$ 来自连续方程的解析传播，ZOH 提供的是区间常值输入条件。

取 $L=0.002\ \mathrm H$、$R=0.1\ \Omega$、$T_s=10^{-4}\ \mathrm s$，净电压输入下 $\dot i=-50i+500u$：

$$
\Phi=e^{-0.005}\approx0.99501248,
\qquad \Gamma=10(1-e^{-0.005})\approx0.04987521\ \mathrm{A/V}.
$$

因此 $i[k+1]=0.99501248i[k]+0.04987521u[k]$，Euler 则为 $0.995i[k]+0.05u[k]$。网压扰动需按方向另列输入，不能悄悄并入不同含义的 $u$。

## 3. 单位圆稳定性与 Z 变换

### 3.1 自然模态的精确映射

连续单模态的包络为 $|x(0)|e^{\operatorname{Re}(\lambda)t}$，所以实部为负时衰减。离散单模态每步乘 $\mu$，幅值逐步乘 $|\mu|$。例如 $\mu=-0.5$ 每步变号且缩半，$\mu=1.2$ 则逐步放大。

在 $t=kT_s$ 观察同一连续模态，有 $e^{\lambda kT_s}=(e^{\lambda T_s})^k$。这给出从连续指数率到离散乘子的映射。

$$
\mu_i=e^{\lambda_iT_s},\qquad
|\mu_i|=e^{\operatorname{Re}(\lambda_i)T_s},\qquad x[k]=\mu^kx[0].
$$

有限维定常离散自由系统渐近稳定当且仅当 $\rho(\Phi)<1$；单位圆上有根时须检查 Jordan 结构，不能称渐近稳定。该映射只针对精确连续自由采样，数字反馈加入后须重新构造完整闭环矩阵。

因为 $T_s>0$，$\operatorname{Re}(\lambda)<0$ 与 $|\mu|<1$ 等价。上述 RL 自由模态 $\lambda=-50\ \mathrm{s^{-1}}$ 映为 $\mu\approx0.99501248$。若所有乘子模长至多为 1，单位圆上的 Jordan 块都为一阶时，自由响应有界。边界上的高阶块可产生多项式增长。

### 3.2 差分方程到传递函数

对 $y[k+1]=ay[k]+bu[k]$，一侧 Z 变换有 $z(Y-y[0])=aY+bU$。零初值才得到：

$$
G(z)=\frac{b}{z-a}=\frac{bz^{-1}}{1-az^{-1}}.
$$

当 $b\ne0$ 时，极点为 $z=a$。这里 $z$ 为 Z 变换的复频域变量。

对本篇 RL，$G_d(z)=\Gamma z^{-1}/(1-\Phi z^{-1})$。电流在采样点 $k$ 读取，同期生效的电压 $u[k]$ 要到 $i[k+1]$ 才进入采样输出，因此分子含一个 $z^{-1}$。若命令还需等到下一周期才生效，应为命令至执行电压另建一个 $z^{-1}$。执行时序见 [数字 PI 与一拍延迟](../../power-electronics/digital-pi-zoh-and-control-timing.md#22-一拍延迟和连续时延)。

### 3.3 频率反推不唯一与章节索引

$e^{j\omega T_s}=e^{j(\omega+2\pi m/T_s)T_s}$，故从 $\mu$ 反推连续频率涉及复对数分支和物理先验，与混叠有相同的周期性根源。本篇不完成一般多速率或采样数据提升理论。

- [连续与离散的原有接口](state-space-feedback-and-small-signal-stability.md#42-特征值映射与稳定区域)
- [数字 PI 与执行时序](../../power-electronics/digital-pi-zoh-and-control-timing.md)
- [测量链与抗混叠](../electrical-engineering/measurement-noise-quantization-and-jitter.md)
- [LCL 模态算例](../../power-electronics/lcl-resonance-modal-stability.md)
- [MathWorks 的阶梯输入离散化说明](https://www.mathworks.com/help/control/ug/continuous-discrete-conversion-methods.html)
