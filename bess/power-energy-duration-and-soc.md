# BESS 功率、可用能量与放电时长

统一算例采用一套假想 BESS。PCC 额定放电功率为 1 MW，铭牌储能容量为 2 MWh。核算放电时长时，先明确容量的可用范围和计量位置。

## 1. 功率与累计能量

### 1.1 MW 与 MWh

令 $E_{\mathrm{out}}$ 为累计送出的能量。输出功率为正时有

$$
P=\frac{dE_{\mathrm{out}}}{dt},
\qquad
E_{\mathrm{out}}(t_1)-E_{\mathrm{out}}(t_0)=\int_{t_0}^{t_1}P(t)\,dt.
$$

功率以 W 或 MW 表示，能量以 J 或 MWh 表示。恒功率时 $\Delta E_{\mathrm{out}}=P\Delta t$。若 2 MWh 全部可在 PCC 交付，且能持续输出 1 MW，则 $\Delta t=2\ \mathrm h$。

### 1.2 AC 与 DC 计量边界

从电池到 PCC 的路径可能经过 DC/DC、PCS、滤波器和变压器，并受辅助用电影响。若 2 MWh 表示电池侧可用 DC 能量，教学假设从电池到 PCC 的综合放电效率恒为 $\eta_d=0.95$，则

$$
E_{\mathrm{AC,usable}}=\eta_d E_{\mathrm{DC,usable}}
=1.9\ \mathrm{MWh},\qquad t_{\mathrm{AC}}=1.9\ \mathrm h.
$$

若可用能量已在 PCC 计量，直接以该 AC 能量除以 AC 功率。实际时长还取决于 SOC 窗口、持续功率限制、负载、温度和辅助功耗。此处 $\eta_d$ 只针对放电路径。

## 2. 能量状态与倍率

### 2.1 从 AC 功率得到 SOC 变化率

在简化恒容量模型中，以可用能量窗口内剩余能量 $E_b$ 表示能量状态，定义 $\mathrm{SOC}=E_b/E_{\mathrm{usable,max}}$。PCC 向外放电 $P_{\mathrm{AC}}>0$ 时有

$$
\dot E_b=-\frac{P_{\mathrm{AC}}}{\eta_d},\qquad
\dot{\mathrm{SOC}}=-\frac{P_{\mathrm{AC}}}{\eta_d E_{\mathrm{usable,max}}}.
$$

用 W 和 J 得到每秒变化率，用 MW 和 MWh 得到每小时变化率。这里的 SOC 是按可用能量定义的简化状态。其变化通常以小时计，电流环则可能在毫秒尺度上动作。

### 2.2 功率能量比与电芯 C-rate

额定数值之比 $1\ \mathrm{MW}/2\ \mathrm{MWh}=0.5\ \mathrm{h^{-1}}$ 给出系统时长对应的倍率直觉。电芯 C-rate 由电芯电流和 Ah 容量定义，换算时需要明确 DC 电压、电池侧功率、串并联配置和损耗。

## 3. 相关笔记与章节索引

### 3.1 从能量预算到交流容量

- [11 kV 三相电流与 PCS 容量](../foundations/electrical-engineering/complex-power.md#34-11-kv-并网电流与-pcs-能力圆)
- [PCC 等效、短路容量与 SCR](../power-systems/pcc-grid-equivalent-and-scr.md)
- [W47 贯穿案例与课堂自测](../weekly/2026/2026-week-47.md)
