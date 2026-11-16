# 三相标幺基准、阻抗换算与变压器

标幺值 $x_{\mathrm{pu}}=x/x_B$ 用同量纲基准表达物理量。基准为 11 kV 时，10.45 kV、11 kV 和 11.55 kV 分别是 0.95、1.00 和 1.05 pu。运行允许范围由设备和研究条件另外定义。

## 1. 协调的三相基准

### 1.1 从线电压和三相功率求电流与阻抗

取 $S_B$ 为三相总视在功率基准，$V_B$ 为线电压 RMS 基准。在线电流与每相星形等效的 RMS 约定下有

$$
I_B=\frac{S_B}{\sqrt3 V_B},\qquad
V_{\mathrm{ph},B}=\frac{V_B}{\sqrt3},\qquad
Z_B=\frac{V_{\mathrm{ph},B}}{I_B}=\frac{V_B^2}{S_B}.
$$

代入 $S_B=1\ \mathrm{MVA}$、$V_B=11\ \mathrm{kV}$，得 $I_B=52.4864\ \mathrm A$、$V_{\mathrm{ph},B}=6.35085\ \mathrm{kV}$、$Z_B=121\ \Omega$。$Z_B$ 是欧姆值与 pu 的换算尺度，外部网络阻抗需要由网络等值另行确定。

### 1.2 标幺复功率中三相系数的来源

平衡基波、统一端口方向和 RMS 相量下，三相总复功率为 $\underline S=3\underline V_{\mathrm{ph}}\underline I_L^*$。用 $S_B=3V_{\mathrm{ph},B}I_B$ 除后，分子和分母的 3 相消。

$$
P_{\mathrm{pu}}+jQ_{\mathrm{pu}}
=\underline V_{\mathrm{ph,pu}}\underline I_{L,\mathrm{pu}}^*.
$$

电压相量按相电压基准归一化。平衡时其幅值等于线电压幅值除以线电压基准。1 MW、0.5 MVAr 在 1 MVA 基准下是 $1+j0.5$ pu。电压幅值为 1 pu 时，电流幅值为 $\sqrt{1.25}\approx1.11803$ pu。

## 2. 换基准与跨电压等级

### 2.1 同侧阻抗重新标幺化

同一侧的真实阻抗可写作 $Z=Z_{\mathrm{pu,old}}Z_{B,\mathrm{old}}=Z_{\mathrm{pu,new}}Z_{B,\mathrm{new}}$。因此

$$
Z_{\mathrm{pu,new}}
=Z_{\mathrm{pu,old}}\frac{S_{B,\mathrm{new}}}{S_{B,\mathrm{old}}}
\left(\frac{V_{B,\mathrm{old}}}{V_{B,\mathrm{new}}}\right)^2.
$$

在 11 kV 处把基准从 1 MVA 改到 100 MVA，电流基准变为 5248.64 A、阻抗基准变为 $1.21\ \Omega$，同一 1 MW 变为 0.01 pu。换算前先确认实际欧姆阻抗和电压基准在同一参考侧。

### 2.2 11/33 kV 变压器两侧的等值

对理想变比 $a=33/11=3$，折算后的高压侧阻抗是低压侧的 $a^2=9$ 倍。两侧取相同的 $S_B=1\ \mathrm{MVA}$，并使电压基准按变比协调，则高、低压侧基准分别为 $1089\ \Omega$ 和 $121\ \Omega$，同样相差 9 倍。实际阻抗与阻抗基准的倍率相消，两侧得到相同 pu。实际分接头、绕组连接及漏阻抗仍需在模型中体现。

以 2 MVA、11/33 kV 变压器为例，假定铭牌短路电抗为 6%。保持两侧额定电压基准，换到 1 MVA 后 $X_{\mathrm{pu}}=0.06(1/2)=0.03$。11 kV 侧为 $3.63\ \Omega$，33 kV 侧为 $32.67\ \Omega$。如果铭牌给出的是短路阻抗模长，需要额外的 $R/X$ 信息才能分出电抗。

## 3. 状态空间与章节索引

### 3.1 状态缩放和特征值

对 $\dot x=Ax+Bu$ 取常值可逆对角基准矩阵，令 $x=D_xx_{\mathrm{pu}}$、$u=D_uu_{\mathrm{pu}}$。代入并左乘 $D_x^{-1}$ 得

$$
A_{\mathrm{pu}}=D_x^{-1}AD_x,\qquad
B_{\mathrm{pu}}=D_x^{-1}BD_u.
$$

第一个式子是相似变换。在相同的物理时间单位下，$A$ 与 $A_{\mathrm{pu}}$ 的特征值一致。若再把时间改成 $\tau=t/t_B$，则对 $\tau$ 求导的状态矩阵多出因子 $t_B$，特征值的数值也按时间基准缩放。

### 3.2 相关笔记

- [平衡三相的线电压与相电压](../foundations/electrical-engineering/sinusoidal-steady-state-and-phasors.md#35-平衡三相的线电压与相电压)
- [幅值不变 dq 与标幺电流参考](../foundations/electrical-engineering/complex-power.md#35-幅值不变-dq-与标幺电流参考)
- [SCR 换基准核对](pcc-grid-equivalent-and-scr.md#22-scr-与标幺阻抗)
- [可对角化时的模态坐标](../foundations/control/state-space-eigenvalues-and-modes.md#41-可对角化时的模态坐标)
