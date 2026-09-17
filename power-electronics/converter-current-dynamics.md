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

这里把 $v_c$ 视为控制输入，是因为平均模型假设控制器可以通过调制主动影响变流器端基波电压。$v_g$ 则由外部电网决定，局部变流器通常不能直接命令它。

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

## 6. 电感与电阻的工程作用

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

平均模型把 $v_c$ 当成近似连续可控量，但真实 VSC 最终操纵的是：

- IGBT、MOSFET 或 SiC 器件的开关状态；
- duty cycle；
- modulation index；
- switching sequence。

PWM 或 SVPWM 利用开关周期内的平均效果实现电压指令 $v_c^*$。因此需要区分：

$$
\text{控制层：生成 }v_c^*
$$

与：

$$
\text{硬件层：通过开关实现实际 }v_c
$$

采样、计算、PWM 更新和功率器件都会引入延迟、误差与非理想约束。

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

## 16. 从电流动态到 P/Q 控制

当 d 轴与电网电压向量对齐时：

$$
v_{g,q}=0
$$

在本文 amplitude-invariant convention 下：

$$
p=\frac{3}{2}v_{g,d}i_d
$$

无功与 $i_q$ 的具体正负关系取决于功率方向和 q 轴约定。增加有功指令的典型因果链为：

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

## 17. 弱电网为什么使问题更复杂

在强电网近似中，$v_g$ 常被视为刚性扰动。弱电网中，变流器电流流过电网阻抗会反过来改变并网点电压：

$$
i
\rightarrow
v_{\mathrm{POI}}
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

## 19. 下一步接口

- 从当前 plant 推导 PI 电流环的闭环传递函数；
- 连接极点、时间常数与 current-loop bandwidth；
- 分析采样、计算和 PWM 延迟对相位裕度的影响；
- 加入电压限幅并理解 integrator windup；
- 把 RL 滤波器扩展到 LCL 滤波器；
- 进入弱电网下的 PLL、dq 阻抗与控制交互。

## 20. 相关笔记

- [微分方程与动态系统基础](../foundations/mathematics/differential-equations-and-dynamic-systems.md)
- [Clarke 与 Park 坐标变换](../foundations/electrical-engineering/clarke-and-park-transformations.md)
- [复功率与 BESS PCS 的 P-Q 能力](../foundations/electrical-engineering/complex-power.md)
- [IBR and Grid Integration](../ibr-grid-integration/README.md)
