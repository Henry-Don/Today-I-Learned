# 控制系统频域分析：Bode、反馈与稳定裕度

频域分析把“不同频率能否跟上、能否滤掉、会不会放大或失稳”连接到同一个反馈模型。本文侧重读图与判据；完整 PI 电流环算例见 [电流环频域设计](../../power-electronics/current-loop-frequency-domain-design.md)。

## 1. 分析对象与符号边界

### 1.1 正弦稳态回答什么问题

对具有稳定正弦稳态的 LTI 模型，取零初始增量定义 $G(s)=Y(s)/U(s)$。持续输入峰值为 $U_m$ 的正弦信号时：

$$
u(t)=U_m\cos\omega t,
\qquad
y_{\mathrm{ss}}(t)=U_m|G(j\omega)|\cos[\omega t+\angle G(j\omega)].
$$

本式使用峰值，不是 RMS。$G(j\omega)$ 描述受迫稳态的幅值缩放和相位变化；原极点与自然暂态并未被删除。不稳定系统即使能形式上计算 $G(j\omega)$，也不能因此声称实际响应最终收敛。

初始条件与完整时域响应的推导见 [Laplace 变换与传递函数](../mathematics/laplace-transform-and-transfer-functions.md)。

### 1.2 Plant、controller 与 loop 的角色

- $G(s)$：选定边界内的被控对象，不专指整个电网。
- $C(s)$：控制器；它与 plant、反馈及其他动态共同组成闭环系统。
- $L_{\mathrm{loop}}(s)$：环路传递函数，避免与电感 $L$ 混淆。
- $R_{\mathrm{ref}}(s)$：参考输入，避免与电阻 $R$ 混淆。
- $T(s)$、$S(s)$：本文约定的闭环跟踪与灵敏度通道。

最简单位负反馈中 $L_{\mathrm{loop}}=CG$；真实模型还可能包含调制、计算、测量及采样环节。先固定输入输出、运行点和端口符号，再画 Bode。

### 1.3 RL 的特征频率

净电压到电流的 RL 模型为：

$$
G(s)=\frac{1}{Ls+R},
\qquad
G(j\omega)=\frac{1}{R+j\omega L},
\qquad L,R>0.
$$

$$
|G(j\omega)|=\frac{1}{\sqrt{R^2+(\omega L)^2}},
\qquad
\angle G(j\omega)=-\arctan\frac{\omega L}{R}.
$$

以 $\omega_p=R/L=1/\tau$ 为参照判断高低频，而不是固定某个 Hz 数字。低频增益约 $1/R$、相位约 $0^\circ$；高频幅值约 $1/(\omega L)$、相位趋向 $-90^\circ$。这是裸 plant，不是 PI 电流闭环。

## 2. Bode 图的构造与读图

### 2.1 幅频、相频和对数坐标

Bode 图分别显示增益与相位，频率横轴通常取对数。一段 decade 是频率乘十倍；10→100 Hz 与 100→1000 Hz 在轴上占相同长度。相位用度或弧度，并采用一致的展开约定，避免把 $\pm180^\circ$ 包裹当作真实跳变。

无量纲幅值比 $M$ 的 dB 表示为：

$$
M_{\mathrm{dB}}=20\log_{10}M.
$$

| 幅值比 | 幅值 dB |
| --- | --- |
| 10 | +20 dB |
| 2 | 约 +6.02 dB |
| 1 | 0 dB |
| $1/\sqrt2$ | 约 -3.01 dB |
| 0.1 | -20 dB |

功率比用 $10\log_{10}$，不能与幅值比混用。RL 的 $G$ 单位是 A/V；若绘制 dB，应明确参考增益 $G_{\mathrm{base}}$，使用 $20\log_{10}(|G|/G_{\mathrm{base}})$。归一化到 $G(0)$ 得到的是相对衰减，不是无单位的绝对 plant 增益。

### 2.2 一阶低通的精确曲线与锚点

对 $K,\tau>0$：

$$
G(s)=\frac{K}{1+s/\omega_p},
\qquad
\omega_p=\frac1\tau.
$$

令 $r=\omega/\omega_p$，相对 DC 增益的幅频与相位为：

$$
M_{\mathrm{rel,dB}}=-10\log_{10}(1+r^2),
\qquad
\phi=-\arctan r.
$$

| $r$ | 相对幅值 dB | 相位 |
| --- | --- | --- |
| 0.01 | 约 0 | 约 $-0.57^\circ$ |
| 0.1 | 约 -0.043 | 约 $-5.71^\circ$ |
| 1 | 约 -3.01 | $-45^\circ$ |
| 10 | 约 -20.04 | 约 $-84.29^\circ$ |
| 100 | 约 -40 | 约 $-89.43^\circ$ |

转折之后的高频渐近斜率为 -20 dB/dec，即每增大十倍频率，幅值约缩小十倍。真实相位平滑变化，不在转折点瞬间跳到 $-90^\circ$。

### 2.3 串联环节为何可以相加

对 $G=G_1G_2$，一致归一化和相位展开下：

$$
20\log_{10}|G|=20\log_{10}|G_1|+20\log_{10}|G_2|,
\qquad
\angle G=\angle G_1+\angle G_2.
$$

这使控制器、plant、滤波器和时延可以分别理解，再合成为总环路。

| 基本因子 | 幅频渐近斜率贡献 | 相位特征 |
| --- | --- | --- |
| 正常数 $K$ | 0 dB/dec | $0^\circ$ |
| 积分器 $1/s$ | -20 dB/dec | $-90^\circ$ |
| 理想微分器 $s$ | +20 dB/dec | $+90^\circ$ |
| LHP 零点 $1+s/\omega_z$ | 转折后 +20 dB/dec | 趋向 $+90^\circ$ |
| LHP 极点 $1/(1+s/\omega_p)$ | 转折后 -20 dB/dec | 趋向 $-90^\circ$ |

表中转折频率取正值。微分器单独并非可直接实现的严格真有理物理对象；复共轭极点、RHP 零点和时延应按完整表达式计算，不能只套这张渐近规则表。

## 3. 反馈、灵敏度与信号通道

### 3.1 从单位负反馈推导 T 与 S

取 $e=r-y$，零初始增量下 $Y=CGE$，则：

$$
Y=CG(R_{\mathrm{ref}}-Y),
\qquad
E=R_{\mathrm{ref}}-CGE.
$$

整理得到：

$$
T(s)=\frac{Y}{R_{\mathrm{ref}}}
=\frac{L_{\mathrm{loop}}}{1+L_{\mathrm{loop}}},
\qquad
S(s)=\frac{E}{R_{\mathrm{ref}}}
=\frac1{1+L_{\mathrm{loop}}},
\qquad S+T=1.
$$

这些通道关系依赖本文的单位负反馈及误差定义。非单位测量、参考预滤波或不同信号位置，需要重新推导。

### 3.2 大环路增益与跟踪能力

闭环稳定且模型适用时，$|L_{\mathrm{loop}}|\gg1$ 的频段通常有 $T\approx1$、$S\approx0$；$|L_{\mathrm{loop}}|\ll1$ 时则有 $T\approx L_{\mathrm{loop}}$、$S\approx1$。

但“增益越大越好”不能脱离相位：若环路靠近 $-1$，分母 $1+L_{\mathrm{loop}}$ 很小，反而可能放大响应。滤除不希望传递的 PWM 纹波、跟踪希望变化的电流参考与维持反馈稳定，是三个不同目标。

### 3.3 扰动与测量噪声走不同通道

设 $d$ 加在 plant 输入端，测量噪声 $n$ 进入比较节点，则：

$$
y=G[C(r-y-n)+d].
$$

零初始增量下整理为：

$$
Y=T R_{\mathrm{ref}}+GS D-TN.
$$

| 信号 | 到实际输出的通道 |
| --- | --- |
| 参考 $r$ | $T$ |
| plant 输入扰动 $d$ | $GS$ |
| 测量噪声 $n$ | $-T$ |

若扰动改加在 plant 输出端，其通道是 $S$，不是 $GS$。所以讨论“扰动抑制”之前必须说明注入位置；提高跟踪带宽也可能让更多测量噪声传到输出。

## 4. 交越频率、带宽与经典裕度

### 4.1 0 dB 交越与相对 -3 dB 带宽

开环增益交越频率满足：

$$
|L_{\mathrm{loop}}(j\omega_{\mathrm{gc}})|=1.
$$

对有限非零 DC 增益的低通闭环跟踪通道，带宽通常满足：

$$
|T(j\omega_{\mathrm{BW}})|=\frac{|T(0)|}{\sqrt2}.
$$

前者看开环，后者看指定闭环通道；定义不同，但特殊理想模型中数值可以相同。谐振峰、多次交点或非低通响应下，需说明采用哪个交点及带宽定义。$1/\sqrt2$ 是幅值比例，绝不是频率公式。

### 4.2 Phase Margin

对常见单一增益交越的 SISO 负反馈模型，采用适当展开的相位：

$$
PM=180^\circ+\angle L_{\mathrm{loop}}(j\omega_{\mathrm{gc}}).
$$

例如交越处相位 $-135^\circ$，则 PM 为 $45^\circ$：幅值到 1 时，还离 $-180^\circ$ 有多少相位余量。

它是经典单环指标，不是任意开环不稳定、多重交越或 MIMO 系统的独立稳定证明。

### 4.3 Gain Margin

在相位交越 $\omega_{\mathrm{pc}}$ 处，相位为 $-180^\circ$（模 $360^\circ$），经典增益裕度为：

$$
GM=\frac1{|L_{\mathrm{loop}}(j\omega_{\mathrm{pc}})|},
\qquad
GM_{\mathrm{dB}}=-20\log_{10}|L_{\mathrm{loop}}(j\omega_{\mathrm{pc}})|.
$$

若幅值为 0.25，则 GM 为 4、约 12.04 dB。这表示只改变比例增益时的临界倍数，不意味着任意模型误差都能放大四倍。

有多个增益/相位交越时要检查所有相关点；没有相位交越时，简单模型可能按惯例报告无穷 GM，但不能因此忽略延迟或其他动态。定义及多交越的工具解释可参考 [MathWorks margin 官方文档](https://www.mathworks.com/help/control/ref/dynamicsystem.margin.html)。

## 5. 从局部裕度到完整稳定性判断

### 5.1 为什么临界点是 -1

负反馈特征条件为 $1+L_{\mathrm{loop}}(s)=0$。若虚轴上某点 $L_{\mathrm{loop}}(j\omega)=-1$，它同时满足幅值为 1、相位为 $\pm180^\circ$。

Nyquist 曲线是在复平面看整条环路频响；PM、GM 分别从不同截面衡量余量。Nyquist 判断还需要开环 RHP 极点数量、完整轮廓与环绕方向，原点积分极点要采用缩进轮廓处理。

### 5.2 哪些情况需要升级分析

- 多次交越、共振峰或开环不稳定模态。
- 采样/PWM 时延、LCL 共振与频率相关电网阻抗。
- 不稳定极零相消、隐藏模态与强 d/q 耦合。
- 多运行点、多种参数误差同时变化。

这些情况下，应结合闭环极点、Nyquist、灵敏度峰值或更一般的鲁棒性判据。完整研究入口见 [频域稳定性与阻抗接口](../../ibr-grid-integration/frequency-domain-stability-and-impedance-interfaces.md)。

## 6. 工程分析顺序与自检

### 6.1 九步分析流程

1. 明确参考、输出、扰动位置、测量通道与模型边界。
2. 从物理方程或线性化建立 $G$，说明运行点与零初始增量。
3. 找极点、零点、转折频率、共振、RHP 零点及时延。
4. 同时读幅值与相位，检查频率与增益单位。
5. 由跟踪、扰动、噪声和硬件约束给出目标指标。
6. 设计 $C$、组成完整环路，检查全部相关交越。
7. 分析 $T$、$S$ 及实际扰动/噪声通道。
8. 加入延迟、限幅、LCL、电网与控制耦合。
9. 在参数和工况变化下，用频域与时域共同验证。

### 6.2 常见误区与验收问题

- Bode 幅值相同不代表相位或稳定性相同。
- 裸 RL 的 $-90^\circ$ 相位不能单独推出闭环失稳。
- 0 dB 开环交越与 -3 dB 闭环带宽不是同一个定义。
- 正 PM、无穷 GM 都需要结合模型与判据边界解释。
- 高频衰减有助滤波，但跟踪速度、噪声和裕度仍需折中。

不看公式时，应能独立推导 $T$、$S$ 与噪声通道，说明一个 decade 的含义，并从“交越处相位 $-135^\circ$、相位交越处幅值 0.25”分别算出 PM 与 GM。

### 6.3 相关笔记

- [极点、零点、带宽与稳态误差](poles-zeros-bandwidth-and-steady-state-error.md)
- [Laplace 变换与传递函数](../mathematics/laplace-transform-and-transfer-functions.md)
- [PI 电流环频域设计、时延与级联控制](../../power-electronics/current-loop-frequency-domain-design.md)
- [频域稳定性与阻抗分析接口](../../ibr-grid-integration/frequency-domain-stability-and-impedance-interfaces.md)
