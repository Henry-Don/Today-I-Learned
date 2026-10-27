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

这可以从同一模型的两条路线直接核对。零初始状态下：

$$
G(s)=\frac{I(s)}{U(s)}=\frac1{Ls+R},\qquad p=-\frac RL;
\qquad
\det(\lambda I-A)=\lambda+\frac RL=0.
$$

因此 $p=\lambda=-R/L$，描述的是同一种自然衰减。这里 $R,L>0$，$\lambda$ 的单位是 $\mathrm s^{-1}$，不是电流的安培数。例如 $\lambda=-20\ \mathrm s^{-1}$ 时，$i(t)=I_0e^{-20t}$ 的时间常数为 0.05 s，而幅值 $I_0$ 由初值决定。

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

### 2.5 有损 RL、RC 与理想积分器的区别

常规单状态串联 RC 以电源电压 $u$ 为输入、电容电压为状态，满足：

$$
RC_f\dot v_C+v_C=u,
\qquad \lambda=-\frac1{RC_f},
\qquad v_C(t)=v_C(0)e^{-t/(RC_f)}\quad(u=0).
$$

它与有损 RL 都只有负实自然模态，不会自行形成正弦振荡。理想电感去掉串联电阻，或理想电容直接以电流为输入时，才分别得到：

$$
L\dot i=u
\quad\Longrightarrow\quad
i(t)=i(0)+\frac1L\int_0^t u(\tau)\,d\tau,
$$

$$
C_f\dot v_C=i_C
\quad\Longrightarrow\quad
v_C(t)=v_C(0)+\frac1{C_f}\int_0^t i_C(\tau)\,d\tau.
$$

这两个一阶积分器的 $A=0$，零输入时保持常值；有损 RL/RC 则会自然衰减，不能统称为纯积分器。以上只针对给定的一阶拓扑和输入选择，不能推广为任意含 R、L 或 C 的网络都只有一个状态。

没有自然振荡模态，也仍可有正弦受迫响应。对 $R,L>0$ 的 RL 施加 $u(t)=U\cos\omega t$，稳态为：

$$
i_{\mathrm{ss}}(t)=\frac{U}{\sqrt{R^2+(\omega L)^2}}
\cos\left(\omega t-\arctan\frac{\omega L}{R}\right).
$$

其频率由外部输入决定，零输入自然响应仍是指数衰减。若从 $t=0$ 施加上述正弦输入，满足给定初始电流的完整响应为：

$$
i(t)=i_{\mathrm{ss}}(t)+\left[i(0)-i_{\mathrm{ss}}(0)\right]e^{-Rt/L}.
$$

自然项负责补足初始条件，正弦特解负责外部输入；不能直接把 $i(0)e^{-Rt/L}$ 加到稳态项上而仍要求同一初值。

| 模型与前提 | 独立状态数 | 零输入自然特征值 | 自然运动 |
| --- | --- | --- | --- |
| 常规有损 RL / RC | 1 | $-R/L$ / $-1/(RC_f)$ | 指数衰减 |
| 理想单 L / 单 C 积分器 | 1 | $0$ | 保持常值 |
| 理想 LC | 2 | $\pm j/\sqrt{LC_f}$ | 持续振荡 |
| 串联 RLC | 2 | 由 $R,L,C_f$ 决定 | 欠阻尼、临界阻尼或过阻尼 |
| 本文无损 LCL | 3 | $0,\pm j\omega_{\mathrm{res}}$ | 常量模态加振荡模态 |

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

### 3.4 非对角元素怎样体现状态耦合

对零输入线性系统：

$$
\dot x_i=a_{ii}x_i+\sum_{j\ne i}a_{ij}x_j.
$$

在选定坐标中，$a_{ii}$ 是自身状态对自身变化率的贡献，非零 $a_{ij}$ 表示 $x_j$ 直接影响 $\dot x_i$。RLC 中的 $v_C\to\dot i_L$ 与 $i_L\to\dot v_C$ 构成双向动态耦合；应明确影响的是变化率，而不是说两个量在公式中同时出现就算耦合。

耦合也可以单向存在。例如 $\dot x_1=-x_1+x_2$、$\dot x_2=-2x_2$ 中，只有 $x_2\to\dot x_1$ 的直接作用。双向耦合也不保证振荡：[两负实根的完整例子](#42-两个纯实衰减模态的完整例子)具有非零非对角元，却只有指数衰减。

耦合的矩阵表现依赖坐标。可对角化时，模态坐标把自由运动写成相互独立的复标量方程；若坚持使用实坐标，非实共轭模态通常组合成实 $2\times2$ 块，而不是实对角矩阵。坐标变换没有断开真实元件，相似变换也不改变特征值。相关形式见 [MathWorks 模态实现说明](https://www.mathworks.com/help/control/ug/canonical-state-space-realizations.html)。

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

令 $c_i=z_i(0)$，则同一公式也写为 $x(t)=\sum_i c_iv_ie^{\lambda_i t}$，其中时间原点取为 0：

| 对象 | 决定什么 | 不能单独决定什么 |
| --- | --- | --- |
| $\lambda_i$ | 第 $i$ 个模态的增长、衰减与振荡速率 | 电流或电压的瞬时数值与幅值 |
| $v_i$ | 该模态内各状态的相对复幅值与相位 | 脱离归一化约定的绝对参与程度 |
| $c_i$ | 给定初值激发该模态的权重 | 仅凭特征值无法算出它 |
| $x(t)$ | 所有模态叠加后的状态 | 不应把一个状态固定配给一个特征值 |

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

### 4.4 从共轭模态得到实际电流与电压

对实矩阵、实初值，若 $\lambda_+=\sigma+j\omega_d$、$v_+$ 与 $c_+$ 对应一个模态，其共轭项组合为：

$$
x_{\mathrm{pair}}(t)
=c_+v_+e^{(\sigma+j\omega_d)t}
+\overline{c_+v_+}e^{(\sigma-j\omega_d)t}
=2\operatorname{Re}\left(c_+v_+e^{(\sigma+j\omega_d)t}\right).
$$

第 $k$ 个物理状态的这一模态分量便是：

$$
x_{k,\mathrm{pair}}(t)=A_ke^{\sigma t}\cos(\omega_dt+\phi_k).
$$

$A_k,\phi_k$ 来自 $c_+$ 与 $v_+$ 的第 $k$ 个分量；系数为零时该状态不显示此模态。对 $x=[i_L,v_C]^T$，电流和电压可共享相同衰减率与振荡角频率，但具有不同幅值、相位和单位。$-50\pm j1000$ 中的 $-50$、$1000$ 是速率，并不是安培数或伏特数。

这只是所选共轭对的贡献；完整自由响应还要加其他模态，完整实际响应还需加 [零状态受迫项](#13-自由响应与零状态受迫响应)。缺陷矩阵需使用上一小节的广义模态，不能无条件套用完备特征向量展开。具体电流波形见 [带阻尼 LCL 的模态叠加](../../power-electronics/lcl-resonance-modal-stability.md#35-网侧电流如何叠加三个自由模态)。

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

由伴随矩阵公式，在 $sI-A$ 可逆处有：

$$
(sI-A)^{-1}=\frac{\operatorname{adj}(sI-A)}{\det(sI-A)},
\qquad
G(s)=\frac{C_y\operatorname{adj}(sI-A)B+D\det(sI-A)}{\det(sI-A)}.
$$

这说明分母为什么涉及特征多项式；作为有理函数仍须约分，不能把所有候选分母根都当作指定通道的实际极点。$\det(sI-A)$ 与 $\det(\lambda I-A)$ 是同一个多项式的不同自变量写法，求根后才得到具体特征值。

因此两条路径应分清：$A\to\det(\lambda I-A)=0\to$ 内部特征值；$(A,B,C_y,D)\to G(s)\to$ 指定通道的极点与零点。单独的 $A$ 不足以决定 $G$；改变输入输出选择可改变可见模态与零点。零点也不是 $A$ 的另一类特征值。给定传递函数可有多种状态实现，最小实现之间通过可逆状态坐标变换联系。

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

### 5.4 串联 RLC 的状态与消元路线核对

采用第 3.1 节的同一电路，选 $y=v_C$，即 $C_y=[0,1]$、$D=0$。由 $i_L=C_f\dot v_C$ 消去电流：

$$
LC_f\ddot v_C+RC_f\dot v_C+v_C=u.
$$

在 $i_L(0)=v_C(0)=0$ 的传递函数约定下：

$$
G(s)=\frac{V_C(s)}{U(s)}=\frac1{LC_fs^2+RC_fs+1},
\qquad
\det(sI-A)=s^2+\frac RL s+\frac1{LC_f}.
$$

两分母只差非零常数 $LC_f$；从 $C_y(sI-A)^{-1}B$ 也得到同一 $G$。本例 $L,C_f>0$，分子为非零常数，没有极零相消，是二阶最小实现，两个传递极点与两个状态特征值计重数一致。

这份对应依赖完整输入输出选择及最小实现。不能推广成“任意传递通道总能看到所有内部模态”；第 5.2 节的隐藏 $+2$ 特征值正是反例。

## 6. 自检、研究边界与章节索引

### 6.1 应能独立复现的推导

1. 从 RL 写出四个矩阵并得到 $\lambda=-R/L$。
2. 从 RLC 的两条方程求行列式、特征值及 $\omega_n,\zeta$。
3. 用 $x=Vz$ 推出模态解，并解释初值、输出和坐标的作用。
4. 从含初值 Laplace 方程推出零状态传递矩阵，并解释隐藏不稳定模态。
5. 对照有损 RL/RC 与理想积分器，区分自然振荡和正弦受迫响应。
6. 用 RLC 两条路线核对分母，并说明 $\lambda_i,v_i,c_i,x(t)$ 的不同角色。

### 6.2 常见误区与后续任务

- 状态多不等于一定振荡，共轭对也不是两个独立实物振荡器。
- 特征向量不是特征值，模态形状不是参与因子。
- 虚轴特征值必须结合 Jordan 结构判断自由响应。
- 某个通道稳定，不保证全部内部状态稳定。
- 非对角项表示选定坐标中的动态耦合，不保证存在振荡。
- 从 $A$ 单独求不出指定 $G(s)$；从特征值单独求不出物理响应幅值。
- 参与因子、一般可控可观证明与非正规暂态放大留待后续专题。

### 6.3 相关笔记与章节索引

- [矩阵与坐标变换](../mathematics/matrices-and-coordinate-transformations.md)
- [微分方程与动态系统基础](../mathematics/differential-equations-and-dynamic-systems.md)
- [LCL 的零模态与谐振模态](../../power-electronics/lcl-resonance-modal-stability.md#23-零模态与共轭振荡模态)
- [状态反馈与小信号稳定性](state-space-feedback-and-small-signal-stability.md)
- [隐藏模态反例](#52-稳定输入输出通道仍可隐藏不稳定状态)
- [RL、RC、积分器与受迫正弦](#25-有损-rlrc-与理想积分器的区别)
- [状态耦合的含义与坐标边界](#34-非对角元素怎样体现状态耦合)
- [模态如何组成实际电流与电压](#44-从共轭模态得到实际电流与电压)
- [RLC 的状态与消元路线](#54-串联-rlc-的状态与消元路线核对)
- [闭环特征值、根轨迹与劳斯判据](state-space-feedback-and-small-signal-stability.md#13-闭环特征值根轨迹与劳斯判据)
