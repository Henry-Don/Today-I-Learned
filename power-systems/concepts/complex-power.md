# 复功率与 BESS PCS 的 P-Q 能力

## 1. 瞬时功率与平均有功功率

真实瞬时功率始终是实数：

$$
p(t)=v(t)i(t)
$$

设电压和电流分别为：

$$
v(t)=\sqrt2V\cos\omega t
$$

$$
i(t)=\sqrt2I\cos(\omega t-\phi)
$$

其中 $V$、$I$ 为 RMS 值，$\phi$ 是电压超前电流的角度。利用

$$
2\cos A\cos B=\cos(A-B)+\cos(A+B)
$$

可得：

$$
p(t)=VI\cos\phi+VI\cos(2\omega t-\phi)
$$

第二项在一个基波周期内的平均值为零，因此平均有功功率为：

$$
\boxed{P=VI\cos\phi}
$$

有功功率表示一个周期内的净能量传递，单位为 W。

## 2. 无功功率与正交电流

无功功率定义为：

$$
\boxed{Q=VI\sin\phi}
$$

它描述电源与电感、电容储能场之间的周期性能量交换，单位为 var。

若把电压相量选作实轴参考：

$$
\underline V=V\angle0^\circ
$$

对滞后电流：

$$
\underline I=I\angle(-\phi)=I\cos\phi-jI\sin\phi
$$

电流可以分解成沿电压方向与垂直于电压方向的两部分：

$$
I_\parallel=I\cos\phi,\qquad I_\perp=I\sin\phi
$$

于是：

$$
P=VI_\parallel,\qquad Q=VI_\perp
$$

这给出一个重要直觉：有功与同相电流有关，无功与正交电流有关。但这个直接对应只在电压已经对齐参考轴时成立；旋转参考坐标后，电流的实部和虚部会变化，而物理功率不应变化。

## 3. 为什么复功率使用共轭

采用 RMS 相量时，定义复功率：

$$
\boxed{\underline S=\underline V\underline I^*=P+jQ}
$$

设：

$$
\underline V=V\angle\theta_V,qquad
\underline I=I\angle\theta_I
$$

因为：

$$
\underline I^*=I\angle(-\theta_I)
$$

所以：

$$
\underline S=VI\angle(\theta_V-\theta_I)
$$

令 $\phi=\theta_V-\theta_I$：

$$
\underline S=VI(\cos\phi+j\sin\phi)
$$

因此自然得到：

$$
P=VI\cos\phi,
\qquad
Q=VI\sin\phi
$$

共轭的作用是让乘法得到有物理意义的电压—电流相位差，而不是两个绝对相位之和。

它还保证复功率与公共参考相位无关。若整个参考系旋转 $\alpha$：

$$
\theta_V\rightarrow\theta_V+\alpha,qquad
\theta_I\rightarrow\theta_I+\alpha
$$

则相位差保持不变：

$$
(\theta_V+\alpha)-(\theta_I+\alpha)=\theta_V-\theta_I
$$

因此 $\underline V\underline I^*$ 是坐标旋转不变量。

## 4. 直角坐标形式

设：

$$
\underline V=V_r+jV_i,qquad
\underline I=I_r+jI_i
$$

展开复功率：

$$
\underline S=(V_r+jV_i)(I_r-jI_i)
$$

得到：

$$
\boxed{P=V_rI_r+V_iI_i}
$$

$$
\boxed{Q=V_iI_r-V_rI_i}
$$

这说明有功不是“实部电压乘实部电流”，无功也不是“虚部乘虚部”；它们取决于两个二维相量之间的相对关系。

当电压对齐实轴，即 $\underline V=V+j0$ 时，上式简化为：

$$
P=VI_r,qquad Q=-VI_i
$$

在被动符号约定下，感性负载的电流滞后，因此 $I_i<0$、$Q>0$。

### 数值例子

若：

$$
\underline V=230+j0\ \text{V}
$$

$$
\underline I=10\angle(-30^\circ)=8.66-j5\ \text{A}
$$

则：

$$
P=230\times8.66\approx1992\ \text{W}
$$

$$
Q=-230\times(-5)=1150\ \text{var}
$$

复功率约为：

$$
\underline S=1992+j1150\ \text{VA}
$$

## 5. 有功、无功与视在功率

复功率的幅值称为视在功率：

$$
|\underline S|=VI=\sqrt{P^2+Q^2}
$$

三者的工程含义为：

- $P$：净能量传递速率，单位 W。
- $Q$：周期性储能交换的度量，单位 var。
- $|S|$：设备同时承受电压和电流的容量尺度，单位 VA。

功率因数为：

$$
\mathrm{pf}=\frac{P}{|\underline S|}=\cos\phi
$$

这一表达式针对正弦稳态；存在明显谐波时，还需区分位移功率因数与总功率因数。

## 6. BESS PCS 的 P-Q 能力

PCS 不仅决定电池充放电的 MW，也可以通过调节交流电流的相位提供或吸收无功，参与电压支撑。

在忽略过载、直流侧、交流电压、热限制和厂商控制约束的理想圆形能力边界下：

$$
P^2+Q^2\le S_{\max}^2
$$

若 PCS 额定容量为 $100\ \text{MVA}$，当前有功为 $80\ \text{MW}$，则剩余无功能力为：

$$
|Q|_{\max}=\sqrt{100^2-80^2}=60\ \text{MVAr}
$$

这只是几何上限。实际能力曲线还可能受以下因素限制：

- 变流器最大电流与结温；
- 直流母线电压与电池充放电功率；
- 并网点电压及滤波器压降；
- 调制比、控制策略和保护定值；
- 厂商规定的持续/短时运行包络。

## 7. 通向 dq 控制

当同步旋转坐标系的 d 轴与电网电压矢量对齐时，电压的 q 轴分量为零。于是有功、无功常可分别主要由 $i_d$、$i_q$ 控制：

$$
P\propto i_d,qquad Q\propto i_q
$$

具体比例和正负号取决于 Clarke/Park 变换、功率方向和 q 轴方向等约定，不能脱离模型直接套用。这一坐标对齐，正是“同相电流负责有功、正交电流负责无功”在三相变流器控制中的延伸。

## 8. 常见误区

1. **“相量的虚部是另一股真实电流。”** 真实瞬时电流为实数；虚部是参考坐标中的正交分量。
2. **“电流实部永远是有功电流，虚部永远是无功电流。”** 只有在电压对齐实轴等特定坐标选择下才能直接对应。
3. **“有功等于实部乘实部，无功等于虚部乘虚部。”** 一般式应使用 $P=V_rI_r+V_iI_i$ 和 $Q=V_iI_r-V_rI_i$。
4. **“$S=VI$ 就足够。”** 不带共轭会得到绝对相位之和；$\underline V\underline I^*$ 才给出参考无关的相位差。
5. **“额定 MVA 减去 MW 就是剩余 MVAr。”** 能力边界按平方关系计算，而且真实 PCS 还受多重运行限制。

