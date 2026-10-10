# 并网变流器电流动态

## 1. 从电压指令到电流轨迹

并网变流器的电流控制建立在一条基本因果链上：

$$
v_c
\longrightarrow
v_L
\longrightarrow
\frac{di}{dt}
\longrightarrow
i(t)
$$

控制器不能在物理上把电流瞬间设置为某个目标值。它先改变变流器端电压，使滤波电感两端形成净电压，再通过电流斜率逐渐改变电流。

本笔记以最简单的串联 RL 并网接口为起点，建立后续 dq 电流内环、限幅、anti-windup、PLL 和弱电网交互所需的物理直觉。

## 2. RL 并网接口模型

考虑以下平均模型：

~~~text
Converter ── R ── L ── Grid
    vc                 vg
             → i
~~~

沿电流正方向应用 KVL：

$$
v_c=Ri+L\frac{di}{dt}+v_g
$$

整理得到：

$$
L\frac{di}{dt}=v_c-v_g-Ri
$$

或：

$$
\frac{di}{dt}
=
\frac{v_c-v_g-Ri}{L}
$$

这里的 $v_g$ 表示所选 RL 支路电网侧端口的实际电压；若该端口就是 PCC，则可记为 $v_{\mathrm{PCC}}$。它不能同时被当成电网阻抗后方完全刚性的远端理想电源电压。后文用 $E_g$ 单独表示远端 Thevenin 电源。

### 2.1 为什么电路两端都能画成电压源

从 VSC 的 AC 端平均模型看，变流器利用 DC-link 能量和开关桥合成可控电压 $v_c$；外部电网提供另一个端口电压 $v_g$。KVL 是两个端口电压与支路压降的平衡：

$$
v_c-Ri-L\dot i-v_g=0
$$

这不是“一个电源单独供一个电阻负载”的拓扑。正方向电流由两端电压差推动，功率可从变流器送往网络，也可从网络流入变流器。平均模型的可控电压源表示不意味着它具有无限电压、无限电流或无限能量；实际能力受 DC-link、调制、器件和储能状态约束。

在交流中，瞬时电压差的正负决定当前电流斜率；有功方向还取决于电压与电流的相对相位，不能仅比较两个电压幅值大小。

### 2.2 最简电流模型没有画出的负载

真实系统可以包含以下支路：

~~~text
Battery / DC link → Converter → filter → bus
                                         ├── local loads
                                         ├── other converters / generators
                                         └── external network
~~~

RL 方程只描述选定接口支路的电流动态，负载可能在其他支路中显式建模，也可能已包含在外部网络等效内。没有画出负载不等于现实没有负载；也不能把被等效包含的负载又重复计入同一个网络。

### 2.3 净电感电压与电流斜率

定义净电感电压：

$$
v_L=v_c-v_g-Ri
$$

则：

$$
v_L=L\frac{di}{dt}
$$

因此：

- $v_L>0$：正方向电流增加；
- $v_L<0$：正方向电流减小；
- $v_L=0$：当前电流斜率为零。

## 3. 方程中每一项的角色

将方程写成：

$$
\dot{i}
=
-\frac{R}{L}i
+\frac{1}{L}v_c
-\frac{1}{L}v_g
$$

可以直接读出：

- 状态：$i$；
- 控制输入：$v_c$；
- 外部扰动：$v_g$；
- 自然耗散项：$-(R/L)i$；
- 状态变化率：$\dot{i}$。

这里把 $v_c$ 视为控制输入，是因为平均模型假设控制器可以通过调制主动影响变流器端基波电压。对孤立的滤波支路模型，$v_g$ 是由边界外部提供的端口电压，局部电流控制器通常不能直接命令它。

“外部输入”是一种模型角色，不等于该电压在完整互联系统中完全不受变流器影响。若把电网阻抗与负载纳入边界，端口电压可成为由网络约束决定的内部变量，也可以被选为输出。只有加入相应独立储能动态时，才应把某个电压认作状态；一个变量可测量或可作为输出，并不自动意味着它是状态。

输出是模型选择观察的量，例如 $y=i$ 或 $y=[i,v_{\mathrm{PCC}}]^T$，不是“物理上流出装置的东西”。

## 4. 数值例子

令：

$$
L=0.01\ \mathrm{H},
\qquad
R=0.1\ \Omega,
\qquad
i=10\ \mathrm{A}
$$

若：

$$
v_c-v_g=5\ \mathrm{V}
$$

则：

$$
Ri=1\ \mathrm{V}
$$

所以净电感电压为：

$$
v_L=5-1=4\ \mathrm{V}
$$

于是：

$$
\frac{di}{dt}
=
\frac{4}{0.01}
=400\ \mathrm{A/s}
$$

若在 $1\ \mathrm{ms}$ 内斜率近似不变：

$$
\Delta i\approx400\times0.001=0.4\ \mathrm{A}
$$

电流约从 $10\ \mathrm{A}$ 增加到 $10.4\ \mathrm{A}$。

## 5. 为什么有限电压下电感电流连续

由：

$$
v_L=L\frac{di}{dt}
$$

若电流在零时间内发生有限跳变，$di/dt$ 必须趋于无穷大，因此所需电感电压也必须趋于无穷大。

在有限电压模型下：

$$
i_L(t_0^-)=i_L(t_0^+)
$$

能量关系也给出同样直觉：

$$
W_L=\frac{1}{2}Li^2
$$

若有限能量变化在 $\Delta t\rightarrow0$ 内完成，平均功率 $\Delta W/\Delta t$ 将趋于无穷大。

“电感电流绝对不能跳变”仍不够严格。若数学模型允许理想冲激电压：

$$
v_L(t)=K\delta(t-t_0)
$$

则对两侧积分可得：

$$
\Delta i=\frac{K}{L}
$$

所以准确说法是：有限电压不能使理想电感电流发生有限的瞬时跳变。

## 6. 储能、耗散与接口滤波

### 6.1 电感决定电流变化速度尺度

由：

$$
\frac{di}{dt}=\frac{\Delta v}{L}
$$

对相同净电压：

$$
L\uparrow
\quad\Longrightarrow\quad
|\dot{i}|\downarrow
$$

较大电感通常：

- 降低开关电流纹波；
- 降低故障或指令变化时的电流斜率；
- 使电流动态变慢；
- 需要更大电压裕量才能实现相同快速响应；
- 增加磁性元件体积、成本和损耗。

因此 $L$ 不是越大越好，而是在纹波、动态、电压裕量和硬件代价之间折中。

### 6.2 电阻提供耗散

若：

$$
v_c-v_g=0
$$

则：

$$
\dot{i}=-\frac{R}{L}i
$$

电流按指数规律自然衰减。电阻将磁场能量转化为热，并在简化模型中提供阻尼。

在常值净电压 $\Delta V=v_c-v_g>0$、$i(0)=0$ 的 RL 例子中，电流增大导致 $Ri$ 增大，留给电感的净电压 $\Delta V-Ri$ 变小，因此正向电流斜率逐渐降低，最后趋向 $i_\infty=\Delta V/R$。若初始电流高于目标，斜率则为负，电流从上方衰减到目标。

若只剩理想电阻与恒压源，则 $i=\Delta V/R$ 是代数关系，没有电感储能状态和逼近过程。这与 RL 的渐变响应并不矛盾；两者保留的物理动态不同。

能量关系为：

$$
\frac{d}{dt}\left(\frac12Li^2\right)
=i(v_c-v_g)-Ri^2
$$

其中 $Ri^2$ 是真实热耗散。通过很大的串联电阻限制高功率电流会付出较大损耗，所以实际接口通常依靠电感与控制，并处理已有的电阻和阻尼需求。

### 6.3 电容缓冲的是电压变化

理想电容满足：

$$
i_C=C\dot v_C,
\qquad
W_C=\frac12Cv_C^2
$$

有限电流下，电容电压不能发生有限的瞬时跳变。DC-link 电容提供短时能量缓冲，滤波电容为高频分量提供支路；基波下也会交换无功。

频域中的 $Z_L=j\omega L$、$Z_C=1/(j\omega C)$ 说明相位与频率选择性，但元件的基础作用仍是储能以及约束 $di/dt$ 或 $dv/dt$，不只是“调相”。

### 6.4 RL 是一阶接口模型

滤波电感、变压器漏感及适当频段下的等效线路电感，可合并成 $L$；绕组与电缆的电阻等可合并成 $R$。在 $v_g$ 固定、零初始条件下，从变流器电压增量到支路电流的 plant 为：

$$
G(s)=\frac{I(s)}{V_c(s)}
=\frac{1}{Ls+R}
$$

这是电流控制的经典一阶起点，不是完整电站所有支路的动态模型。

### 6.5 标准稳定一阶响应为什么没有超调

常值 $\Delta V$ 下：

$$
i(t)=i_\infty+(i_0-i_\infty)e^{-t/\tau},
\qquad
\tau=\frac{L}{R}
$$

因此：

$$
\dot i(t)=\frac{i_\infty-i_0}{\tau}e^{-t/\tau}
$$

在 $R,L>0$ 时，指数因子始终为正，导数符号不变，响应从初值单调逼近最终值。若一开始高于最终值，它会向下逼近；这不是由振荡造成的超调。

标准一阶 $G(s)=K/(\tau s+1)$ 在 $K,\tau>0$、零初始条件和正阶跃下同样单调。这个结论不能推广到任意带零点、非线性或多状态闭环。加入 PI 积分器、延迟、LCL 或控制交互后，完整电流环可能呈现更高阶响应与超调；裸 plant 是一阶不保证闭环也如此。相关阶跃与极点分析见 [极点、零点、带宽与稳态误差](../foundations/control/poles-zeros-bandwidth-and-steady-state-error.md)。

### 6.6 LCL 增强高频滤波并引入谐振

一个简化的每相或单轴等值为：

~~~text
Converter ── L1 ──●── L2 ── grid-side port
                  │
                  Cf
                  │
          equivalent reference
~~~

这只是等值图；真实三相电容组可采用不同星形、三角形及接地安排，不能由这张图推断硬件必须有接地中性线。

高频下，串联电感阻抗增大，并联电容阻抗减小，开关纹波更难进入网侧。与单个 L 滤波相比，LCL 可以在较高频段提供更强网侧电流衰减，但有三个储能状态和谐振动态。

忽略电阻，以电容电压为 $v_f$，两侧电流为 $i_1,i_2$：

$$
L_1\dot i_1=v_c-v_f,
\qquad
C_f\dot v_f=i_1-i_2,
\qquad
L_2\dot i_2=v_f-v_g
$$

这说明 $i_1$ 与 $i_2$ 并非同一个电流，电容支路承担两者之差。控制目标与测量点必须说明是变流器侧还是网侧。

对理想固定网侧电压、零初始条件的增量通道：

$$
\left.\frac{I_2(s)}{V_c(s)}\right|_{V_g(s)=0}
=
\frac{1}{L_1L_2C_fs^3+(L_1+L_2)s}
$$

无阻尼自然谐振频率为：

$$
\omega_{\mathrm{res}}
=\sqrt{\frac{L_1+L_2}{L_1L_2C_f}}
$$

这里 $V_g(s)=0$ 表示该通道中外部电压扰动置零，不是把运行中的电网物理断电。理想模型的高频幅值按 $1/\omega^3$ 衰减；谐振附近则不能直接套用这个高频近似。

实际设计需要电阻或有源阻尼，并考虑网侧等效电感变化、采样和 PWM 延迟。关于网侧电流通道与谐振阻尼，可参考 [imperix 的 LCL 有源阻尼说明](https://imperix.com/doc/implementation/active-damping-of-lcl-filters)。

## 7. 稳态条件必须结合坐标系

在 DC 或单轴常值模型中，若目标是保持恒定电流：

$$
\dot{i}=0
$$

则：

$$
v_c=v_g+Ri
$$

净电感电压为零。

这一结论不能直接照搬到 abc 正弦稳态。若：

$$
i_a(t)=I\cos\omega t
$$

则即使处于稳态，仍有：

$$
\frac{di_a}{dt}\neq0
$$

因此交流稳态仍需要非零电感压降。在同步 dq 坐标中，$i_d$、$i_q$ 可以成为常量，但原来的正弦导数会转化为 $\omega L$ 交叉耦合项。

## 8. 控制器如何让电流跟踪参考

设电流参考为 $i^*$，测量电流为 $i$，误差为：

$$
e=i^*-i
$$

若 $i^*>i$，控制器应产生合适的电压指令，使：

$$
v_c-v_g-Ri>0
$$

于是：

$$
\dot{i}>0
$$

电流从当前状态连续上升并逐渐接近参考。控制器真正选择的是状态的运动方向和速度，而不是直接覆盖状态值。

基本闭环为：

~~~text
i* → error → current controller → vc* → modulation → vc
 ↑                                               ↓
 └──────────────────── current i ← RL plant ─────┘
~~~

## 9. PI 电流控制与前馈

PI 控制器为：

$$
u_{\mathrm{PI}}
=
K_p e+K_i\int e\,dt
$$

一种单轴概念结构为：

$$
v_c^*
=
v_g+Ri+u_{\mathrm{PI}}
$$

其中：

- $v_g$：电网电压前馈；
- $Ri$：电阻压降补偿；
- $u_{\mathrm{PI}}$：生成所需电感动态压降。

若参数和前馈准确：

$$
L\dot{i}\approx u_{\mathrm{PI}}
$$

比例项根据当前误差立即调整电压，积分项积累历史误差并消除恒定扰动下的稳态偏差。积分器本身是一个控制器状态，因此饱和时必须关注 windup。

PI current controller 是控制算法模块，通常运行在 DSP、MCU 或 FPGA 等控制平台上。完整变流器还包含 DC-link、功率桥、驱动、测量、滤波、调制、保护和热管理，不能把一个 PI 框直接等同于整台 converter。

### 9.1 Plant 的边界决定 PI 所见动态

回顾补充：Plant 指被控制的物理过程，不是“整个电网”的专有名称。净电压 $u=v_c-v_g$ 到电流的 RL 通道为 $1/(Ls+R)$；controller、plant 与反馈共同组成闭环系统。把 PWM、测量或电网状态纳入模型时，应明确新增动态属于哪个框。

本节含 $Ri$ 状态补偿的理想结构，使 PI 所见对象近似成为 $1/(Ls)$，不是未经补偿的 $1/(Ls+R)$。取积分状态 $\dot\xi=i^*-i$，该补偿结构的参考到电流通道为：

$$
T(s)=\frac{K_p s+K_i}{Ls^2+K_p s+K_i}.
$$

若只使用 $v_g$ 前馈、不补偿 $Ri$，则：

$$
T(s)=\frac{K_p s+K_i}{Ls^2+(R+K_p)s+K_i}.
$$

后者才对应经典 $K_i/K_p=R/L$ 的 RL 极点匹配。两种结构都包含电感电流和 PI 积分状态，通常是二阶；参数设计不能跨结构直接套用。完整推导、精确相消后的最简阶数与带宽条件见 [理想 RL 电流环 PI 起点](../foundations/control/poles-zeros-bandwidth-and-steady-state-error.md#26-理想-rl-电流环-pi-起点)。

## 10. 从期望电流斜率反解电压

若希望电流按指定斜率变化：

$$
\dot{i}=\dot{i}_{\mathrm{des}}
$$

由 plant 方程可反解：

$$
v_c^*
=
v_g+Ri+L\dot{i}_{\mathrm{des}}
$$

这三个电压分量分别用于：

- 跟随外部电网电压；
- 补偿电阻压降；
- 在电感上形成推动目标动态的净电压。

该表达式揭示了 feedforward、模型补偿和 current-loop control law 的共同物理基础。

$v_g$ 是端口电压前馈，使变流器先合成与网侧相应的电压；$Ri$ 补偿支路电阻压降；$L\dot i_{\mathrm{des}}$ 为期望斜率留下净电感电压。这是合成同一个 $v_c^*$ 的三个分量，而不是向电网另外“补送一份电压”。在闭环中，期望斜率还应随跟踪误差调整，不能只凭参考导数保证消除初始误差或模型偏差。

## 11. 电压上限决定最大电流斜率

变流器由有限 DC-link 电压供电，调制能够生成的交流电压存在上限：

$$
|v_c|\le v_{c,\max}
$$

因此：

$$
\dot{i}
=
\frac{v_c-v_g-Ri}{L}
$$

也存在可实现的最大正、负斜率。

当电流指令变化过快、电网电压较高、DC-link 电压较低或故障电压突变时，电压指令可能饱和。此时：

- 理想线性电流环假设失效；
- 积分器可能继续累积误差；
- 实际电流跟踪速度受物理电压裕量限制；
- 需要限幅、anti-windup 和故障模式控制。

## 12. 平均模型与开关硬件

平均模型把 $v_c$ 当成近似连续可控量；实际实现需要把电压参考转成调制与门极指令，最终由功率桥的离散开关状态产生电压。

PWM 或 SVPWM 利用开关周期内的平均效果实现电压指令 $v_c^*$。因此需要区分：

$$
\text{控制层：生成 }v_c^*
$$

与：

$$
\text{硬件层：通过开关实现实际 }v_c
$$

采样、计算、PWM 更新和功率器件都会引入延迟、误差与非理想约束。

### 12.1 算法、调制参数与硬件开关的层级

~~~text
current controller
       ↓
voltage reference vc*
       ↓
PWM / SVPWM
       ↓
duty ratios and state durations / switching sequence
       ↓
gate driver → semiconductor switching
       ↓
switched pole voltages → averaged / fundamental voltage
~~~

| 概念 | 含义 |
|---|---|
| IGBT、MOSFET、SiC MOSFET | 实际功率半导体器件；SiC 本身是材料体系 |
| Switching state | 某时刻功率桥所处的离散组合，例如两电平桥的 $(S_a,S_b,S_c)$ |
| Duty cycle | 一个开关周期内某状态的持续时间比例 |
| Modulation index | 电压指令相对调制基准的归一化程度，定义依具体方法而定 |
| Switching sequence | 一个周期内各离散状态的排列与持续时间 |

这些概念属于不同层级，不是互相替代的五种控制方法。SVPWM 等方法可实现相同的平均电压矢量，但不同开关序列仍可能产生不同的开关损耗、谐波和共模特性。

### 12.2 占空比为什么能改变平均电压

以理想半桥极点对 DC 中点的电压为例，若在 $+V_{dc}/2$ 与 $-V_{dc}/2$ 之间切换，正状态占比为 $d$：

$$
\bar v_{\mathrm{pole}}
=d\frac{V_{dc}}{2}
+(1-d)\left(-\frac{V_{dc}}{2}\right)
=\frac{(2d-1)V_{dc}}{2}
$$

因此改变占空比就改变周期平均极点电压。例如 $V_{dc}=800\ \mathrm V$、$d=0.6$ 时，平均极点电压为 $80\ \mathrm V$。

这是忽略死区、器件压降并假设周期内 DC 电压近似不变的简化关系。极点对 DC 中点电压不自动等于实际负载相对中性点电压；三相相电压还取决于共模与连接方式。调制指数也不能直接与某一桥臂的 duty ratio 画等号。

## 13. BESS PCS 控制层级

典型控制链可写成：

~~~text
P*, Q*
  ↓
outer loop
  ↓
id*, iq*
  ↓
current controller
  ↓
vd*, vq*
  ↓
modulation and switching
  ↓
converter voltage
  ↓
filter and grid
  ↓
id, iq
~~~

完整物理因果关系为：

$$
\text{controller}
\rightarrow
v_c^*
\rightarrow
\text{PWM}
\rightarrow
\text{switching}
\rightarrow
v_c
\rightarrow
v_L
\rightarrow
\dot{i}
\rightarrow
i
$$

上层 P/Q 控制并没有绕过电流动态，而是通过电流参考和电压指令逐层作用到物理 plant。

## 14. 与本文 Park 约定一致的 dq 电流方程

采用 [Clarke 与 Park 坐标变换](../foundations/electrical-engineering/clarke-and-park-transformations.md) 中的约定：

$$
\mathbf{i}_{dq}=P(\theta)\mathbf{i}_{\alpha\beta},
\qquad
P(\theta)=R(-\theta)
$$

静止坐标中的 RL 方程为：

$$
L\dot{\mathbf{i}}_{\alpha\beta}
=
\mathbf{v}_{c,\alpha\beta}
-\mathbf{v}_{g,\alpha\beta}
-R\mathbf{i}_{\alpha\beta}
$$

由于 Park 矩阵随时间变化：

$$
\dot{\mathbf{i}}_{dq}
=
\dot{P}\mathbf{i}_{\alpha\beta}
+P\dot{\mathbf{i}}_{\alpha\beta}
$$

令 $\omega=\dot{\theta}$，按当前 convention 整理后得到：

$$
L\frac{di_d}{dt}
=
v_{c,d}
-v_{g,d}
-Ri_d
+\omega Li_q
$$

$$
L\frac{di_q}{dt}
=
v_{c,q}
-v_{g,q}
-Ri_q
-\omega Li_d
$$

状态、控制输入和扰动分别为：

$$
\mathbf{x}=
\begin{bmatrix}
i_d\\
i_q
\end{bmatrix},
\qquad
\mathbf{u}=
\begin{bmatrix}
v_{c,d}\\
v_{c,q}
\end{bmatrix},
\qquad
\mathbf{d}=
\begin{bmatrix}
v_{g,d}\\
v_{g,q}
\end{bmatrix}
$$

## 15. dq 解耦补偿

d 轴方程含有 $+\omega Li_q$，q 轴方程含有 $-\omega Li_d$，所以两轴动态并非天然独立。

若希望：

$$
L\dot{i}_d=u_d,
\qquad
L\dot{i}_q=u_q
$$

按本文符号约定，可以选择：

$$
v_{c,d}^*
=
v_{g,d}
+Ri_d
-\omega Li_q
+u_d
$$

$$
v_{c,q}^*
=
v_{g,q}
+Ri_q
+\omega Li_d
+u_q
$$

其中 $u_d$、$u_q$ 可由 PI 控制器生成。理想模型下，电网电压前馈、电阻补偿和交叉耦合补偿被抵消后，两轴分别近似为积分环节。

解耦项的正负号不是可以脱离 convention 背诵的固定公式。若 Park 定义、q 轴方向、电压方向或电流正方向改变，必须重新推导。

### 15.1 两种电阻处理方式对应不同 plant

上述结构也补偿了 $Ri_d,Ri_q$，理想每轴 plant 因而为 $1/(Ls)$。另一种常用结构仅补偿电网电压与旋转交叉项：

$$
v_{c,d}^*=v_{g,d}-\omega Li_q+u_d
$$

$$
v_{c,q}^*=v_{g,q}+\omega Li_d+u_q
$$

这时每轴保留：

$$
L\dot i_d+Ri_d=u_d,
\qquad
L\dot i_q+Ri_q=u_q
$$

对应 plant 为 $1/(Ls+R)$。已有的 [RL 电流环 PI 设计](../foundations/control/poles-zeros-bandwidth-and-steady-state-error.md) 中 $K_p=L\omega_c$、$K_i=R\omega_c$ 使用的正是保留 $R$ 的 plant。更改补偿结构后，必须重新检查 PI 与闭环模型，不能把两种 plant 混用。

## 16. 从电流动态到 P/Q 控制

当 d 轴与电网电压向量对齐时：

$$
v_{g,q}=0
$$

在本文 amplitude-invariant convention 下：

$$
p=\frac{3}{2}v_{g,d}i_d
$$

若取电流由变流器流向电网为正，定义向电网送出的 P/Q 为正，并使用本文 Park 约定，则无零序正弦条件下：

$$
P=\frac{3}{2}(v_{g,d}i_d+v_{g,q}i_q)
$$

$$
Q=\frac{3}{2}(v_{g,q}i_d-v_{g,d}i_q)
$$

在 $v_{g,q}=0$ 时：

$$
P=\frac{3}{2}v_{g,d}i_d,
\qquad
Q=-\frac{3}{2}v_{g,d}i_q
$$

这里 dq 幅值对应相量的峰值尺度，不能再把 RMS 额定值直接代入 $3/2$ 公式。改变功率方向、Park 或 q 轴约定时，必须同步改变符号。增加有功指令的典型因果链为：

$$
P^*
\rightarrow
i_d^*
\rightarrow
v_{c,d}^*
\rightarrow
\dot{i}_d
\rightarrow
i_d
\rightarrow
P
$$

因此外环带宽通常应显著低于电流内环，才能在设计上形成清晰的时间尺度分离。

### 16.1 常见控制目标与电流能力

| 控制目标 | 典型作用路径 | 主要条件 |
|---|---|---|
| $P\to P^*$ | 有功外环生成 $i_d^*$ | 电压对齐、功率方向与单位一致 |
| $Q\to Q^*$ | 无功外环生成 $i_q^*$ | 无功符号与变换一致 |
| $V_{\mathrm{PCC}}\to V_{\mathrm{PCC}}^*$ | 电压外环调节无功电流或无功指令 | 响应受电网阻抗及控制模式影响 |
| $V_{dc}\to V_{dc}^*$ | DC-link 外环调节有功电流 | 恢复 AC/DC 功率平衡，方向由充放电工况决定 |
| 电流限制 | 约束 $i_d^*,i_q^*$ 并分配优先级 | 需要配合电压限幅和 anti-windup |
| 动态阻尼 | 调整环路与阻尼控制 | 同时检查裕度、延迟和电网交互 |

P/Q 与电压外环是不同运行模式下的选择，不能把相互竞争的外环都当成独立硬约束。在高 X/R 网络中，无功对电压的影响通常更突出；一般阻抗下，有功与无功都可能影响 PCC 电压。

对平衡、无零序、amplitude-invariant 的电流表示，常用限制为：

$$
\sqrt{i_d^2+i_q^2}\le I_{\max}
$$

其中 $I_{\max}$ 应使用与 dq 相同的峰值尺度。该圆形约束对平衡基波对应逐相峰值限制；不平衡时还必须检查正负序叠加后的实际每相电流，四线系统还需包含零序。故障电压降低时，维持原 P/Q 所需电流可能超出能力，需要明确有功与无功优先策略。

## 17. 并网接口、节点功率与弱电网

### 17.1 PCC 电压与远端电源电压

PCC 是 Point of Common Coupling，即公共耦合点。$V_{\mathrm{PCC}}$ 是该点的实际测量电压；具体使用相电压、线电压、RMS 或空间矢量幅值时，都应明确测量定义。

~~~text
Converter ── filter / transformer ── PCC ── Zg ── Eg
                                    → I
~~~

在电流由 PCC 流向远端电网为正的单频 Thevenin 等值中：

$$
\underline V_{\mathrm{PCC}}
=
\underline E_g+Z_g\underline I
$$

改变电流正方向后压降符号随之改变。核心是实际 PCC 电压包含电流经过电网阻抗产生的电压变化，一般不等于远端电源电压。PCC 与项目中的 POI（Point of Interconnection）是否重合，应按具体接线与项目定义确认。

这里 $\underline I$ 是流入所建模外部网络的端口电流。若 PCC 还有显式本地负载，它就不能自动等于变流器电流，两者之差要由节点 KCL 决定。

POI 是 Point of Interconnection，即接入点。一个项目可能存在以下节点关系：

~~~text
PCS vc → filter → local bus → transformer / collector network → POI → utility
~~~

哪个节点定义为 PCC，需要根据项目单线图、研究模型和接口定义确认，不能从这张通用图强行指定。

| 符号 | 本文含义 |
|---|---|
| $v_c$ | 变流器 AC 端合成电压 |
| $v_g$ | 选定 RL 支路的实际网侧端口电压 |
| $v_{\mathrm{PCC}}$ | 项目定义的公共耦合点实际电压 |
| $v_{\mathrm{POI}}$ | 接入点实际电压 |
| $\underline E_g$ | 外部网络 Thevenin 等值中的远端源项 |

Thevenin 等效由源项与端口阻抗一起描述外部网络，并不是把“发电机电压”和“负载电压”直接相加。对非线性或受控网络，固定等效通常只在指定工况与频段附近有效；需要更完整动态时应保留相应网络与控制状态。

### 17.2 PLL 与电流的闭环交互

在强电网近似中，$v_{\mathrm{PCC}}$ 常被视为刚性扰动。电网阻抗较大时，电流变化会明显改变 PCC 电压，其幅值与相角又进入 PLL 和控制器：

$$
i
\rightarrow
v_{\mathrm{PCC}}
\rightarrow
\text{PLL and control}
\rightarrow
v_c
\rightarrow
i
$$

此时 $v_g$ 不再是与变流器状态无关的理想外部量，而成为闭环交互的一部分。这条反馈链会继续通向：

- PLL 弱电网稳定性；
- dq 阻抗与频率扫描；
- 变流器—电网控制交互；
- GFL/GFM 小信号模型；
- 故障限流与恢复。

这也解释了为什么 PLL 属于变流器—电网动态模型的一部分。具体角度反馈结构见 [PLL 与 SRF-PLL](../ibr-grid-integration/pll-and-srf-pll.md)。

### 17.3 注入节点与本地负载

将变流器电流 $i_c$、电网进口电流 $i_{\mathrm{grid,in}}$ 都定义为流入母线，负载电流 $i_{\mathrm{load}}$ 为流出母线：

~~~text
converter ic → bus ← igrid,in external grid
                ↓
              iload
~~~

则：

$$
i_c+i_{\mathrm{grid,in}}=i_{\mathrm{load}}
$$

BESS 向这个节点注入或吸收电流，其他网络支路同时重新满足 KCL/KVL。它是可控功率端口，不是一根只能供给某个指定负载的专线。网侧出口方向与上式进口方向相反，写电网阻抗压降时必须同步转换符号。

若母线电压近似刚性且负载阻抗固定：

$$
\underline I_{\mathrm{load}}
=\frac{\underline V_{\mathrm{bus}}}{Z_{\mathrm{load}}}
$$

改变 BESS 注入时，负载电流可能近似不变，主要改变的是电网支路的净电流。若母线电压因网络阻抗而改变，负载电流也可能跟随变化。恒功率负载的响应又不同，因此不能仅凭“强网/弱网”而不说明负载模型。

### 17.4 功率分担与能量

取 $P_{\mathrm{BESS}}>0$ 为放电注入，$P_{\mathrm{grid,in}}>0$ 为电网进口。忽略损耗且不考虑母线暂态储能时：

$$
P_{\mathrm{BESS}}+P_{\mathrm{grid,in}}=P_{\mathrm{load}}
$$

对于 10 MW 负载：

| BESS 工况 | $P_{\mathrm{BESS}}$ | 电网净进口 |
|---|---|---|
| 不交换有功 | 0 MW | 10 MW |
| 放电 | +4 MW | 6 MW |
| 充电 | -4 MW | 14 MW |

RL 方程先决定电流动态，电流与端口电压决定 P/Q，再由网络功率平衡决定其余支路的功率：

$$
v_c
\rightarrow
v_L
\rightarrow
\dot i
\rightarrow
i
\rightarrow
P,Q
\rightarrow
\text{network power balance}
$$

功率单位是 MW；能量为 $E=\int P(t)\,dt$，用小时积分可得 MWh。例如 AC 端连续输出 4 MW、持续 1 h，对外送出 4 MWh；电池内部能量变化还需考虑转换与辅助损耗。MW 不能直接当作 MWh，“电量”在这里指能量，也不能与电荷量混淆。

### 17.5 削峰运行与孤岛供电

正常并网削峰时，BESS 与电网并联，放电减少电网看到的净负荷，充电则增加净负荷。改变充放电功率不需要因此切断外部电网。

孤岛或备电是另外的系统工况：本地网络与外部电网分离后，需要某个具备相应能力的源建立电压与频率，并持续维持功率平衡。若由 BESS 承担，还需要足够的能量、功率能力以及适配的控制、保护和切换设计；普通并网注流控制不能自动保证独立供电。

两种状态的控制任务分别是“向已有网络电压下注入/吸收功率”和“在本地网络建立并维持电压、频率”。GFL/GFM 与这类任务的关系继续放在 [IBR and Grid Integration](../ibr-grid-integration/README.md)。

## 18. 常见误区

1. **控制器直接设置电流。** 控制器主要通过电压改变 $\dot{i}$，再让电流沿时间演化。
2. **方程右边的量都是控制输入。** $v_c$ 通常可控，$v_g$ 通常是扰动。
3. **电感电流在任何数学条件下都不能跳变。** 有限电压下连续；理想冲激电压可造成数学上的跳变。
4. **电感越大越好。** 较大电感降低纹波，也会减慢动态并占用电压裕量。
5. **稳态时电感压降一定为零。** abc 正弦稳态仍有 $L\,di/dt$。
6. **平均模型中的连续电压就是硬件直接操纵量。** 硬件最终操纵开关状态和占空比。
7. **PI 输出可以无限增加。** 实际变流器电压受 DC-link 和调制限制。
8. **dq 两轴天然完全解耦。** 旋转坐标系引入 $\omega L$ 交叉项。
9. **解耦公式的符号在所有模型中相同。** 符号取决于坐标和功率方向约定。
10. **最简图里没有负载，说明注入电流不供给负载。** 负载可能位于其他支路或网络等效中，需结合节点平衡理解。
11. **所有 $v_g$ 都代表同一台理想发电机。** 本文是实际支路端口电压；远端源项单独写作 $E_g$。
12. **L 和 C 主要只是调相。** 它们储能、限制变化率并构成频率选择性网络。
13. **裸 RL 是一阶，电流闭环就不可能超调。** PI、延迟与滤波可以增加闭环阶数。
14. **PI controller 就是 converter。** PI 只是控制算法，硬件、调制、测量和保护共同组成系统。
15. **削峰放电必须切断电网。** 通常是并联改变净进口，孤岛供电是不同工况。

## 19. 下一步接口

- 从当前 plant 推导 PI 电流环的闭环传递函数；
- 连接极点、时间常数与 current-loop bandwidth；
- 分析采样、计算和 PWM 延迟对相位裕度的影响；
- 加入电压限幅并理解 integrator windup；
- 推导包含电阻、网侧电感变化和主动阻尼的 LCL 模型；
- 进入弱电网下的 PLL、dq 阻抗与控制交互。

## 20. 相关笔记

- [微分方程与动态系统基础](../foundations/mathematics/differential-equations-and-dynamic-systems.md)
- [Laplace 变换与传递函数](../foundations/mathematics/laplace-transform-and-transfer-functions.md)
- [极点、零点、带宽与稳态误差](../foundations/control/poles-zeros-bandwidth-and-steady-state-error.md)
- [Clarke 与 Park 坐标变换](../foundations/electrical-engineering/clarke-and-park-transformations.md)
- [复功率与 BESS PCS 的 P-Q 能力](../foundations/electrical-engineering/complex-power.md)
- [PLL 与 SRF-PLL](../ibr-grid-integration/pll-and-srf-pll.md)
- [对称分量、零序与接地](../power-systems/symmetrical-components-zero-sequence-and-grounding.md)
- [IBR and Grid Integration](../ibr-grid-integration/README.md)
- [BESS 系统结构与控制](../bess/README.md)
