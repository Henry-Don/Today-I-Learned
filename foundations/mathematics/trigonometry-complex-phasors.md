# 三角、复数与相量

## 1. 一条统一主线

这组概念可以串成一条工程主线：

$$
\boxed{
\text{单位圆}
\rightarrow \sin/\cos
\rightarrow \text{二维旋转}
\rightarrow e^{j\theta}
\rightarrow \text{幅值与相角}
\rightarrow \text{相量}
\rightarrow \frac{d}{dt}\leftrightarrow j\omega
\rightarrow \text{交流电路代数化}
}
$$

核心直觉是：正弦量可以看成复平面内匀速旋转向量在某条轴上的投影。导线中的瞬时电压和电流仍是实数；复数只是完整保存幅值、相位和旋转关系的数学表示。

## 2. 正弦、余弦、频率与相位

标准正弦稳态量可写成：

$$
x(t)=A\cos(\omega t+\phi)
$$

其中 $A$ 是峰值，$\omega$ 是角频率（rad/s），$\phi$ 是初相位。频率 $f$ 和周期 $T$ 满足：

$$
\omega=2\pi f,\qquad T=\frac{1}{f}=\frac{2\pi}{\omega}
$$

正弦和余弦只是相差 $90^\circ$ 的同一种周期信号：

$$
\sin\theta=\cos\left(\theta-\frac{\pi}{2}\right)
$$

工程上采用哪一个主要是约定；相量分析通常以余弦为基准。

对于两个同频信号：

$$
x_1=A_1\cos(\omega t+\phi_1),\qquad
x_2=A_2\cos(\omega t+\phi_2)
$$

相位差为：

$$
\Delta\phi=\phi_2-\phi_1
$$

$\Delta\phi>0$ 表示 $x_2$ 相对 $x_1$ 超前，反之表示滞后。因为

$$
A\cos(\omega t+\phi)=A\cos\left[\omega\left(t+\frac{\phi}{\omega}\right)\right]
$$

所以正相位对应波形在时间上的提前。

相位是圆周上的量：

$$
\theta\equiv\theta+2k\pi
$$

例如 $30^\circ$、$390^\circ$ 和 $-330^\circ$ 表示同一方向。

## 3. 正弦为什么会自然出现

### 3.1 LC 电路的自然振荡

理想 LC 电路满足：

$$
L\frac{di}{dt}+v_C=0,\qquad i=C\frac{dv_C}{dt}
$$

消去电流得到：

$$
LC\frac{d^2v_C}{dt^2}+v_C=0
$$

令 $\omega_0=1/\sqrt{LC}$：

$$
v_C''+\omega_0^2v_C=0
$$

其自然解为：

$$
v_C(t)=A\cos(\omega_0t+\phi)
$$

因此正弦不是人为强加给系统的形状，而是大量线性振荡系统的自然模态。

### 3.2 旋转发电机与正弦电压

在线圈匀速旋转的简化模型中：

$$
\theta=\omega t,\qquad \Phi(t)=BA\cos\omega t
$$

由法拉第定律：

$$
e(t)=-N\frac{d\Phi}{dt}=NBA\omega\sin\omega t
$$

所以机械匀速旋转会通过周期变化的磁通产生正弦感应电压。实际同步发电机还会用绕组分布和磁路设计，使基波尽量接近正弦。

### 3.3 LTI 系统中的特殊地位

在线性时不变（LTI）系统中，正弦稳态输入经过系统后频率不变，只改变幅值和相位：

$$
e^{j\omega t}\rightarrow H(j\omega)e^{j\omega t}
$$

这使庞大的交流网络可以在每个固定频率上用幅值和相位来分析。理想正弦还只有一个频率分量；方波等非正弦波包含许多谐波，会增加损耗、发热、振动、谐振和 EMI 风险。

## 4. 为什么是交流，以及为什么通常是 50/60 Hz

传统交流系统的重要优势是可以直接使用工频变压器改变电压。理想变压器依赖交变磁通：

$$
v=N\frac{d\Phi}{dt}
$$

恒定直流达到稳态后 $d\Phi/dt=0$，不能用传统工频变压器直接变压。升高输电电压可在相同功率下降低电流，从而降低线路损耗：

$$
P\approx VI,\qquad P_{\text{loss}}=I^2R
$$

这不是说交流永远优于直流。现代 HVDC 在长距离、大容量、海缆和异步联网中非常重要；BESS 本身也体现了 AC/DC 分工：

$$
\boxed{\text{Battery DC}\leftrightarrow\text{PCS}\leftrightarrow\text{Grid AC}}
$$

50/60 Hz 是长期工程折中与标准化的结果，并非数学常数。

- 频率太低时，由 $V\approx4.44fN\Phi_{\max}$ 可知，变压器需要更多匝数或更高磁通；受磁饱和限制，设备会更大、更重。同步转速 $n_s=120f/P$ 也会显著下降。
- 频率太高时，$X_L=2\pi fL$ 和 $I_C=2\pi fCV$ 增加，趋肤效应、涡流损耗、电缆充电电流和 EMI 更突出。

高频有利于缩小磁性元件，所以适合开关电源与 DC/DC；但它不适合作为传统大范围输电网的统一工频。

## 5. 复平面：二维坐标加旋转代数

复数写作：

$$
z=a+jb,\qquad j^2=-1
$$

在几何上，$z$ 就是二维点 $(a,b)$。与普通二维向量相比，复数额外提供了一套特别适合描述二维缩放和旋转的乘法结构。

二维旋转矩阵：

$$
\begin{bmatrix}a'\\b'\end{bmatrix}
=
\begin{bmatrix}
\cos\theta&-\sin\theta\\
\sin\theta&\cos\theta
\end{bmatrix}
\begin{bmatrix}a\\b\end{bmatrix}
$$

用复数只需写成：

$$
z'=e^{j\theta}z
$$

两者完全等价。特别地，$j=e^{j\pi/2}$，所以乘以 $j$ 就是逆时针旋转 $90^\circ$。

## 6. 欧拉公式、幅值与相角

欧拉公式是：

$$
\boxed{e^{j\theta}=\cos\theta+j\sin\theta}
$$

它可以由 Taylor 级数得到：

$$
e^{j\theta}
=1+j\theta-\frac{\theta^2}{2!}-j\frac{\theta^3}{3!}+\frac{\theta^4}{4!}+\cdots
$$

把实部和虚部分组，正好分别得到余弦与正弦级数。因此 $e^{j\theta}$ 是单位圆上的旋转。

对 $z=a+jb$：

$$
|z|=\sqrt{a^2+b^2},\qquad \theta=\arg(z)
$$

工程实现应使用：

$$
\theta=\operatorname{atan2}(b,a)
$$

而不是简单的 $\arctan(b/a)$。后者无法区分象限，在 $a=0$ 时也会失效。常见函数参数顺序是 $\operatorname{atan2}(y,x)$，因此对复数应代入 $y=b$、$x=a$。

极坐标形式为：

$$
z=re^{j\theta}=r\angle\theta
$$

若 $z_1=r_1e^{j\theta_1}$、$z_2=r_2e^{j\theta_2}$，则：

$$
z_1z_2=r_1r_2e^{j(\theta_1+\theta_2)}
$$

即“幅值相乘，相角相加”，不是“角度相乘”。

## 7. 为什么一条实轴不足以描述振荡

只知道某一瞬间 $x=0.5$，无法判断它对应单位圆上的 $+60^\circ$ 还是 $-60^\circ$，也不能判断下一瞬间上升还是下降。一个瞬时实数值不足以唯一确定完整振荡状态。

任意固定频率的实正弦都可写成：

$$
x(t)=a\cos\omega t+b\sin\omega t
$$

因此完整描述需要两个独立实数 $(a,b)$。同时，微分会在两个基函数之间转换：

$$
\frac{d}{dt}\cos\omega t=-\omega\sin\omega t,
\qquad
\frac{d}{dt}\sin\omega t=\omega\cos\omega t
$$

$\{\cos\omega t,\sin\omega t\}$ 构成一个对微分封闭的二维实空间。一个复数恰好把这两个实自由度统一表示：

$$
2\text{ 个实自由度}\leftrightarrow1\text{ 个复自由度}
$$

对理想单频正弦 $x(t)=A\cos(\omega t+\phi)$，定义正交分量 $y(t)=A\sin(\omega t+\phi)$，则：

$$
y(t)=-\frac{1}{\omega}\dot x(t)
$$

所以：

$$
z(t)=x(t)+jy(t)=x(t)-j\frac{\dot x(t)}{\omega}
$$

这里的第二维不是另一股神秘物理量，而是补足相位和变化趋势所需的正交状态。

## 8. 为什么最后可以取实部

构造旋转量：

$$
z(t)=Ae^{j(\omega t+\phi)}
$$

采用余弦约定时，物理信号是：

$$
x(t)=\Re\{z(t)\}
$$

这表示从完整数学状态中读取选定的物理投影，并不表示完整复数信息“藏在实部里”。映射 $\Re:\mathbb C\to\mathbb R$ 是多对一的，单独取实部会丢失信息。

也没有物理定律规定必须取实部。若从正弦约定出发，同样可以写：

$$
x(t)=\Im\{Ae^{j(\omega t+\phi)}\}
$$

## 9. 相量：提取公共时间因子

由：

$$
A\cos(\omega t+\phi)
=\Re\left\{Ae^{j\phi}e^{j\omega t}\right\}
$$

定义相量：

$$
\underline X=Ae^{j\phi}
$$

于是：

$$
x(t)=\Re\{\underline Xe^{j\omega t}\}
$$

相量法适用于固定频率的正弦稳态。所有信号共享 $e^{j\omega t}$，所以把公共时间因子提取出去，只保留每个信号特有的幅值和相位。它并不是把真实电压或电流“变成虚数”。

### Peak 与 RMS 约定

若：

$$
v(t)=V_m\cos(\omega t+\phi)
$$

则峰值相量为 $V_m\angle\phi$，RMS 相量为：

$$
\underline V=\frac{V_m}{\sqrt2}\angle\phi
$$

电力系统通常默认 RMS。例如 $v(t)=325\cos(100\pi t)$ V 对应约 $230\angle0^\circ$ V 的 RMS 相量。使用公式前必须确认相量采用 peak 还是 RMS。

## 10. 为什么微分变成乘以 $j\omega$

因为：

$$
\frac{d}{dt}e^{j\omega t}=j\omega e^{j\omega t}
$$

所以在固定频率的相量域中：

$$
\boxed{\frac{d}{dt}\longleftrightarrow j\omega}
$$

微分运算因此变成代数乘法。三类基本元件的阻抗为：

$$
Z_R=R,\qquad Z_L=j\omega L,\qquad Z_C=\frac{1}{j\omega C}
$$

这分别对应电阻的 $0^\circ$、电感的 $+90^\circ$ 和电容的 $-90^\circ$ 阻抗角。

## 11. Wrapped 与 unwrapped phase

由于相角只在模 $2\pi$ 意义下唯一，常见主值区间可以是 $(-\pi,\pi]$，也可以是 $[0,2\pi)$。将相位限制在固定区间称为 wrapped phase。

在 Bode 图、dq 阻抗扫描等频率响应问题中，更关心相位随频率的连续变化。因此会把跨越边界后的相位继续记为 $-190^\circ$、$-230^\circ$ 等，这称为 unwrapped phase。

## 12. 通向三相系统、dq 与阻抗分析

平衡三相电压本质上是一组三相相差 $120^\circ$ 的旋转结构：

$$
v_a=V\cos\theta
$$

$$
v_b=V\cos\left(\theta-\frac{2\pi}{3}\right),\qquad
v_c=V\cos\left(\theta+\frac{2\pi}{3}\right)
$$

Clarke 变换把三相量映射到二维 $\alpha\beta$ 平面，可用复空间矢量 $v_{\alpha\beta}=v_\alpha+jv_\beta$ 表示。Park 变换让坐标系以同步角速度旋转，使稳态交流量在 dq 坐标中变成近似常量：

$$
\boxed{\text{AC in stationary frame}\rightarrow\text{DC in synchronous frame}}
$$

频率扫描中的 $Z(j\omega)$、$Y(j\omega)$ 同样由幅值与相位描述；Bode 图绘制 $20\log_{10}|Z(j\omega)|$ 与 $\angle Z(j\omega)$。因此复平面、`atan2`、相位展开和 $j\omega$ 会直接进入 PLL、dq 控制、阻抗建模和 Nyquist 稳定性分析。

## 13. 常见误区

1. **“复平面是普通坐标系解决不了才出现的。”** 复平面在几何上就是二维笛卡尔平面；关键新增内容是适合旋转的乘法结构。
2. **“复数乘法是角乘角。”** 实际是模相乘、相角相加。
3. **“整个复数的信息被压进了实部。”** 实部只是投影，单独取实部会丢失相位信息。
4. **“虚部是一股真实存在的虚数电流。”** 瞬时电流仍为实数；相量虚部是所选坐标系中的正交分量。
5. **“相角必须固定在一个范围。”** 不同主值区间都合法，频率响应还常需展开相位。
6. **“相量可用于任意瞬态。”** 标准相量法以单一频率正弦稳态为前提；快速暂态、谐波和变频过程需要更完整的时域或频域模型。

