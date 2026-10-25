# LCL 谐振、模态形状与能量稳定性

本文从同一组 LCL 状态方程连接矩阵特征值、谐振频率、模态形状、绕组电阻和能量稳定性。变量按所选工作点附近的单相/单轴增量解释，省略 $\Delta$；不把局部理想滤波器结论当作整台并网变流器的稳定证明。

## 1. 拓扑、状态与输入输出

### 1.1 电流正方向与等值边界

~~~text
             i1 →                 i2 →
vc ── L1 ────────●──── L2 ──────────── vg
                │  iC ↓
                Cf
                │
          equivalent reference
~~~

$i_1$ 从变流器流向电容节点，$i_2$ 从节点流向网侧，$v_f$ 是电容节点电压，$v_c,v_g$ 为端口电压输入。此图是每相或单轴等值，不规定真实三相电容组必须星形接地；接线、零序和坐标耦合需另行说明。

初始模型取 $L_1,L_2,C_f>0$，忽略电阻、控制器、采样/PWM 延迟及复杂网侧动态。

### 1.2 从 KVL、KCL 写出三个独立状态

$$
L_1\dot i_1=v_c-v_f,
\qquad
C_f\dot v_f=i_1-i_2,
\qquad
L_2\dot i_2=v_f-v_g.
$$

取 $x=[i_1,v_f,i_2]^T$、$u=[v_c,v_g]^T$、输出 $y=i_2$。电容电流是两侧电流之差，因此不能将两个电感电流视为同一状态。

### 1.3 完整状态空间矩阵

$$
\dot x=Ax+Bu,
\qquad
A=\begin{bmatrix}
0&-1/L_1&0\\
1/C_f&0&-1/C_f\\
0&1/L_2&0
\end{bmatrix},
\qquad
B=\begin{bmatrix}
1/L_1&0\\
0&0\\
0&-1/L_2
\end{bmatrix}.
$$

$$
C_y=[0,0,1],\qquad D=[0,0].
$$

这是三状态、双输入、单输出模型。求自由模态时令输入增量 $v_c=v_g=0$，不是把实际运行的电网断电；换一个电流测量位置会改变输出矩阵。

## 2. 无损特征值、谐振与模态形状

### 2.1 特征行列式的展开

$$
\lambda I-A=
\begin{bmatrix}
\lambda&1/L_1&0\\
-1/C_f&\lambda&1/C_f\\
0&-1/L_2&\lambda
\end{bmatrix}.
$$

沿第一行展开：

$$
\begin{aligned}
\det(\lambda I-A)
&=\lambda\left(\lambda^2+\frac1{C_fL_2}\right)
-\frac1{L_1}\left(-\frac\lambda{C_f}\right)\\
&=\lambda\left[\lambda^2+\frac{L_1+L_2}{L_1L_2C_f}\right].
\end{aligned}
$$

因此自然谐振角频率为：

$$
\omega_{\mathrm{res}}=\sqrt{\frac{L_1+L_2}{L_1L_2C_f}}.
$$

### 2.2 与网侧电流传递函数核对

从 $v_c$ 到 $i_2$、令网侧电压增量为零并采用零初始状态，有：

$$
\left.\frac{I_2(s)}{V_c(s)}\right|_{V_g=0}
=\frac1{L_1L_2C_fs^3+(L_1+L_2)s}.
$$

分母根是 $0,\pm j\omega_{\mathrm{res}}$，与状态矩阵吻合；本例该 SISO 通道没有隐藏这些模态。高频衰减和阻尼实现可参考 [imperix LCL 有源阻尼说明](https://imperix.com/doc/implementation/active-damping-of-lcl-filters)，但本文仍需独立检查状态与稳定性。

### 2.3 零模态与共轭振荡模态

三个特征值为：

$$
\lambda_1=0,\qquad \lambda_{2,3}=\pm j\omega_{\mathrm{res}}.
$$

零特征值可取特征向量 $[1,0,1]^T$：两电感有相同常值电流，电容电压为零，既不持续充电，也没有电阻消耗，是共同电流模态。

共轭对共同描述一个真实振荡模式。它不是电网基波、控制交越或开关频率；三阶也不意味着有三种不同自然谐振频率。

### 2.4 共轭模态的相对电流

对非零特征值，模态复幅值满足：

$$
\lambda\underline i_1=-\frac{\underline v_f}{L_1},
\qquad
\lambda\underline i_2=\frac{\underline v_f}{L_2},
\qquad
\frac{\underline i_1}{\underline i_2}=-\frac{L_2}{L_1}.
$$

两侧振荡电流在本文方向下反相，比例由电感决定。此复幅值用于自然模态，不应与默认 RMS 的固定频率电网相量直接混用。特征值描述时间变化，特征向量描述状态间结构；模态形状不是参与因子。

### 2.5 自由有界不等于渐近稳定或 BIBO 稳定

正参数下三个特征值彼此不同，矩阵可对角化，自由响应有界但一般不趋零，因此无损模型的原点 Lyapunov 稳定而非渐近稳定。

这只回答零输入内部运动。上述电压到电流通道含原点和虚轴极点，不是 BIBO 稳定；持续激励可能引起无界响应，不能把“自由有界”当作任意输入都安全。

## 3. 近 2 kHz 数值例与绕组电阻

### 3.1 统一单位计算无损谐振

取：

$$
L_1=0.001\ \mathrm H,
\qquad L_2=0.0005\ \mathrm H,
\qquad C_f=20\times10^{-6}\ \mathrm F.
$$

$$
\omega_{\mathrm{res}}
=\sqrt{\frac{0.0015}{0.001\cdot0.0005\cdot20\times10^{-6}}}
=\sqrt{150\times10^6}
\approx12247.45\ \mathrm{rad/s}.
$$

$$
f_{\mathrm{res}}=\frac{\omega_{\mathrm{res}}}{2\pi}
\approx1949.24\ \mathrm{Hz}.
$$

理想特征值为 $0,\pm j12247.45\ \mathrm s^{-1}$，约 1.95 kHz 表示滤波器的自然振荡尺度，不表示 PCS 必须输出这一频率。非零模态电流比为 $i_1/i_2=-0.5$。

### 3.2 两侧串联电阻的状态矩阵

加入 $R_1,R_2$ 后：

$$
L_1\dot i_1=v_c-v_f-R_1i_1,
\qquad
C_f\dot v_f=i_1-i_2,
\qquad
L_2\dot i_2=v_f-v_g-R_2i_2.
$$

取 $R_1=0.1\ \Omega$、$R_2=0.05\ \Omega$：

$$
A_R=
\begin{bmatrix}
-R_1/L_1&-1/L_1&0\\
1/C_f&0&-1/C_f\\
0&1/L_2&-R_2/L_2
\end{bmatrix}
=\begin{bmatrix}
-100&-1000&0\\
50000&0&-50000\\
0&2000&-100
\end{bmatrix}.
$$

输入和输出选择保持不变；矩阵各行仍分别对应电流、电压、电流变化率。

### 3.3 相等电阻电感比下的特征值

本例恰好 $R_1/L_1=R_2/L_2=100\ \mathrm s^{-1}$，所以：

$$
\det(\lambda I-A_R)
=(\lambda+100)(\lambda^2+100\lambda+150\times10^6).
$$

$$
\lambda_1=-100,
\qquad
\lambda_{2,3}=-50\pm j\sqrt{150\times10^6-2500}
\approx-50\pm j12247.35.
$$

这组因式分解依赖于本例相等的 $R/L$；一般参数应重新求特征值。原零模态开始衰减，谐振模态则近似仍在 1.95 kHz 振荡，但包络为 $e^{-50t}$，时间常数 20 ms。

### 3.4 左半平面不等于阻尼已充分

对 $\lambda=-\sigma\pm j\omega_d$、$\sigma>0$：

$$
\zeta=\frac{\sigma}{\sqrt{\sigma^2+\omega_d^2}}
\approx\frac{50}{\sqrt{50^2+12247.35^2}}
\approx0.00408.
$$

这是约 0.408%，不是 0.04 的阻尼比。负实部保证该模态最终衰减，却不保证工程上足够抗扰或谐振峰足够小。还需评估激发、时延、电网变化和被动/有源阻尼。

## 4. 储能、LaSalle 与 Lyapunov 论证

### 4.1 从储能得到端口功率平衡

对上述增量模型构造：

$$
W=\frac12L_1i_1^2+\frac12C_fv_f^2+\frac12L_2i_2^2.
$$

它是正定二次储能函数；在非零运行点附近，不将其直接当作整个电站的绝对总能量。逐项求导并代入状态方程：

$$
\begin{aligned}
\dot W
&=i_1(v_c-v_f-R_1i_1)+v_f(i_1-i_2)
+i_2(v_f-v_g-R_2i_2)\\
&=v_ci_1-v_gi_2-R_1i_1^2-R_2i_2^2.
\end{aligned}
$$

内部交叉功率抵消，剩下端口净输入与电阻耗散。这一符号关系依赖于本文两个电流的方向。

### 4.2 无损能量守恒与自由稳定性

零输入、无电阻时 $\dot W=0$。能量在储能元件间交换而不自行消失，正定守恒量限制自由状态大小，与零模态和共轭虚轴模态的有界运动相符，但不能推出状态趋于零。

### 4.3 正电阻下为什么还要检查不变集

零输入时：

$$
\dot W=-R_1i_1^2-R_2i_2^2\le0.
$$

这只是半负定；当两电流为零而电容电压非零时，瞬间也可能有 $\dot W=0$，所以不能仅凭这个不等式直接说状态收敛。

若 $R_1,R_2>0$，要持续停在 $\dot W=0$ 集合内，就必须始终有 $i_1=i_2=0$；再由两电感方程得 $v_f=0$。最大不变集只有原点。$W$ 的子水平集紧且正向不变，利用 LaSalle 原理得到此零输入线性模型的原点渐近稳定。

这里只完成本模型的不变集论证，不展开一般 LaSalle 定理证明，也不保证有源控制加入后物理储能仍单调下降。

### 4.4 一般二次 Lyapunov 方程

对 $\dot x=Ax$ 取 $V=x^TPx$、$P=P^T>0$，则：

$$
\dot V=x^T(A^TP+PA)x.
$$

若存在 $P>0$ 满足：

$$
A^TP+PA=-Q,\qquad Q=Q^T>0,
$$

则非零状态处 $\dot V<0$。对连续时间有限维 LTI，$A$ Hurwitz 等价于对每个 $Q>0$ 都存在唯一对称正定解 $P$。物理储能的权重不一定直接给出严格正定的 $Q$，不能把两种构造混为一谈。

求解器约定需核对：[MathWorks lyap](https://www.mathworks.com/help/control/ref/lyap.html)的标准形式为 $AX+XA^T+Q=0$，因此这里的方程对应实矩阵情形下的 `lyap(A',Q)`，不是直接不加转置地套用。

## 5. 自检、边界与章节索引

### 5.1 本篇应能独立重现的推导

1. 从三条电路方程写出 $A,B,C_y,D$ 并检查尺寸、符号。
2. 展开特征行列式，解释零模态和共轭谐振对。
3. 求电流模态比，复算 1.95 kHz、带损特征值及阻尼比。
4. 用端口功率与最大不变集解释无损和带损自由稳定性的区别。

### 5.2 工程限制与下一步

理想模型未包含全部 dq、接地、测量、控制器、数字延迟或电网反馈。加入控制后必须重建增广矩阵，不能继续用裸 LCL 的特征值代替闭环结论。更复杂的有源阻尼、网侧参数变化和实际采样控制仍需后续设计与仿真。

### 5.3 相关笔记与章节索引

- [并网变流器电流动态](converter-current-dynamics.md)
- [状态空间与自然模态](../foundations/control/state-space-eigenvalues-and-modes.md)
- [PI 与无损 LCL 的四状态闭环](../foundations/control/state-space-feedback-and-small-signal-stability.md#22-网侧电流-pi-反馈的具体增广矩阵)
- [带电阻的特征值算例](#33-相等电阻电感比下的特征值)
- [能量与不变集](#43-正电阻下为什么还要检查不变集)
- [频域稳定性与阻抗接口](../ibr-grid-integration/frequency-domain-stability-and-impedance-interfaces.md)
