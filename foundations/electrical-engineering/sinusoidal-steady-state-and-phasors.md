# 正弦稳态与相量

## 1. 为什么工程系统中经常出现正弦

### 1.1 LC 电路的自然振荡

理想 LC 电路满足：

$$
L\frac{di}{dt}+v_C=0
$$

$$
i=C\frac{dv_C}{dt}
$$

消去电流得到：

$$
LC\frac{d^2v_C}{dt^2}+v_C=0
$$

令 $\omega_0=1/\sqrt{LC}$，则：

$$
v_C''+\omega_0^2v_C=0
$$

自然解为：

$$
v_C(t)=A\cos(\omega_0t+\phi)
$$

因此，正弦并非人为强加给系统的波形，而是许多线性振荡系统的自然模态。

### 1.2 旋转发电机与正弦电压

在线圈匀速旋转的简化模型中：

$$
\theta=\omega t
$$

$$
\Phi(t)=BA\cos\omega t
$$

由法拉第定律：

$$
e(t)=-N\frac{d\Phi}{dt}
=NBA\omega\sin\omega t
$$

所以机械匀速旋转通过周期变化的磁通产生正弦感应电压。实际同步发电机会利用绕组分布和磁路设计，使基波尽量接近正弦。

### 1.3 正弦是 LTI 系统的特殊输入

在线性时不变系统中，正弦稳态输入经过系统后频率不变，只改变幅值和相位：

$$
e^{j\omega t}
\longrightarrow
H(j\omega)e^{j\omega t}
$$

因此，大型交流网络可以在每个固定频率上用幅值和相位来分析。理想正弦只有一个频率分量；方波等非正弦波包含多个谐波，会增加损耗、发热、振动、谐振和 EMI 风险。

## 2. 为什么传统电网采用交流

传统交流系统的重要优势是可以直接使用工频变压器改变电压。理想变压器依赖交变磁通：

$$
v=N\frac{d\Phi}{dt}
$$

恒定直流达到稳态后，$d\Phi/dt=0$，不能用传统工频变压器直接变压。

升高输电电压可以在相同功率下降低电流，从而降低线路损耗：

$$
P\approx VI
$$

$$
P_{\mathrm{loss}}=I^2R
$$

这并不意味着交流永远优于直流。现代 HVDC 适用于长距离、大容量、海缆和异步联网；BESS 本身也体现了 AC/DC 的功能分工：

$$
\mathrm{Battery\ DC}
\longleftrightarrow
\mathrm{PCS}
\longleftrightarrow
\mathrm{Grid\ AC}
$$

## 3. 为什么通常是 50 Hz 或 60 Hz

50/60 Hz 是长期工程折中和标准化的结果，不是数学上的唯一最优值。

频率过低时，由变压器近似关系：

$$
V\approx4.44fN\Phi_{\max}
$$

若电压不变而频率下降，必须增加匝数 $N$ 或最大磁通 $\Phi_{\max}$。受磁芯饱和限制，变压器会更大、更重。同步转速也随频率下降：

$$
n_s=\frac{120f}{P_{\mathrm{poles}}}
$$

频率过高时：

$$
X_L=2\pi fL
$$

$$
I_C=2\pi fCV
$$

线路感抗、电缆充电电流、趋肤效应、涡流损耗和 EMI 会更加突出。高频有利于缩小磁性元件，所以适合开关电源和 DC/DC，但不适合作为传统大范围输电网的统一工频。

## 4. 相量提取公共时间因子

由欧拉公式：

$$
A\cos(\omega t+\phi)
=\Re\left\{Ae^{j\phi}e^{j\omega t}\right\}
$$

定义复幅值或相量：

$$
\underline{X}=Ae^{j\phi}
$$

于是：

$$
x(t)=\Re\left\{\underline{X}e^{j\omega t}\right\}
$$

相量法适用于固定频率的正弦稳态。所有信号共享 $e^{j\omega t}$，所以将公共时间因子提取出去，只保留每个信号特有的幅值和相位。

“相量把时间去掉了”只是简化说法。更准确地说，相量法把共同的时间依赖因式分解；恢复瞬时量时，仍需乘回 $e^{j\omega t}$ 并按约定取实部。

## 5. Peak 与 RMS 相量

若：

$$
v(t)=V_m\cos(\omega t+\phi)
$$

峰值相量为：

$$
\underline{V}_{\mathrm{peak}}=V_m\angle\phi
$$

RMS 相量为：

$$
\underline{V}_{\mathrm{rms}}
=\frac{V_m}{\sqrt{2}}\angle\phi
$$

电力系统通常默认 RMS。例如：

$$
v(t)=325\cos(100\pi t)\ \mathrm{V}
$$

对应约：

$$
\underline{V}=230\angle0^\circ\ \mathrm{V}
$$

阅读论文、软件模型或设备手册时，必须确认使用的是 peak 还是 RMS。

## 6. 为什么微分变成乘以 jω

因为：

$$
\frac{d}{dt}e^{j\omega t}
=j\omega e^{j\omega t}
$$

所以在固定频率的相量域中：

$$
\frac{d}{dt}
\longleftrightarrow
j\omega
$$

微分运算因此变成代数乘法。

### 电阻

$$
\underline{V}=R\underline{I}
$$

$$
Z_R=R
$$

### 电感

由 $v_L=L\,di/dt$：

$$
\underline{V}_L=j\omega L\underline{I}
$$

$$
Z_L=j\omega L
$$

### 电容

由 $i_C=C\,dv/dt$：

$$
\underline{I}=j\omega C\underline{V}
$$

$$
Z_C=\frac{1}{j\omega C}
$$

因此，电阻的阻抗角为 $0^\circ$，电感为 $+90^\circ$，电容为 $-90^\circ$。

## 7. 相量的实部与虚部

真实导线中的瞬时电流仍然是实数。例如：

$$
i(t)=10\sqrt{2}\cos(\omega t-30^\circ)\ \mathrm{A}
$$

对应的 RMS 相量为：

$$
\underline{I}=10\angle(-30^\circ)\ \mathrm{A}
$$

展开后：

$$
\underline{I}=8.66-j5\ \mathrm{A}
$$

$8.66$ 和 $-5$ 是同一个电流相量在所选二维参考坐标中的两个分量。虚部不是另一股“虚数电流”。

只有把电压相量选作实轴参考时，电流的同相分量和正交分量才可以直接联系到有功与无功。这个关系在 [复功率与 BESS PCS 的 P-Q 能力](complex-power.md) 中继续推导。

## 8. 通向三相系统和 dq 坐标

平衡三相电压可以写成：

$$
v_a=V\cos\theta
$$

$$
v_b=V\cos\left(\theta-\frac{2\pi}{3}\right)
$$

$$
v_c=V\cos\left(\theta+\frac{2\pi}{3}\right)
$$

Clarke 变换把三相量映射到二维 αβ 平面，可用复空间矢量表示为：

$$
v_{\alpha\beta}=v_\alpha+jv_\beta
$$

Park 变换让坐标系以同步角速度旋转，使稳态交流矢量在 dq 坐标中变成近似常量。这是后续 PLL、dq 电流控制、GFL/GFM 和阻抗分析的数学入口。

## 9. 相量法的适用边界

标准相量法以单一频率正弦稳态为前提。它非常适合稳态交流电路和按频率分解后的 LTI 分析，但不能直接完整描述：

- 快速暂态和开关过程
- 多频率谐波相互作用
- 参数随时间显著变化的系统
- 大扰动下的非线性限幅和控制模式切换

这些问题需要更完整的时域、谐波域、频域或状态空间模型。

## 10. 常见误区

1. **相量把真实电压和电流变成了复数。** 相量只是正弦稳态的数学表示，瞬时物理量仍是实数。
2. **相量完全删除了时间。** 它只是提取公共因子 $e^{j\omega t}$。
3. **相量虚部是一股真实的无功电流。** 实部和虚部是坐标分量，其物理解释取决于参考轴。
4. **RMS 和 peak 可以混用。** 两种约定相差 $\sqrt{2}$，混用会直接造成幅值和功率错误。
5. **相量适用于任意瞬态。** 标准相量法的前提是固定频率正弦稳态。

## 11. 相关笔记

- [三角函数与复数](../mathematics/trigonometry-and-complex-numbers.md)
- [复功率与 BESS PCS 的 P-Q 能力](complex-power.md)
- [IBR and Grid Integration](../../ibr-grid-integration/README.md)
