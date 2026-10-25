# 状态空间、特征值与自然模态

状态空间把多条微分方程组织成矩阵，保留内部状态之间的作用；特征值与特征向量则分别说明模态怎样随时间变化、各状态如何共同运动。本文讨论连续时间有限维 LTI 模型，不把局部线性结论当作任意非线性工况的保证。

## 1. 状态空间的对象与完整响应

### 1.1 为什么不能只看一个输入输出分式

单状态 RL 可以用一条电流方程描述，但 LCL 需要同时跟踪两侧电感电流与电容电压。消元后的传递函数突出某条输入输出通道，状态空间则保留内部储能、控制器状态与耦合：

$$
\dot x=Ax+Bu,
\qquad
y=C_yx+Du.
$$

状态 $x$ 是给定当前值与未来输入后足以确定后续运动的独立内部变量；输入 $u$ 是从边界施加的作用；输出 $y$ 是选择观察的量，不必包含所有状态。“一个储能元件一个状态”只是常见直觉，特殊拓扑约束可能降低独立状态数量。

### 1.2 四个矩阵的角色与尺寸

若 $x\in\mathbb R^n$、$u\in\mathbb R^m$、$y\in\mathbb R^p$：

| 矩阵 | 尺寸 | 作用 |
| --- | --- | --- |
| $A$ | $n\times n$ | 零输入时状态间的动态作用 |
| $B$ | $n\times m$ | 输入如何驱动状态 |
| $C_y$ | $p\times n$ | 从状态组合得到输出 |
| $D$ | $p\times m$ | 输入到输出的直接代数馈通 |

输出矩阵写成 $C_y$，避免与电容 $C_f$ 混淆。具有不同单位的状态放在同一向量中时，矩阵各元素的单位也不同，不能认为每个元素都有相同物理单位。

### 1.3 自由响应与零状态受迫响应

定常 $A,B$ 下，完整解为：

$$
x(t)=e^{A(t-t_0)}x(t_0)
+\int_{t_0}^{t}e^{A(t-\tau)}Bu(\tau)\,d\tau.
$$

第一项是零输入自由响应，第二项是从零初始状态由输入产生的响应。矩阵指数为：

$$
e^{At}=I+At+\frac{(At)^2}{2!}+\cdots.
$$

研究 $A$ 的特征值主要是研究自由模态，不能因此忽略输入、饱和或外部反馈。小信号模型中“零输入”通常指输入增量为零，不是把实际电网电压断掉。

## 2. 从 RL 到特征值稳定判据

### 2.1 RL 的四个矩阵与自然指数

净电压输入的 RL 模型：

$$
L\dot i+Ri=u
\quad\Longrightarrow\quad
\dot i=-\frac RL i+\frac1L u.
$$

取 $x=i$、$y=i$：

$$
A=[-R/L],\quad B=[1/L],\quad C_y=[1],\quad D=[0].
$$

零输入下 $i(t)=i(0)e^{-Rt/L}$。唯一特征值 $\lambda=-R/L$ 描述这个自然衰减率；单状态最小实现的传递函数 $1/(Ls+R)$ 也有相同极点。

### 2.2 从试探解推导 Av = λv

在 $\dot x=Ax$ 中试取 $x(t)=ve^{\lambda t}$、$v\ne0$：

$$
\lambda ve^{\lambda t}=Ave^{\lambda t}
\quad\Longrightarrow\quad
Av=\lambda v.
$$

$\lambda$ 是时间变化率，$v$ 是该模态的状态相对结构。若 $\lambda=\sigma+j\omega_d$：

$$
e^{\lambda t}=e^{\sigma t}e^{j\omega_dt}.
$$

实部决定包络增长或衰减，虚部决定自然振荡角频率。这里的 $\omega_d$ 属于系统模态，不是 Bode 扫描中外部选择的测试 $\omega$。

### 2.3 Hurwitz、虚轴与 Jordan 条件

对连续时间齐次 LTI 系统 $\dot x=Ax$：

- 所有特征值实部严格小于零：原点指数稳定，也渐近稳定，$A$ 称 Hurwitz。
- 存在实部大于零的特征值：原点不稳定。
- 实部均不大于零、至少一个在虚轴：还要检查虚轴模态是否半单，即对应 Jordan 块是否全为一阶。若满足且其余模态严格在左半平面，则原点 Lyapunov 稳定但一般不渐近稳定。

“虚轴上有根”不能单独判断所有内部自由响应是否有界；内部稳定性也不能与指定传递函数的 BIBO 稳定性混为一谈。

### 2.4 读取一对衰减振荡模态

实矩阵的非实特征值成共轭对。例如 $\lambda=-50\pm j1000$ 对应真实响应中的：

$$
q(t)=a e^{-50t}\cos(1000t+\phi).
$$

包络时间常数为 20 ms，阻尼振荡角频率为 1000 rad/s，即约 159.15 Hz。一对共轭特征值共同组成一个真实振荡模式，不是两个互不相关的实物振荡器；状态数量也不等于不同谐振频率数量。

## 3. 串联 RLC 的耦合与阻尼

### 3.1 从 KVL、KCL 选择两个状态

采用被动符号约定，串联 RLC 的电压源为 $u$：

$$
u=Ri_L+L\dot i_L+v_C,
\qquad
i_L=C_f\dot v_C.
$$

令 $x=[i_L,v_C]^T$，则：

$$
\dot x=
\begin{bmatrix}-R/L&-1/L\\1/C_f&0\end{bmatrix}x
+\begin{bmatrix}1/L\\0\end{bmatrix}u.
$$

$-v_C/L$ 与 $i_L/C_f$ 是状态间耦合：电容电压影响电感电流，电感电流又给电容充放电。输出仍需另行选择，例如观察电容电压时 $C_y=[0,1]$、$D=0$。

### 3.2 行列式给出自然模态

$$
\det(\lambda I-A)
=\det\begin{bmatrix}\lambda+R/L&1/L\\-1/C_f&\lambda\end{bmatrix}
=\lambda^2+\frac RL\lambda+\frac1{LC_f}.
$$

因此：

$$
\lambda_{1,2}=-\frac R{2L}
\pm\sqrt{\left(\frac R{2L}\right)^2-\frac1{LC_f}}.
$$

与标准二阶式比较：

$$
\omega_n=\frac1{\sqrt{LC_f}},
\qquad
\zeta=\frac R2\sqrt{\frac{C_f}{L}}.
$$

对 $L,C_f>0$、$0<\zeta<1$，根为 $-\zeta\omega_n\pm j\omega_n\sqrt{1-\zeta^2}$；$\zeta=1$ 临界阻尼，$\zeta>1$ 过阻尼，$R=0$ 对应无损振荡。

### 3.3 状态数量不保证振荡

电感和电容交换能量，电阻以 $Ri_L^2$ 耗散能量，但两个独立状态也可以只有两个纯实衰减模态。是否存在振荡，应看模态结构，而不是只数储能元件。

LCL 的三状态、零模态与共轭谐振对，继续见 [LCL 谐振与模态稳定性](../../power-electronics/lcl-resonance-modal-stability.md)。

## 4. 特征向量、模态分解与 Jordan 反例

### 4.1 可对角化时的模态坐标

若 $A$ 有 $n$ 个线性无关右特征向量，令：

$$
V=[v_1,\ldots,v_n],\quad
\Lambda=\operatorname{diag}(\lambda_1,\ldots,\lambda_n),\quad
AV=V\Lambda.
$$

由 $x=Vz$ 得：

$$
\dot z=\Lambda z,
\qquad
x(t)=\sum_i v_i z_i(0)e^{\lambda_i t},
\qquad z(0)=V^{-1}x(0).
$$

初始条件决定自由响应中各模态的系数，输入与输出通道则决定模态能否被有效激发或观察。某个模态存在，不意味着每次实验都能清楚看到它。

特征向量可任意乘非零标量，分量也依赖状态单位和坐标选择；不能把未经归一化的分量当作绝对参与程度。模态形状与参与因子不是同义词，本课未正式推导参与因子。

### 4.2 两个纯实衰减模态的完整例子

$$
A=\begin{bmatrix}-2&1\\1&-2\end{bmatrix},
\quad
\lambda_1=-1,\ v_1=[1,1]^T,
\quad
\lambda_2=-3,\ v_2=[1,-1]^T.
$$

零输入解为：

$$
x(t)=a_1[1,1]^T e^{-t}+a_2[1,-1]^T e^{-3t}.
$$

一组同向、一组反向衰减，没有振荡。若 $x(0)=[x_{10},x_{20}]^T$，则 $a_1=(x_{10}+x_{20})/2$、$a_2=(x_{10}-x_{20})/2$。

### 4.3 缺陷矩阵为何需要多项式时间项

对 Jordan 块：

$$
A=\begin{bmatrix}-1&1\\0&-1\end{bmatrix},
\qquad
e^{At}=e^{-t}\begin{bmatrix}1&t\\0&1\end{bmatrix}.
$$

虽然出现 $te^{-t}$，仍最终衰减。若将两个对角元素改为零：

$$
A=\begin{bmatrix}0&1\\0&0\end{bmatrix},
\qquad
e^{At}=\begin{bmatrix}1&t\\0&1\end{bmatrix},
$$

则某些初值产生无界线性增长。这解释了虚轴特征值的半单条件，也说明重复特征值不一定能组成完整的独立特征向量基。

## 5. 传递矩阵与隐藏内部模态

### 5.1 保留初始状态再定义传递函数

Laplace 变换给出：

$$
(sI-A)X=x(0)+BU,
\qquad
X=(sI-A)^{-1}x(0)+(sI-A)^{-1}BU.
$$

采用零初始状态后：

$$
G(s)=C_y(sI-A)^{-1}B+D.
$$

多输入多输出时，$G$ 是传递矩阵。$\det(sI-A)=0$ 给出内部特征值，但经 $B$ 激发、$C_y$ 观察后，某些模态可在指定通道中隐藏或相消。最小实现意义下，内部动态与传递矩阵的动态极点相对应；不能要求某个标量元素显示全部模态。最小实现的解释可参考 [MathWorks 状态空间实现说明](https://www.mathworks.com/help/control/ug/canonical-state-space-realizations.html)。

### 5.2 稳定输入输出通道仍可隐藏不稳定状态

考虑：

$$
A=\begin{bmatrix}-1&0\\0&2\end{bmatrix},
\quad B=\begin{bmatrix}1\\0\end{bmatrix},
\quad C_y=[1,0],\quad D=0.
$$

从指定输入到输出：

$$
G(s)=\frac1{s+1}.
$$

但当 $x_2(0)\ne0$ 时，内部 $x_2(t)=x_2(0)e^{2t}$ 增长。输入不能驱动这个状态，输出也看不到它；稳定的零状态输入输出关系并没有证明内部稳定。

### 5.3 可控、可观与本课边界

可控性问能否通过已有输入影响所需内部状态；可观性问能否由已知输入和输出重建内部状态。本课用隐藏模态建立物理直觉，不把 Kalman 秩判据、Gramian 或观测器设计写成已经完成的推导。

## 6. 自检、研究边界与章节索引

### 6.1 应能独立复现的推导

1. 从 RL 写出四个矩阵并得到 $\lambda=-R/L$。
2. 从 RLC 的两条方程求行列式、特征值及 $\omega_n,\zeta$。
3. 用 $x=Vz$ 推出模态解，并解释初值、输出和坐标的作用。
4. 从含初值 Laplace 方程推出零状态传递矩阵，并解释隐藏不稳定模态。

### 6.2 常见误区与后续任务

- 状态多不等于一定振荡，共轭对也不是两个独立实物振荡器。
- 特征向量不是特征值，模态形状不是参与因子。
- 虚轴特征值必须结合 Jordan 结构判断自由响应。
- 某个通道稳定，不保证全部内部状态稳定。
- 参与因子、一般可控可观证明与非正规暂态放大留待后续专题。

### 6.3 相关笔记与章节索引

- [矩阵与坐标变换](../mathematics/matrices-and-coordinate-transformations.md)
- [微分方程与动态系统基础](../mathematics/differential-equations-and-dynamic-systems.md)
- [LCL 的零模态与谐振模态](../../power-electronics/lcl-resonance-modal-stability.md#23-零模态与共轭振荡模态)
- [状态反馈与小信号稳定性](state-space-feedback-and-small-signal-stability.md)
- [隐藏模态反例](#52-稳定输入输出通道仍可隐藏不稳定状态)
