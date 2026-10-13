# 频域稳定性与阻抗分析接口：从灵敏度到弱电网 MIMO

本文连接局部电流环分析与变流器—电网互联稳定性。Nyquist、鲁棒性积分和 generalized Nyquist 只建立必要概念、公式与前提，不把研究入口当作已经完成的完整证明。

## 1. Nyquist 与最坏灵敏度

### 1.1 不只在某一个交越点看裕度

对负反馈 SISO 环路，特征条件是：

$$
1+L_{\mathrm{loop}}(s)=0.
$$

Nyquist 利用环路频响对 $-1$ 的环绕与开环 RHP 极点数量，判断闭环 RHP 根。PM、GM 是经典局部截面指标，不能单独覆盖开环不稳定、多次交越或隐藏模态。

一种明确的方向约定是：右半平面 Nyquist 轮廓按顺时针走，定义像曲线对 $-1$ 的逆时针净环绕数为 $N$。若轮廓上无相关零极点，则：

$$
Z=P-N,
$$

其中 $P$ 是环路在轮廓内的极点数，$Z$ 是 $1+L_{\mathrm{loop}}$ 的零点数，均计重数。闭环无 RHP 根要求 $Z=0$。采用相反环绕正号时公式也要随之改变，不能混用。

虚轴积分极点需要缩进轮廓，临界点穿越需要另行处理。内部稳定性还要排除不稳定相消和不可见模态，而不是只数约简后的通道根。

### 1.2 Ms 是到临界点最小距离的倒数

稳定闭环的灵敏度为：

$$
S(s)=\frac1{1+L_{\mathrm{loop}}(s)},
\qquad
M_s=\sup_{\omega\ge0}|S(j\omega)|.
$$

因此：

$$
|S(j\omega)|=\frac1{|1+L_{\mathrm{loop}}(j\omega)|}.
$$

$|1+L_{\mathrm{loop}}|$ 是 Nyquist 曲线到 $-1$ 的距离。曲线靠近临界点时，灵敏度可能出现大峰值；$M_s$ 补充了只在交越处报告 PM/GM 的不足。

例如环路在某频率取 $-0.8+j0.1$，到 $-1$ 的距离约 0.224，该点 $|S|\approx4.47$。这只是该频率的值，不能冒充已搜索全频率得到的 $M_s$。

有限或较小的频率扫描峰值不自动证明内部稳定，也不等于所有不确定性集合下的鲁棒稳定证明。先确认名义稳定、扫描范围及模型完整性，再解释性能指标。

## 2. 频域性能限制与非最小相位

### 2.1 Bode 灵敏度积分的一个充分背景

在连续时间、实系数有理 SISO 环路中，若开环与闭环均稳定，无虚轴奇点或不稳定相消，且 $L_{\mathrm{loop}}$ 高频衰减足够快（相对阶至少 2），有零积分形式：

$$
\int_0^\infty\ln|S(j\omega)|\,d\omega=0.
$$

相对阶是约简后分母次数减分子次数。积分沿线性的角频率 $d\omega$，使用自然对数，不是直接比较 Bode 对数横轴上肉眼看到的两块面积。

在这些条件下，一段频率 $|S|<1$ 的抑制，需要在其他频段用 $|S|>1$ 补偿，这就是 waterbed effect。更一般的 RHP 极点、相对阶和时延条件对应其他积分形式；此处不展开完整定理。条件与一般形式可继续阅读 [Bode Integral: A Unifier of Control-Relevant Integral Relations](https://scholars.cityu.edu.hk/files/132010996/122933843.pdf)。

### 2.2 为什么不能机械套到纯积分环路

课程的 $L_{\mathrm{loop}}=\omega_0/s$ 有原点极点，相对阶为 1，不满足上述简单零积分版本的充分条件。该例：

$$
S(s)=\frac{s}{s+\omega_0},
\qquad
|S(j\omega)|=\frac{\omega}{\sqrt{\omega^2+\omega_0^2}}.
$$

有限正频率处均小于 1，随着频率增大趋于 1，$M_s=1$ 为上确界。这不是对零积分水床定理的反例，而是提醒使用定理前必须核验结构和收敛条件。

### 2.3 相同幅值可以对应不同相位

取 $z>0$，比较 LHP 与 RHP 零点因子：

$$
F_+(s)=1+s/z,
\qquad
F_-(s)=1-s/z.
$$

两者在虚轴上的幅值相同：

$$
|F_+(j\omega)|=|F_-(j\omega)|=\sqrt{1+(\omega/z)^2}.
$$

相位却分别为：

$$
\angle F_+(j\omega)=+\arctan(\omega/z),
\qquad
\angle F_-(j\omega)=-\arctan(\omega/z).
$$

将它们分别配上同一个足够高阶的稳定极点分母，得到 proper 系统后，幅值相同、相位不同的关系仍成立。RHP 零点是 non-minimum-phase 特征，会限制相位和可实现速度，并可能造成 inverse response。

RHP 零点不是 RHP 极点；它不直接意味着裸系统不稳定。也不能把非最小相位限制靠不稳定极零相消安全地“删除”。纯时延同样能改变相位而不改变幅值，因此 magnitude-only 比较不足以判断反馈性能。

## 3. 从 RL 到 LCL 与弱电网

### 3.1 LCL 增强滤波也增加动态

理想无损单轴 LCL 保留三个状态：

$$
L_1\dot i_1=v_c-v_f,
\qquad
C_f\dot v_f=i_1-i_2,
\qquad
L_2\dot i_2=v_f-v_g.
$$

令网侧电压增量为零、各初始增量为零，从变流器电压到网侧电流的通道为：

$$
G_{\mathrm{LCL}}(s)
=\left.\frac{I_2(s)}{V_c(s)}\right|_{V_g=0}
=\frac1{L_1L_2C_fs^3+(L_1+L_2)s}.
$$

非零理想谐振频率为：

$$
\omega_{\mathrm{res}}=\sqrt{\frac{L_1+L_2}{L_1L_2C_f}}.
$$

这里 $V_g=0$ 是该增量通道的外部扰动置零，不是把真实电网电压设为零。无损模型含原点与虚轴共轭极点，不是稳定一阶低通；谐振附近不能只用高频衰减解释响应。

高频网侧电流衰减更强，但损耗、被动/有源阻尼、数字延迟、电流测量位置与网侧电感变化都需纳入设计。完整物理模型见 [并网变流器电流动态](../power-electronics/converter-current-dynamics.md#66-lcl-增强高频滤波并引入谐振)；工程实现可参考 [imperix LCL 有源阻尼说明](https://imperix.com/doc/implementation/active-damping-of-lcl-filters)。

### 3.2 弱电网增加了另一条反馈链

较刚性电网中，PCC 电压可近似作为外部扰动。电网端口阻抗的影响不可忽略时，电流注入会改变 PCC 电压，进而进入 PLL 和控制器：

~~~text
converter current → PCC voltage → PLL / controller
        ↑                              ↓
        └──── filter current ← converter voltage
~~~

此时单个一阶 RL 不再覆盖所有反馈路径。应扩大模型边界，明确电网阻抗、同步估计、电压前馈与电流控制的耦合，而非只给局部电流环报一个正 PM。

相关端口定义见 [PCC 与远端源项](../power-electronics/converter-current-dynamics.md#171-pcc-电压与远端电源电压)，同步耦合见 [PLL 与 SRF-PLL](pll-and-srf-pll.md)。

## 4. dq 导纳与矩阵回路接口

### 4.1 非对角项表示轴间耦合

固定工作点、兼容同步坐标系下，可建立小信号关系：

$$
\begin{bmatrix}\Delta i_d\\\Delta i_q\end{bmatrix}
=
\underbrace{\begin{bmatrix}
Y_{dd}(s)&Y_{dq}(s)\\
Y_{qd}(s)&Y_{qq}(s)
\end{bmatrix}}_{\mathbf Y_{\mathrm{conv}}(s)}
\begin{bmatrix}\Delta v_d\\\Delta v_q\end{bmatrix}.
$$

非对角项描述 d/q cross-coupling。分别看两个对角 SISO 通道的 PM，不能自动保证整体 MIMO 稳定。强不平衡、周期时变与多频耦合时，固定 $2\times2$ dq LTI 模型也可能不够，需要序域、多频或谐波状态空间模型。

### 4.2 端口方向决定互联符号

为明确给出加号形式，本小节采用小信号电流正方向为“从电网流入变流器”，与电流动态笔记中向电网注入为正的约定不同。定义 $\Delta\mathbf i=\mathbf Y_{\mathrm{conv}}\Delta\mathbf v$，电网端口关系为：

$$
\Delta\mathbf v=\Delta\mathbf e_g-\mathbf Z_g\Delta\mathbf i.
$$

代入得到：

$$
(\mathbf I+\mathbf Z_g\mathbf Y_{\mathrm{conv}})\Delta\mathbf v
=\Delta\mathbf e_g.
$$

因而可定义无量纲 return-ratio matrix：

$$
\mathbf L(s)=\mathbf Z_g(s)\mathbf Y_{\mathrm{conv}}(s),
\qquad
\det[\mathbf I+\mathbf L(s)].
$$

$\mathbf I$ 是单位矩阵。若导纳定义改为向电网注入电流，符号和方程必须同步改变，不能拿不同方向、dq 坐标或归一化基值的两个模型直接相乘。

### 4.3 Generalized Nyquist 还缺哪些条件

行列式与矩阵频响是互联稳定性分析的入口，不是“写出 $\det(\mathbf I+\mathbf L)$ 就证明稳定”。完整判断还需：

- 两侧端口模型、连接形式、坐标与符号兼容。
- 正确统计矩阵互联的开环不稳定模态，处理虚轴极点。
- 按同一轮廓检查行列式或特征轨迹的适当环绕。
- 检查隐藏模态、相消、内部稳定性与频率扫描覆盖范围。

本节不展开矩阵 argument principle、模型识别和 generalized Nyquist 的完整证明。

## 5. 研究边界与后续学习

### 5.1 已建立的接口与尚未完成的内容

| 接口 | 现在能说明什么 | 后续仍需完成 |
| --- | --- | --- |
| Nyquist | 开环 RHP 极点、临界点和方向约定的重要性 | 完整轮廓与 argument principle 推导 |
| $M_s$ | 灵敏度峰值与临界点距离 | 不确定性模型及鲁棒稳定证明 |
| 水床效应 | 性能分配受结构约束 | 一般积分公式与收敛条件 |
| RHP 零点 | 幅值相同不代表相位相同 | 非最小相位性能界限 |
| LCL | 三状态、谐振与滤波折中 | 离散有源阻尼与参数变化验证 |
| dq 阻抗 | 耦合与端口互联的矩阵结构 | 导纳识别、多频与 generalized Nyquist |

### 5.2 常见误区与自测

- 一个正 PM 不足以证明复杂弱电网互联稳定。
- $M_s$ 不是任意不确定性下的鲁棒稳定证书。
- 灵敏度积分并非对所有环路恒为零。
- RHP 零点与 RHP 极点的含义不同。
- LCL 无损谐振公式不是完整有源阻尼设计。
- d/q 两个对角通道稳定，不等于耦合矩阵互联稳定。

应能用 $1+L_{\mathrm{loop}}$ 解释临界点距离，说明纯积分例为何不能机械套零积分水床公式，并从明确的端口电压电流方向推导 $\mathbf I+\mathbf Z_g\mathbf Y_{\mathrm{conv}}$。

### 5.3 相关笔记

- [控制系统频域分析：Bode、反馈与稳定裕度](../foundations/control/control-frequency-domain-analysis.md)
- [PI 电流环频域设计、时延与级联控制](../power-electronics/current-loop-frequency-domain-design.md)
- [PLL 与 SRF-PLL](pll-and-srf-pll.md)
- [Clarke 与 Park 坐标变换](../foundations/electrical-engineering/clarke-and-park-transformations.md)
- [矩阵与坐标变换](../foundations/mathematics/matrices-and-coordinate-transformations.md)
