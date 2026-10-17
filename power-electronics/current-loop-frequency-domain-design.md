# PI 电流环频域设计、时延与级联控制

本文用同一个 RL 电流环把 PI 参数、Bode、交越、带宽和响应时间连起来，再引入时延与内外环分离。数值是学习用的理想算例，不是可直接用于真实 PCS 的硬件调参值。

## 1. 建模边界与一般 PI 闭环

### 1.1 净电压输入与 RL plant

取电流由变流器流向网侧为正：

$$
L\dot i+Ri=v_c-v_g,
\qquad
u=v_c-v_g,
\qquad L,R>0.
$$

零初始增量下：

$$
G(s)=\frac{I(s)}{U(s)}=\frac1{Ls+R}.
$$

本篇假设 $v_g$ 前馈准确、平均电压指令可理想执行，没有额外 $Ri$ 状态补偿，暂不计采样、PWM 时延、LCL 高阶模态和 dq 耦合。模型边界首先固定在局部电流通道，而非整个电网。

如果加入精确 $Ri$ 状态补偿，PI 所见 plant 变为 $1/(Ls)$，不是本文的 $1/(Ls+R)$；两种结构的区别见 [并网变流器电流动态](converter-current-dynamics.md#34-plant-的边界决定-pi-所见动态)。

### 1.2 PI 控制器与两个状态

取 $e=i^*-i$：

$$
C(s)=K_p+\frac{K_i}{s}
=\frac{K_p s+K_i}{s},
\qquad
\dot\xi=e,
\qquad
u=K_p e+K_i\xi.
$$

理想 PI 有一个积分状态；RL 有一个电感电流状态。一般单位负反馈的环路与参考通道为：

$$
L_{\mathrm{loop}}(s)=\frac{K_p s+K_i}{s(Ls+R)},
\qquad
T(s)=\frac{K_p s+K_i}{Ls^2+(R+K_p)s+K_i}.
$$

一般闭环是二阶。单独 PI 是一阶且有比例直通项，不等同于标准一阶低通。具体状态模型见 [控制基础的 PI-RL 状态推导](../foundations/control/poles-zeros-bandwidth-and-steady-state-error.md#65-pi-rl-闭环的两个状态)。

### 1.3 参考、误差与控制动作不是同一种信号

以下采用归一化标量教学模型，时间单位为秒，不是将本文 RL 参数改写成 1：

$$
G(s)=\frac1{s+1},
\qquad
C(s)=9,
\qquad
r(t)=100\quad(t\ge0),
\qquad y(0)=0.
$$

plant 满足 $\dot y+y=u$，控制器选择 $u=9(r-y)$，因此：

$$
\dot y+10y=900,
\qquad
y(t)=90(1-e^{-10t}).
$$

稳态时 $y=90$、$e=10$、$u=90$、$\dot y=u-y=0$。误差 10 是控制器的输入，不是新的输出目标；实际输出仍由控制作用与物理动态决定。这里 P 用非零误差维持所需输入，最终并没有达到参考 100。

这也是“响应很快”和“稳态准确”不同的例子：本例时间常数为 0.1 s，但最终参考误差仍为 10。

### 1.4 积分状态为什么能在误差归零后保持输入

改用 PI：

$$
\dot\xi=e,
\qquad
u=K_p e+K_i\xi.
$$

在闭环稳定、执行器未持续饱和、积分作用有效等条件下，对上一教学 plant 的常值参考，可以到达 $y=100$、$e=0$。此时：

$$
\dot\xi=0,
\qquad
\xi=\frac{100}{K_i},
\qquad
u=K_i\xi=100\quad(K_i>0).
$$

导数为零只说明积分状态不再改变，不说明它归零。此前积累的状态继续提供维持输出的作用，因此 $e=0$ 不推出 $u=0$。

对应到 RL 的常值净电压模型，稳态保持非零电流通常需要 $u=Ri$；在 $v_g$ 前馈且未补偿 $Ri$ 的边界下，PI 也要提供这部分维持作用。不要把“误差消失”误读为“变流器电压命令消失”。积分控制与稳态误差的关系可参考 [ECE 486 控制讲义](https://courses.physics.illinois.edu/ece486/fa2026/documentation/handbook/lec09.html)。

## 2. 理想匹配：从参数到频域指标

### 2.1 PI 零点匹配 RL 极点

plant 极点为 $-R/L$，PI 零点为 $-K_i/K_p$。令 $K_p,K_i>0$ 且：

$$
\frac{K_i}{K_p}=\frac RL.
$$

则：

$$
L_{\mathrm{loop}}(s)
=\frac{K_p(s+R/L)}{Ls(s+R/L)}
=\frac{K_p}{Ls}
=\frac{\omega_0}{s},
\qquad
\omega_0=\frac{K_p}{L}.
$$

目标时间尺度 $\omega_0$ 对应：

$$
K_p=L\omega_0,
\qquad
K_i=R\omega_0.
$$

物理单位为 $[K_p]=\Omega$、$[K_i]=\Omega/\mathrm s$、$[L]=\Omega\cdot\mathrm s$。软件若使用标幺电流、电压或归一化调制量，还需计入基值与执行器增益。

### 2.2 纯积分环路的 Bode 与裕度

匹配后：

$$
|L_{\mathrm{loop}}(j\omega)|=\frac{\omega_0}{\omega},
\qquad
\angle L_{\mathrm{loop}}(j\omega)=-90^\circ.
$$

幅频斜率为 -20 dB/dec，幅值到 1 时 $\omega_{\mathrm{gc}}=\omega_0$，经典相位裕度为 $90^\circ$。无有限频率的 $-180^\circ$ 相位交越，因此此理想模型的经典 GM 按惯例记为无穷大。

这不意味着任意带宽都安全。它只是这个没有延迟、共振和非理想约束的简化模型的结果；原点积分极点在 Nyquist 分析中仍需按相应轮廓处理。

### 2.3 闭环带宽与响应时间

参考到电流通道约简为：

$$
T(s)=\frac{\omega_0}{s+\omega_0},
\qquad
T(0)=1.
$$

其幅值为 $\omega_0/\sqrt{\omega_0^2+\omega^2}$，相对 DC 增益下降到 $1/\sqrt2$ 时：

$$
\omega_{\mathrm{BW}}=\omega_0=\omega_{\mathrm{gc}},
\qquad
\tau_{\mathrm{cl}}=\frac1{\omega_0}.
$$

这是两种定义在特定模型中的数值重合。$1/\sqrt2$ 是幅值门槛，不是频率；$K_p/\sqrt2$ 的单位是欧姆，不能用作带宽。

### 2.4 改变测试频率与 PI 参数的不同作用

在 $\omega_0$ 已固定时，提高测试频率 $\omega$ 会使 $|L_{\mathrm{loop}}(j\omega)|=\omega_0/\omega$ 下降；只是在扫描同一个系统，不会改变它的交越、带宽和时间常数。

在固定测试频率下，重新设计 $K_p$ 并按 $K_i/K_p=R/L$ 同步调整 $K_i$，会提高 $\omega_0=K_p/L$、移动曲线与交越。只改 $K_p$ 而固定 $K_i$，则匹配关系已改变，不能继续无条件套用约简公式。

理想无延迟匹配环路的相位始终为 $-90^\circ$，所以提高匹配增益本身不降低其 $90^\circ$ PM。这与 [三极点增益算例](../foundations/control/control-frequency-domain-analysis.md#53-三极点算例提高增益如何触及临界点)形成对照；有延迟和其他动态后，才需按第 4 节及完整模型重新判断。

## 3. 完整算例：2 mH、0.1 Ω、100 Hz

### 3.1 计算参数与理想闭环

设：

$$
L=0.002\ \mathrm H,
\qquad
R=0.1\ \Omega,
\qquad
f_{\mathrm{gc}}=100\ \mathrm{Hz}.
$$

先把 Hz 换为 rad/s，再计算增益：

$$
\omega_0=2\pi f_{\mathrm{gc}}\approx628.32\ \mathrm{rad/s},
$$

$$
K_p=L\omega_0\approx1.2566\ \Omega,
\qquad
K_i=R\omega_0\approx62.8319\ \Omega/\mathrm s.
$$

检查匹配：$K_i/K_p=R/L=50\ \mathrm s^{-1}$。裸 plant 的极点为 $-50\ \mathrm s^{-1}$，自然时间常数 20 ms，不是闭环的 100 Hz 带宽。

理想匹配后：

$$
L_{\mathrm{loop}}(s)=\frac{628.32}{s},
\qquad
T(s)=\frac{628.32}{s+628.32}.
$$

### 3.2 用频域与时域交叉检查

| 指标 | 理想结果 |
| --- | --- |
| 增益交越 $\omega_{\mathrm{gc}}$ | 约 628.32 rad/s，即 100 Hz |
| 闭环带宽 $f_{\mathrm{BW}}$ | 100 Hz |
| 交越处开环相位、PM | $-90^\circ$、$90^\circ$ |
| 带宽处闭环幅值、相位 | $1/\sqrt2$、$-45^\circ$ |
| 闭环时间常数 | 约 1.59 ms |
| 10%–90% 上升时间 | $\ln9/\omega_0\approx3.50$ ms |
| 2% settling time | $\ln50/\omega_0\approx6.23$ ms |

从零初始增量施加幅值 $I^*$ 的电流参考阶跃：

$$
i(t)=I^*(1-e^{-\omega_0t}),
\qquad t\ge0.
$$

本表严格对应理想一阶参考通道，未包含限幅、延迟和工况变化。它不是完整硬件上升时间或所有扰动通道的保证。

### 3.3 极零相消没有消除物理状态

原闭环分母在匹配时为：

$$
Ls^2+(R+K_p)s+K_i=L(s+R/L)(s+K_p/L).
$$

参考通道的分子约去稳定因子 $s+R/L$，但电感与积分器的状态仍存在。本例内部特征根为 $-50$ 与约 $-628.32\ \mathrm s^{-1}$；非零初始状态或其他扰动通道可能显现被隐藏的模态。

$R,L$ 参数误差破坏精确相消，真实控制也可能增加更多状态。不应依赖相消不稳定极点来获得表面稳定的传递函数。

### 3.4 三个频率的贯通计算：50、600、600 rad/s

回顾题另取：

$$
L=0.002\ \mathrm H,
\qquad
R=0.1\ \Omega,
\qquad
K_p=1.2\ \Omega,
\qquad
K_i=60\ \Omega/\mathrm s.
$$

这是不同于前面精确 100 Hz 设计的一组参数；$K_i/K_p=50\ \mathrm s^{-1}=R/L$，仍满足理想匹配。因此：

$$
\omega_p=\frac RL=50\ \mathrm{rad/s},
\qquad
L_{\mathrm{loop}}(s)=\frac{600}{s},
\qquad
T(s)=\frac{600}{s+600}.
$$

得到 $\omega_{\mathrm{gc}}=\omega_{\mathrm{BW}}=600\ \mathrm{rad/s}$，约为 95.49 Hz；时间常数为 $1/600\ \mathrm s\approx1.667$ ms。裸 plant 转折没有随控制器参数改变，闭环极点则为 $-600\ \mathrm s^{-1}$，不能把两种极点的转折混用。

带宽复核应写为：

$$
\frac{600}{\sqrt{\omega^2+600^2}}=\frac1{\sqrt2},
\qquad
600^2=360000,
\qquad
\omega=600\ \mathrm{rad/s}.
$$

分母不能误写成 $\sqrt{\omega^2+3600}$。后两个频率相等来自匹配后的纯积分环路与标准一阶闭环，不是定义相同，也不代表电感或电阻发生改变。三个频率所属对象的对比见 [交越与带宽](../foundations/control/control-frequency-domain-analysis.md#41-0-db-交越与相对--3-db-带宽)。

## 4. 采样、PWM 与纯时间延迟

### 4.1 真实执行链与模型近似

~~~text
measurement → ADC → controller calculation → PWM update
     ↑                                         ↓
filter current ← power bridge and gate drive ← voltage command
~~~

计算、采样保持、PWM 更新与测量滤波都会影响动态。用一个 $T_d$ 等效纯延迟是入门近似，不等于完整 sampled-data 模型，也不自动覆盖零阶保持的幅值影响。

### 4.2 纯延迟为何不改幅值却改相位

$$
G_d(s)=e^{-sT_d},
\qquad
G_d(j\omega)=e^{-j\omega T_d}.
$$

$$
|G_d(j\omega)|=1,
\qquad
\angle G_d(j\omega)=-\omega T_d\ \mathrm{rad}.
$$

纯延迟的增益是 0 dB，但展开相位随频率线性下降。只读幅值 Bode 会漏掉这种潜在稳定性损失。

### 4.3 理想积分环路加延迟的 PM

把单一纯延迟放入前向路径：

$$
L_{\mathrm{loop},d}(s)=\frac{\omega_0}{s}e^{-sT_d}.
$$

幅值不变，仍在 $\omega_{\mathrm{gc}}=\omega_0$ 交越；相位变为：

$$
\angle L_{\mathrm{loop},d}(j\omega)
=-90^\circ-\omega T_d\frac{180^\circ}{\pi}.
$$

因此此特定模型的经典 PM 为：

$$
PM=90^\circ-\omega_{\mathrm{gc}}T_d\frac{180^\circ}{\pi}.
$$

该公式专用于理想积分环路加一个纯延迟，不是任意 PI、LCL、PLL 或离散控制的通用公式。

### 4.4 0.5 ms 延迟的定量比较

令 $T_d=0.0005\ \mathrm s$：

| 增益交越 | 延迟相位滞后 | PM |
| --- | --- | --- |
| 100 Hz | $18^\circ$ | $72^\circ$ |
| 300 Hz | $54^\circ$ | $36^\circ$ |
| 500 Hz | $90^\circ$ | $0^\circ$ |

达到 $\omega_0T_d=\pi/2$ 时，该模型出现虚轴临界根，不能继续当作渐近稳定系统。结论不是“所有 0.5 ms 延迟的真实变流器都在 500 Hz 临界”，因为真实 plant、控制结构和离散化可能不同。

加入延迟后的参考通道为：

$$
T_d^{\mathrm{cl}}(s)
=\frac{\omega_0e^{-sT_d}}{s+\omega_0e^{-sT_d}}.
$$

这里上标用于区分闭环传递函数与延迟时间 $T_d$。该通道不再是标准一阶低通，不能继续直接写 $\omega_{\mathrm{BW}}=\omega_{\mathrm{gc}}$ 或沿用前节的上升/settling time；应重新求闭环频响与时域响应。

### 4.5 延迟系统的特征方程

闭环特征方程为：

$$
s+\omega_0e^{-sT_d}=0.
$$

$T_d>0$ 时它是超越方程，不是有限阶多项式，关联延迟微分方程与无限维谱。本文只建立接口，不展开其完整稳定性证明。Padé 近似或离散模型用于进一步分析时，还需检查近似阶数、采样率与有效频段。

## 5. 内外环快慢与带宽分离

### 5.1 级联结构为什么要求内环够快

~~~text
P/Q or DC-link target
          ↓
outer controller → id*/iq* → inner current loop → actual id/iq
~~~

外环生成参考，内环负责执行。若内环在外环工作频段内有 $T_{\mathrm{inner}}(j\omega)\approx1$，外环才可以近似把它当作快速电流执行机构。

一阶内环 $T_{\mathrm{inner}}=\omega_i/(s+\omega_i)$，若外环典型频率为 $\omega_i/10$，该频率处内环幅值约 0.995、相位约 $-5.71^\circ$；若为 $\omega_i/5$，则约 0.981、$-11.31^\circ$。即使已有分离，内环也不是严格瞬时执行。

### 5.2 5–10 倍是设计起点

课程中的初始估算是 $\omega_{\mathrm{inner}}\approx(5\text{–}10)\omega_{\mathrm{outer}}$，例如 100 Hz 内环与 10 Hz 外环。它不是普适定理或强制标准；实际比例要结合相位、耦合、限幅、噪声和运行点验证。

不同应用有不同经验比例，[imperix 级联电压控制说明](https://imperix.com/doc/implementation/cascaded-voltage-control)也强调内环足够快及其对外环的直接作用。不能把某一经验值替代完整闭环检查。

### 5.3 开关、采样、同步与控制带宽要分开

| 对象 | 含义 |
| --- | --- |
| 开关频率 | 功率桥执行离散开关动作的频率 |
| 采样频率 | 获取信号或更新控制计算的速率 |
| 开环交越 | 完整环路幅值达到 1 的频率 |
| 闭环带宽 | 指定跟踪通道的频率范围指标 |
| rise/settling time | 指定时域响应的速度指标 |

PWM 为 10 kHz，不表示电流环也有 10 kHz 带宽。EMS/调度通常更慢，功率或电压外环通常慢于电流内环；这些只是常见层级，不是一张通用带宽表。

PLL 是同步估计环，不应机械放进固定快慢顺序。其带宽受电网强度、噪声与电流环耦合影响；GFM 主同步参考也未必依赖 PLL。

### 5.4 dq 带宽与 50 Hz 基波不冲突

平衡正序 abc 电流仍以电网基波变化，同步 dq 中的稳态 $i_d,i_q$ 则可近似常值。100 Hz dq 电流环带宽描述工作点附近指令或扰动的跟踪，不是把实际 AC 基波改成 100 Hz。

负序、不平衡和谐波可能产生多频耦合，不能将所有 dq 现象都当作 DC。坐标与同步边界见 [Clarke 与 Park 坐标变换](../foundations/electrical-engineering/clarke-and-park-transformations.md)和 [PLL 与 SRF-PLL](../ibr-grid-integration/pll-and-srf-pll.md)。

## 6. 工程边界、误区与下一步

### 6.1 真实设计还要检查什么

- $R,L$ 变化、传感器和执行器增益误差。
- 采样保持、计算和 PWM 更新，及有效频段。
- DC-link 电压、调制限幅、电流限制与 anti-windup。
- LCL 共振、阻尼、反馈电流位置和电网阻抗。
- dq 耦合、PLL 和外环交互，多工况稳定裕度。

先以理想匹配得到设计起点，再在完整模型上重算 Bode、所有交越、闭环带宽及时间响应，不能只靠 $K_p=L\omega_0$、$K_i=R\omega_0$ 两个公式验收。

### 6.2 常见误区与自测

- PI 一阶不意味着完整 PI-RL 闭环只有一个状态。
- 理想无延迟 PM 为 $90^\circ$，不意味着真实控制可以无限提速。
- 纯延迟幅值为 1，不意味着它对反馈稳定性没有影响。
- 开环交越不变，不意味着闭环带宽和响应时间不变。
- 内外环十倍分离不等于所有工况稳定的证明。
- 误差是控制器输入，不是 plant 下一刻的新目标；PI 可以在 $e=0$ 时保持非零维持输入。
- 只改测试频率不改变固定系统速度；只改 $K_p$ 不保证 PI-RL 仍匹配。
- 裸 RL 的 $R/L$ 不必等于完整环路交越或闭环带宽，rad/s 与 Hz 也不能直接互换。

应能从物理边界独立计算 100 Hz 算例的 PI 参数，区分开环 $-90^\circ$ 与闭环带宽处 $-45^\circ$，并复算 0.5 ms 延迟表。下一步加入参数失配、离散化和限幅，再比较频域预测与时域仿真。

本轮复习还应能解释 P 教学例为何停在 $y=90$，PI 如何在 $e=0$ 时仍输出 100，并独立求出回顾题的 $50,600,600\ \mathrm{rad/s}$，包括匹配条件、平方项与单位换算。

### 6.3 相关笔记

- [控制系统频域分析：Bode、反馈与稳定裕度](../foundations/control/control-frequency-domain-analysis.md)
- [极点、零点、带宽与稳态误差](../foundations/control/poles-zeros-bandwidth-and-steady-state-error.md)
- [并网变流器电流动态](converter-current-dynamics.md)
- [频域稳定性与阻抗分析接口](../ibr-grid-integration/frequency-domain-stability-and-impedance-interfaces.md)
