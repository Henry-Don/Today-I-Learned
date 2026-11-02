# 采样、混叠与精确 ZOH 离散系统

采样规定观察与控制的节拍；离散化建立采样时刻的数学递推。连续 plant 在两个采样时刻之间仍然演化。

## 1. 采样与混叠

### 1.1 采样频率与闭环带宽

均匀理想采样 $x[k]=x(kT_s)$，$f_s=1/T_s$。$T_s=100\ \mu\mathrm s$ 对应 10 kHz，不代表电流环能达到 10 kHz 带宽；带宽还受到 plant、保持、反馈、执行能力和延迟限制。

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

取 $L=0.002\ \mathrm H$、$R=0.1\ \Omega$、$T_s=10^{-4}\ \mathrm s$，净电压输入下 $\dot i=-50i+500u$：

$$
\Phi=e^{-0.005}\approx0.99501248,
\qquad \Gamma=10(1-e^{-0.005})\approx0.04987521\ \mathrm{A/V}.
$$

因此 $i[k+1]=0.99501248i[k]+0.04987521u[k]$，Euler 则为 $0.995i[k]+0.05u[k]$。网压扰动需按方向另列输入，不能悄悄并入不同含义的 $u$。

## 3. 单位圆稳定性与 Z 变换

### 3.1 自然模态的精确映射

$$
\mu_i=e^{\lambda_iT_s},\qquad
|\mu_i|=e^{\operatorname{Re}(\lambda_i)T_s},\qquad x[k]=\mu^kx[0].
$$

有限维定常离散自由系统渐近稳定当且仅当 $\rho(\Phi)<1$；单位圆上有根时须检查 Jordan 结构，不能称渐近稳定。该映射只针对精确连续自由采样，数字反馈加入后须重新构造完整闭环矩阵。

### 3.2 差分方程到传递函数

对 $y[k+1]=ay[k]+bu[k]$，一侧 Z 变换有 $z(Y-y[0])=aY+bU$。零初值才得到：

$$
G(z)=\frac{b}{z-a}=\frac{bz^{-1}}{1-az^{-1}}.
$$

极点是 $z=a$。这里 $z$ 为复频域变量，不是前一课的模态坐标向量。

### 3.3 频率反推不唯一与章节索引

$e^{j\omega T_s}=e^{j(\omega+2\pi m/T_s)T_s}$，故从 $\mu$ 反推连续频率涉及复对数分支和物理先验，与混叠有相同的周期性根源。本篇不完成一般多速率或采样数据提升理论。

- [连续与离散的原有接口](state-space-feedback-and-small-signal-stability.md#42-特征值映射与稳定区域)
- [数字 PI 与执行时序](../../power-electronics/digital-pi-zoh-and-control-timing.md)
- [测量链与抗混叠](../electrical-engineering/measurement-noise-quantization-and-jitter.md)
- [LCL 模态算例](../../power-electronics/lcl-resonance-modal-stability.md)
