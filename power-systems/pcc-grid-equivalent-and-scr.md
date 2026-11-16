# PCC 戴维南等效、短路比与电压变化

算例以假想 BESS 的 11 kV 公共耦合点（PCC）为边界，从 PCC 看向外部电网。功率与电流从 BESS 向网侧注入为正。稳态方程采用平衡正序、基波 RMS 相量。工程中应分别确认 PCC、计量点和接入协议指定的连接点。

## 1. 外部网络与三相短路容量

### 1.1 戴维南等效和电压方向

以外部电源 $\underline E$ 串联 $Z_{\mathrm{th}}=R+jX$ 表示指定工况下的网侧正序等值。按注入方向，PCC 相量满足

$$
\underline V=\underline E+Z_{\mathrm{th}}\underline I.
$$

相同注入电流下，等值阻抗越大，PCC 相量偏移通常越大。此处的 $Z_{\mathrm{th}}$ 对应指定频率和工况。

### 1.2 从三相金属性短路得到故障容量

在线性、平衡的简化等值中，取故障前参考线电压 RMS 为 $V_{LL}$，其相电压为 $V_{LL}/\sqrt3$。令 PCC 三相金属性短路，则外网供给的对称短路电流幅值和三相短路容量为

$$
|I_{\mathrm{sc}}|=\frac{V_{LL}}{\sqrt3|Z_{\mathrm{th}}|},\qquad
S_{\mathrm{sc}}=\sqrt3 V_{LL}|I_{\mathrm{sc}}|
=\frac{V_{LL}^2}{|Z_{\mathrm{th}}|}.
$$

式中的容量使用故障前参考电压。实际研究需确定电压系数、网络拓扑以及应计入的故障电流贡献。

## 2. SCR 定义及换基准

### 2.1 分母和计算边界

本例采用 $SCR=S_{\mathrm{sc}}/P_{\mathrm{rated}}$ 的工程数值约定，分子用 MVA、分母用额定有功 MW。设备视在功率作分母的定义需要另行声明。澳洲 AEMO V2.2 第 7.1 节对发电系统和综合资源系统采用接入点同步三相故障容量除以额定有功，故障容量排除连接点设备侧贡献。[AEMO 系统强度影响评估指南（含 V2.2）](https://www.aemo.com.au/energy-systems/electricity/national-electricity-market-nem/participate-in-the-market/network-connections/system-strength-impact-assessment-guidelines)

在 11 kV、1 MW 的教学案例中，假设 $S_{\mathrm{sc}}=5\ \mathrm{MVA}$，则 $SCR=5$、$|Z_{\mathrm{th}}|=11000^2/(5\times10^6)=24.2\ \Omega$。模长只约束 $R^2+X^2$，下面的纯电抗算例另取 $R=0$。

### 2.2 SCR 与标幺阻抗

当故障容量使用的参考线电压与 $V_B$ 一致时，$S_{\mathrm{sc}}=S_B/|Z_{\mathrm{th,pu}}|$，因此

$$
SCR=\frac{S_B/P_{\mathrm{rated}}}{|Z_{\mathrm{th,pu}}|}.
$$

这里 $S_B/P_{\mathrm{rated}}$ 也使用 MVA/MW 数值比。1 MVA 基准下 $Z_B=121\ \Omega$、$|Z_{\mathrm{th,pu}}|=0.2$，此时可写 $SCR=1/0.2$。改用 100 MVA，$Z_B=1.21\ \Omega$、$|Z_{\mathrm{th,pu}}|=20$，于是 $SCR=100/20$，仍然为 5。若故障参考电压不同，还需乘电压与基准电压之比的平方。

## 3. 注入功率与 PCC 电压

### 3.1 一阶电压幅值与相角灵敏度

在协调的标幺相量下，$\underline S=P+jQ=\underline V\underline I^*$，因此 $\underline I=(\underline S/\underline V)^*$。取外部电源 $\underline E=1\angle0$ pu，在近空载的平坦点附近，线性化时 $\Delta\underline I\approx\Delta P-j\Delta Q$，从而

$$
\Delta\underline V\approx(R\Delta P+X\Delta Q)+j(X\Delta P-R\Delta Q).
$$

对单位电压零相角作一阶展开，$\Delta|V|\approx R\Delta P+X\Delta Q$，$\Delta\theta\approx X\Delta P-R\Delta Q$（rad）。在高 $X/R$ 时，幅值主要受到无功注入影响，相角主要受到有功注入影响。带载点应从其自身的潮流方程重新求灵敏度。

在 1 MVA、11 kV、1 MW 分母的条件下，再假设各个阻抗全为电抗，正无功变化 $\Delta Q=0.2$ pu 给出下表。

| SCR | 短路容量（MVA） | 外网阻抗模长（Ω） | $X$（pu） | 一阶 $\Delta\lvert V\rvert$（pu） |
| --- | --- | --- | --- | --- |
| 2 | 2 | 60.5 | 0.5 | +0.10 |
| 5 | 5 | 24.2 | 0.2 | +0.04 |
| 10 | 10 | 12.1 | 0.1 | +0.02 |

对 SCR=5，0.04 pu 折合线电压上升约 0.44 kV。扰动较大时应对照非线性解。

### 3.2 纯电抗两节点的精确对照

设 $\underline V=V e^{j\delta}$、$\underline E=E\angle0$、$Z=jX$，各量均为 pu。代入电流 $\underline I=(\underline V-\underline E)/(jX)$ 后有

$$
P+jQ=\underline V\underline I^*
=\frac{j}{X}(V^2-EV e^{j\delta}).
$$

取实虚部得 $P=(EV/X)\sin\delta$、$Q=(V^2-EV\cos\delta)/X$。在 $E=1$、$P=0$、$\delta=0$、$Q=0.2$、$X=0.2$ 的分支内，$V^2-V-0.04=0$。取正电压幅值得 $V=(1+\sqrt{1.16})/2\approx1.03852$ pu，电压上升约 3.852%，与 4% 的一阶估算可直接比较。

若给定 $E=V=1$、$X=0.2$、$P=1$，近零相角分支是 $\delta=\arcsin(0.2)\approx11.54^\circ$，同时要求 $Q=(1-\cos\delta)/0.2\approx0.101$ pu。这个稳态约束了可同时指定的 P、Q 和电压。

### 3.3 从两节点推广到节点导纳

以 $\underline{\mathbf I}=Y_{\mathrm{bus}}\underline{\mathbf V}$ 表示净注入电流。令 $Y_{ij}=G_{ij}+jB_{ij}$、$\theta_{ij}=\theta_i-\theta_j$，代入 $\underline S_i=\underline V_i\underline I_i^*$ 得

$$
P_i=\sum_j V_i V_j(G_{ij}\cos\theta_{ij}+B_{ij}\sin\theta_{ij}),
$$

$$
Q_i=\sum_j V_i V_j(G_{ij}\sin\theta_{ij}-B_{ij}\cos\theta_{ij}).
$$

本节用标幺量说明方程结构。节点类型、约束、潮流 Jacobian 与 Newton–Raphson 求解属于后续学习。

## 4. 电网强度与动态分析入口

### 4.1 从 SCR 到 GFL 控制交互

在相同额定电压和装机功率下，SCR 较低对应较大的外网阻抗模长。注入电流改变 PCC 电压，PLL 由此更新角度，dq 电流控制再改变注入，形成反馈链。相同 SCR 下，$X/R$、频率相关阻抗、运行点、多台 IBR、PLL 和电流环带宽、LCL、数字时延及限流仍可能不同。

SCR 可作为选择详细研究工况的初筛指标。传统强弱网分类依赖指标定义和适用条件，动态稳定需要结合模型与工况检验。GFM 的同步机制与典型 GFL-PLL 不同，仍需分析网络阻抗、功率同步与限流。低 SCR 下 IBR 接入的稳态和动态议题可参照 [IEC TR 63401-1:2022](https://webstore.iec.ch/en/publication/64424)。

### 4.2 相关笔记与章节索引

- [三相标幺与阻抗换算](three-phase-per-unit-and-transformer-bases.md)
- [PLL 与 PCC 电压反馈](../ibr-grid-integration/pll-and-srf-pll.md)
- [从 LCL 到弱电网反馈](../ibr-grid-integration/frequency-domain-stability-and-impedance-interfaces.md#32-弱电网增加了另一条反馈链)
- [W47 贯穿案例](../weekly/2026/2026-week-47.md)
