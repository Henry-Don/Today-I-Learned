# 极点、零点、带宽与稳态误差

## 1. 从传递函数到控制性能

传递函数：

$$
G(s)=\frac{N(s)}{D(s)}
$$

把微分方程压缩成输入输出动态关系。控制设计的下一步，是从 $N(s)$、$D(s)$ 和闭环结构中读取：

- 自然响应是否衰减；
- 响应有多快、是否振荡；
- 不同频率能否被跟踪或抑制；
- 稳态误差能否消除；
- PI 参数如何改变闭环极点和带宽。

本笔记沿着以下主线展开：

$$
\text{poles and zeros}
\rightarrow
\text{time-domain response}
\rightarrow
\text{frequency response}
\rightarrow
\text{closed loop}
\rightarrow
\text{steady-state error}
\rightarrow
\text{PI current-loop foundation}
$$

## 2. 极点的定义

对已经约简的有理传递函数：

$$
G(s)=\frac{N(s)}{D(s)}
$$

使分母为零的根称为极点：

$$
D(p_i)=0
$$

例如 RL plant：

$$
G(s)=\frac{1}{Ls+R}
$$

满足：

$$
Ls+R=0
$$

所以极点为：

$$
p=-\frac{R}{L}
$$

## 3. 极点对应自然模态

极点 $p$ 对应的时间域自然模态具有：

$$
e^{pt}
$$

的形式。对 RL plant：

$$
e^{-Rt/L}
$$

正是无外部输入时电流的自然衰减。

因此极点不仅是“分母的根”，更重要的是：

$$
\text{pole location}
\longleftrightarrow
\text{natural mode}
\longleftrightarrow
\text{transient behavior}
$$

## 4. 从极点位置读取稳定性

若：

$$
p=\sigma+j\omega
$$

对应模态为：

$$
e^{pt}=e^{\sigma t}e^{j\omega t}
$$

所以：

- $\sigma<0$：模态衰减；
- $\sigma>0$：模态增长；
- $\omega\neq0$：模态振荡。

对经典连续时间有理 LTI 输入输出模型，所有极点位于开左半平面是 BIBO 稳定的基本条件。

传递函数只反映特定输入输出通道。若系统存在不可控、不可观或被精确相消的内部模态，还需要状态空间模型判断内部稳定性。

## 5. 极点离虚轴的距离与主导极点

例如：

$$
p_1=-1,
\qquad
p_2=-100
$$

对应：

$$
e^{-t},
\qquad
e^{-100t}
$$

第二个模态衰减得更快。对稳定实极点，越靠左通常意味着时间尺度越快。

最靠近虚轴的稳定极点衰减最慢，常在总响应中持续时间最长，因此更可能成为 dominant pole。但模态在特定输入输出中的可见程度还取决于对应残差和耦合强度。

## 6. 零点的定义与作用

使分子为零的根称为零点：

$$
N(z_i)=0
$$

极点主要决定系统能够产生哪些自然动态，零点则塑造某个输入输出通道如何激发和组合这些动态。

零点会影响：

- 上升过程和超调；
- 频率响应幅值；
- 相位特性；
- 瞬态方向；
- 可实现控制带宽。

因此零点不是可以忽略的分子细节。

## 7. Pole-Zero Cancellation 的边界

代数上：

$$
\frac{s-a}{(s-a)(s+b)}
=
\frac{1}{s+b}
$$

但实际参数存在误差，控制器零点很难与 plant 极点永久精确重合。

尤其不能依赖控制器零点精确相消不稳定极点。即使输入输出传递函数表面上发生相消，不稳定内部模态仍可能保留，并被噪声、初始条件或非线性激发。

因此：

$$
\text{unstable pole-zero cancellation is not robust}
$$

## 8. 右半平面零点

若零点位于右半平面：

$$
z>0
$$

它是 non-minimum-phase zero，可能导致：

- inverse response；
- 额外相位滞后；
- 上升时间和带宽限制；
- 更困难的稳定裕度折中。

右半平面零点虽然不是不稳定极点，却会显著限制控制性能。

## 9. 标准一阶系统

标准一阶传递函数为：

$$
G(s)=\frac{K}{\tau s+1}
$$

极点为：

$$
p=-\frac{1}{\tau}
$$

单位阶跃输入：

$$
R(s)=\frac{1}{s}
$$

对应输出：

$$
y(t)=K\left(1-e^{-t/\tau}\right)
$$

当 $t=\tau$ 时，输出完成最终变化量的约：

$$
1-e^{-1}\approx63.2\%
$$

暂态误差约剩 36.8%。

## 10. 时间常数与 Settling Time

一阶系统中：

$$
p=-\frac{1}{\tau}
$$

所以：

$$
\tau=\frac{1}{|p|}
$$

常见近似为：

$$
t_{s,5\%}\approx3\tau
$$

$$
t_{s,2\%}\approx4\tau
$$

因此：

$$
|p|\uparrow
\Longrightarrow
\tau\downarrow
\Longrightarrow
\text{response faster}
$$

这些是标准一阶系统的近似，不应无条件套用到任意高阶或含零点系统。

## 11. 频率响应

沿虚轴令：

$$
s=j\omega
$$

得到频率响应：

$$
G(j\omega)
$$

其中：

$$
|G(j\omega)|
$$

表示不同频率正弦输入的稳态幅值缩放，而：

$$
\angle G(j\omega)
$$

表示相位偏移。

对标准一阶系统：

$$
G(j\omega)
=
\frac{K}{1+j\omega\tau}
$$

所以：

$$
\left|G(j\omega)\right|
=
\frac{|K|}{\sqrt{1+(\omega\tau)^2}}
$$

$$
\angle G(j\omega)
=
-\tan^{-1}(\omega\tau)
$$

该相位公式假设 $K>0$、$\tau>0$；负增益还要计入其相角。频率响应针对稳定系统的受迫正弦稳态，不包含所有初始暂态，也不会改变原极点。具体的 RL 暂态与稳态拆分见 [Laplace 笔记](../mathematics/laplace-transform-and-transfer-functions.md#151-自然暂态与受迫正弦稳态)。

### 11.1 RL 的低频与高频要相对特征频率判断

取 $L,R>0$：

$$
G(j\omega)=\frac{1}{R+j\omega L},
\qquad
\omega_p=\frac{R}{L}.
$$

幅值与相位同时变化：

$$
|G(j\omega)|=\frac{1}{\sqrt{R^2+(\omega L)^2}},
\qquad
\angle G(j\omega)=-\arctan\frac{\omega L}{R}.
$$

- $\omega\ll\omega_p$：幅值约为 $1/R$，相位接近 $0^\circ$。
- $\omega=\omega_p$：幅值为 DC 增益的 $1/\sqrt2$，相位为 $-45^\circ$。
- $\omega\gg\omega_p$：幅值约为 $1/(\omega L)$，相位趋近 $-90^\circ$。

所以“低频/高频”不是固定的 Hz 分界，而是相对于所选模型的特征频率。例如 $L=0.01\ \mathrm H$、$R=0.1\ \Omega$，输入电压峰值为 1 V：

| 激励角频率 | 稳态电流峰值 | 电流相位 |
| --- | --- | --- |
| $1\ \mathrm{rad/s}$ | 约 9.95 A | 约 $-5.7^\circ$ |
| $10\ \mathrm{rad/s}$ | 约 7.07 A | $-45^\circ$ |
| $100\ \mathrm{rad/s}$ | 约 0.995 A | 约 $-84.3^\circ$ |

电压直接决定的是 $\dot i$，而非瞬间指定 $i$；激励变化快时，电流幅值更小、相位更滞后。这是输入电压与输出电流之间的相位关系，不等同于固定时间延迟。对单一频率，等效时间偏移为 $|\phi|/\omega$（$\phi$ 用弧度），一般随频率变化。

### 11.2 滤波、跟踪与稳定裕度是三个问题

1. **滤波：** 不希望传递的 PWM 高频电压成分产生较小的电流纹波，幅值衰减是目标。LCL 还含谐振，不能只看远离谐振的高频渐近线。
2. **跟踪：** 对希望电流快速跟随的参考，应看参考到电流的闭环通道。$v_L=L\dot i$ 要求足够的电压裕量；DC-link 与调制限幅可能使快指令无法实现。
3. **稳定裕度：** 应看完整环路 $L_{\mathrm{loop}}(j\omega)$，包括控制器、plant、测量、滤波及采样/PWM 延迟。裸 RL 的高频相位接近 $-90^\circ$，不足以单独推出闭环失稳。

对常见单一增益交越、满足相应开环条件的负反馈环路，相位裕度可读为 $180^\circ+\angle L_{\mathrm{loop}}(j\omega_c)$。交越附近总相位越接近 $-180^\circ$，裕度越小；多重交越或开环不稳定等情况，还需结合完整 Nyquist 判据。

## 12. 一阶系统带宽

对低通系统，经典 $-3\ \mathrm{dB}$ 带宽定义为：

$$
\left|G(j\omega_{\mathrm{BW}})\right|
=
\frac{|G(0)|}{\sqrt{2}}
$$

标准一阶低通满足：

$$
\omega_{\mathrm{BW}}
=
\frac{1}{\tau}
$$

换算为 Hz：

$$
f_{\mathrm{BW}}
=
\frac{1}{2\pi\tau}
$$

所以在标准一阶系统中，极点、时间常数和带宽是同一速度尺度的不同表达：

$$
p=-\omega_{\mathrm{BW}}=-\frac{1}{\tau}
$$

## 13. RL Plant 的统一尺度

对：

$$
G(s)=\frac{1}{Ls+R}
$$

有：

$$
\tau=\frac{L}{R}
$$

$$
p=-\frac{R}{L}
$$

$$
\omega_p=\frac{R}{L}
$$

$$
f_p=\frac{R}{2\pi L}
$$

若：

$$
L=0.01\ \mathrm{H},
\qquad
R=0.1\ \Omega
$$

则：

$$
\tau=0.1\ \mathrm{s}
$$

$$
p=-10\ \mathrm{rad/s}
$$

$$
f_p\approx1.59\ \mathrm{Hz}
$$

这是裸 RL plant 的自然时间尺度，不是加入控制器后的电流环带宽。

## 14. 标准二阶系统

标准二阶低通形式为：

$$
G(s)
=
\frac{K\omega_n^2}
{s^2+2\zeta\omega_n s+\omega_n^2}
$$

其中：

- $\omega_n$：自然角频率，主要决定整体时间尺度；
- $\zeta$：阻尼比，主要决定振铃与超调程度。

极点为：

$$
s_{1,2}
=
-\zeta\omega_n
\pm
\omega_n\sqrt{\zeta^2-1}
$$

当 $0<\zeta<1$ 时：

$$
s_{1,2}
=
-\zeta\omega_n
\pm
j\omega_n\sqrt{1-\zeta^2}
$$

定义阻尼振荡频率：

$$
\omega_d
=
\omega_n\sqrt{1-\zeta^2}
$$

## 15. 二阶极点的物理读取

对：

$$
p=-a\pm jb
$$

时间响应包含：

$$
e^{-at}\cos(bt+\phi)
$$

因此：

- $a$ 决定指数衰减包络；
- $b$ 决定振荡角频率。

例如：

$$
p=-50\pm j200
$$

表示响应约以 $200\ \mathrm{rad/s}$ 振荡，同时包络按 $e^{-50t}$ 衰减。

## 16. 阻尼比、超调与 Settling Time

标准二阶系统常按阻尼比分为：

- $\zeta>1$：overdamped；
- $\zeta=1$：critically damped；
- $0<\zeta<1$：underdamped；
- $\zeta=0$：理想无阻尼持续振荡；
- $\zeta<0$：极点实部为正，出现增长动态。

标准欠阻尼二阶系统的阶跃超调比为：

$$
M_p
=
e^{-\frac{\pi\zeta}{\sqrt{1-\zeta^2}}}
$$

常用 2% settling-time 近似为：

$$
t_s
\approx
\frac{4}{\zeta\omega_n}
$$

这些公式依赖标准二阶、欠阻尼和主导极点等假设。

## 17. 二阶带宽

二阶系统的带宽同时取决于 $\omega_n$ 和 $\zeta$，不能普遍写成：

$$
\omega_{\mathrm{BW}}=\omega_n
$$

标准二阶低通的 $-3\ \mathrm{dB}$ 带宽为：

$$
\omega_{\mathrm{BW}}
=
\omega_n
\sqrt{
1-2\zeta^2
+
\sqrt{2-4\zeta^2+4\zeta^4}
}
$$

该公式不需要背诵。重点是自然频率与阻尼比会共同改变闭环速度和频率响应。

## 18. 为什么带宽不是越高越好

真实变流器系统存在：

- sampling delay；
- computation delay；
- PWM delay；
- switching-frequency 限制；
- measurement noise；
- LCL resonance；
- 未建模高频动态；
- 变化的电网阻抗。

提高带宽可以加快跟踪和扰动抑制，但也会让闭环更接近延迟、噪声和未建模共振区域，降低稳定裕度。

因此：

$$
\text{bandwidth is a speed-robustness tradeoff}
$$

## 19. Unity Negative Feedback

设 plant 为 $G(s)$，controller 为 $C(s)$。

这里 $R(s)$ 表示参考的 Laplace 变换，不是电阻 $R$；$L_{\mathrm{loop}}(s)$ 表示环路传递函数，不是电感 $L$。

Plant 是选定边界内的被控过程，不专指整个电网。对电流控制的净电压输入 $u=v_c-v_g$，RL 动态是 plant；PI 根据 $e=i^*-i$ 生成电压控制量。controller、plant 与 feedback 一起构成闭环系统，PI 并没有被排除在系统之外。PWM、测量和电网扰动归入哪个框，需要按实际模型明说。

单位负反馈满足：

$$
Y(s)
=
G(s)C(s)[R(s)-Y(s)]
$$

定义环路传递函数：

$$
L_{\mathrm{loop}}(s)
=
C(s)G(s)
$$

闭环跟踪传递函数为：

$$
T(s)
=
\frac{Y(s)}{R(s)}
=
\frac{L_{\mathrm{loop}}(s)}
{1+L_{\mathrm{loop}}(s)}
$$

闭环极点由特征方程：

$$
1+L_{\mathrm{loop}}(s)=0
$$

决定。

## 20. Sensitivity Function

误差为：

$$
E(s)=R(s)-Y(s)
$$

由前向通道 $Y(s)=C(s)G(s)E(s)$，逐步代入可得：

$$
\begin{aligned}
E(s)&=R(s)-C(s)G(s)E(s),\\
E(s)[1+C(s)G(s)]&=R(s).
\end{aligned}
$$

因此：

$$
\frac{E(s)}{R(s)}
=
\frac{1}{1+L_{\mathrm{loop}}(s)}
$$

定义灵敏度函数：

$$
S(s)
=
\frac{1}{1+L_{\mathrm{loop}}(s)}
$$

并有：

$$
T(s)+S(s)=1
$$

这也可直接由两个已推导的通道验证：

$$
1-T(s)
=1-\frac{C(s)G(s)}{1+C(s)G(s)}
=\frac{1}{1+C(s)G(s)}
=S(s).
$$

$S(s)$ 的大写 S 与复频率变量 $s$ 不同。这里把 $S$ 识别为 $E/R=1-T$，依赖单位负反馈及 $E=R-Y$ 的约定。非单位反馈 $H(s)$ 下，比较节点误差为 $E=R-HY$，应重新推导，而不能把参考到实际输出的通道机械代入 $1-T$。

在稳定和模型适用的前提下，低频环路增益越大，低频跟踪误差与某些扰动影响通常越小。但不能在所有频率同时让 $S$ 任意小，实际设计仍受稳定性和鲁棒性限制。

## 21. 稳态误差

定义：

$$
e(t)=r(t)-y(t)
$$

若极限存在：

$$
e_{\mathrm{ss}}
=
\lim_{t\rightarrow\infty}e(t)
$$

稳态误差与响应速度是不同指标：

- 系统可以响应很快，但最终停在错误值；
- 系统也可以最终零误差，但收敛很慢或振荡明显。

完整设计需要同时考虑稳态精度、速度、超调、稳定裕度与鲁棒性。

## 22. Final Value Theorem

满足适用条件时：

$$
\lim_{t\rightarrow\infty}e(t)
=
\lim_{s\rightarrow0}sE(s)
$$

使用前必须确认最终值存在。常用充分检查是 $sE(s)$ 的所有极点均位于开左半平面。

若系统发散、持续振荡或不满足相关极点条件，就不能机械套用 Final Value Theorem。

## 23. P 控制的阶跃稳态误差

RL plant：

$$
G(s)=\frac{1}{Ls+R}
$$

比例控制器：

$$
C(s)=K_p
$$

对单位阶跃参考：

$$
R(s)=\frac{1}{s}
$$

若闭环稳定，稳态误差为：

$$
e_{\mathrm{ss}}
=
\frac{1}{1+K_pG(0)}
$$

因为：

$$
G(0)=\frac{1}{R}
$$

所以：

$$
e_{\mathrm{ss}}
=
\frac{1}{1+K_p/R}
$$

有限 $K_p$ 下通常仍存在非零阶跃误差。无限增大 $K_p$ 也不是工程解法，因为噪声、延迟、饱和和稳定裕度会形成限制。

## 24. PI 为什么能消除阶跃稳态误差

PI 控制器为：

$$
C(s)
=
K_p+\frac{K_i}{s}
$$

当 $s\rightarrow0$：

$$
\frac{K_i}{s}\rightarrow\infty
$$

因此理想低频环路增益趋于无穷大。若闭环稳定且执行器未持续饱和，则对阶跃参考：

$$
e_{\mathrm{ss}}=0
$$

核心不是“积分器让系统更快”，而是积分器提供理想的无限 DC 增益。

## 25. PI 的极点与零点

PI 可写成：

$$
C(s)
=
K_p
\frac{s+K_i/K_p}{s}
$$

因此 PI 含有：

积分极点：

$$
p=0
$$

以及零点：

$$
z=-\frac{K_i}{K_p}
$$

上述形式取 $K_p\ne0$；真正含积分动态的 PI 还要求 $K_i\ne0$。若 $K_i=0$，就退化为无动态状态的 P 控制。

单独理想 PI 可由一个积分状态实现：

$$
\dot\xi=e,
\qquad
u_{\mathrm{PI}}=K_p e+K_i\xi.
$$

因此它是一阶控制器，带有比例直通项；不能因为“一阶”就把它当作 $K/(\tau s+1)$ 那样的严格真有理低通。

调整 $K_p$ 和 $K_i$ 同时会：

- 改变环路增益；
- 移动 PI 零点；
- 改变闭环特征方程；
- 改变带宽、阻尼和稳定裕度。

## 26. 理想 RL 电流环 PI 起点

RL plant 可写成：

$$
G(s)
=
\frac{1/L}{s+R/L}
$$

plant 极点为：

$$
p_p=-\frac{R}{L}
$$

回顾补充：在无额外 $Ri$ 状态补偿、理想净电压执行及单位负反馈下，任意 PI 参数对应：

$$
T(s)
=\frac{C(s)G(s)}{1+C(s)G(s)}
=\frac{K_p s+K_i}{Ls^2+(R+K_p)s+K_i}.
$$

电感电流与 PI 积分器通常组成两个动态状态，因此一般是二阶闭环，而不是由“PI 是一阶”推出整个闭环是一阶。对 $L,R,K_p,K_i>0$ 的这个理想模型，二阶特征多项式稳定；加入延迟等动态后需要重新判断。

若选择：

$$
\frac{K_i}{K_p}
=
\frac{R}{L}
$$

则 PI 零点与理想 plant 极点对齐。开环变为：

$$
L_{\mathrm{loop}}(s)
=
\frac{K_p/L}{s}
$$

闭环为：

$$
T(s)
=
\frac{K_p/L}{s+K_p/L}
$$

定义目标角频率：

$$
\omega_c=\frac{K_p}{L}
$$

得到经典参数：

$$
K_p=L\omega_c
$$

$$
K_i=R\omega_c
$$

理想闭环为：

$$
T(s)
=
\frac{\omega_c}{s+\omega_c}
$$

因此：

$$
\tau_{\mathrm{cl}}
=
\frac{1}{\omega_c}
$$

这把目标闭环时间尺度直接连接到 PI 参数。

精确匹配时分母可因式分解为：

$$
Ls^2+(R+K_p)s+K_i
=L(s+R/L)(s+K_p/L).
$$

参考到电流的分子含 $s+R/L$，因此约简后为一阶。但被约去的稳定模态不意味着物理电感或积分状态消失；其他输入通道、非零初始状态与参数误差仍可能使它显现。具体状态模型见 [系统阶数小节](#321-pi-rl-闭环的两个状态)。

## 27. 理想 PI 设计的工程边界

经典设计假设：

- $R$、$L$ 已知且恒定；
- plant 是单一 RL 环节；
- 电网电压前馈和 dq 解耦理想，PI 所见对象仍为 $1/(Ls+R)$；
- 采样、计算和 PWM 没有延迟；
- 调制器没有饱和；
- 不存在 LCL 共振和显著电网动态。

真实系统不满足这些理想条件，因此：

$$
K_p=L\omega_c,
\qquad
K_i=R\omega_c
$$

是重要的设计起点，不是完整设计终点。最终还需检查 Bode 图、相位裕度、参数不确定性、延迟、限幅和 anti-windup。

若另外使用精确的 $Ri$ 状态反馈补偿，使 PI 所见 plant 变成 $1/(Ls)$，就已经换了被控对象，不能直接沿用本节的 RL 极点匹配规则。两种结构的区别见 [电流控制与前馈](../../power-electronics/converter-current-dynamics.md#9-pi-电流控制与前馈)。

## 28. Crossover Frequency 与 Closed-Loop Bandwidth

增益交越频率定义于开环：

$$
\left|
L_{\mathrm{loop}}(j\omega_c)
\right|
=
1
$$

对具有有限非零 DC 增益的低通跟踪通道，闭环带宽通常定义为：

$$
\left|
T(j\omega_{\mathrm{BW}})
\right|
=
\frac{|T(0)|}{\sqrt{2}}
$$

二者可能数值接近，在理想匹配 PI 电流环中甚至相同，但定义不同。“定义不同”不意味着“数值一定不等”，应分别计算。

看到“带宽”时必须确认它指 plant bandwidth、current-loop bandwidth、PLL bandwidth、outer-loop bandwidth、闭环 tracking bandwidth，还是 open-loop crossover。

### 28.1 理想匹配 PI-RL 为什么数值相等

取 $L,R,K_p>0$、$K_i/K_p=R/L$，且满足前节的理想假设：

$$
|L_{\mathrm{loop}}(j\omega)|=\frac{K_p}{L\omega}.
$$

令其等于 1，得到 $\omega_c=K_p/L$。再令 $a=K_p/L$，闭环 $T(s)=a/(s+a)$ 且 $T(0)=1$，所以：

$$
\frac{a}{\sqrt{a^2+\omega_{\mathrm{BW}}^2}}
=\frac{1}{\sqrt2}
\quad\Longrightarrow\quad
2a^2=a^2+\omega_{\mathrm{BW}}^2.
$$

因此只在此理想匹配模型下：

$$
\omega_c=\omega_{\mathrm{BW}}=\frac{K_p}{L}.
$$

$1/\sqrt2\approx0.7071$ 是幅值比例，对应约 $-3\ \mathrm{dB}$，不是频率。不能写成 $\omega_{\mathrm{BW}}=K_p/\sqrt2$。

电流误差到电压输出的物理单位为：

$$
[K_p]=\Omega,
\qquad
[K_i]=\Omega/\mathrm s,
\qquad
[L]=\mathrm H=\Omega\cdot\mathrm s.
$$

所以 $K_p/L$ 的量纲是 $\mathrm s^{-1}$，可用于角频率；$K_p/\sqrt2$ 仍为欧姆，不能作为频率。如果实际软件使用标幺量或归一化指令，还需计入对应基值与执行器增益。

例如 $L=0.01\ \mathrm H$、$R=0.1\ \Omega$、$K_p=1\ \Omega$，匹配得到 $K_i=10\ \Omega/\mathrm s$：

$$
T(s)=\frac{100}{s+100},
\qquad
|T(j100)|=\frac{1}{\sqrt2}.
$$

于是 $\omega_c=\omega_{\mathrm{BW}}=100\ \mathrm{rad/s}$，换算为 $f=100/(2\pi)\approx15.9\ \mathrm{Hz}$，闭环时间常数为 $0.01\ \mathrm s$。

### 28.2 仅用 P 控制的反例

若 $C(s)=K_p$、$G(s)=1/(Ls+R)$，且 $K_p>R>0$：

$$
\frac{K_p}{\sqrt{R^2+(\omega_c L)^2}}=1
\quad\Longrightarrow\quad
\omega_c=\frac{\sqrt{K_p^2-R^2}}{L}.
$$

闭环为：

$$
T(s)=\frac{K_p}{Ls+R+K_p},
\qquad
T(0)=\frac{K_p}{R+K_p}.
$$

相对于自身 DC 增益的 $-3\ \mathrm{dB}$ 带宽是：

$$
\omega_{\mathrm{BW}}=\frac{R+K_p}{L}.
$$

同样取 $L=0.01\ \mathrm H$、$R=0.1\ \Omega$、$K_p=1\ \Omega$，二者分别约为 $99.5$ 与 $110\ \mathrm{rad/s}$，并不相等。若 $0<K_p<R$，开环幅值在 DC 已低于 1，没有正频率增益交越；这不妨碍该理想 P-RL 闭环稳定且具有有限带宽。

## 29. System Type 与稳态误差

开环在原点的积分极点数量定义 system type：

- Type 0：没有积分器；
- Type 1：一个积分器；
- Type 2：两个积分器。

对稳定单位反馈系统，典型结论为：

- Type 0：阶跃通常存在有限稳态误差；
- Type 1：阶跃可零误差，斜坡通常存在有限误差；
- Type 2：阶跃和斜坡可零误差。

这些结论建立在闭环稳定、输入类型明确且模型满足相关假设的前提下。

## 30. 积分器不是越多越好

积分器：

$$
\frac{1}{s}
$$

提高低频增益，但也带来约 $-90^\circ$ 相位特性，并会累积持续误差。

过多积分器可能：

- 降低相位裕度；
- 增加振荡风险；
- 在电压或电流饱和时造成 windup；
- 放大低频漂移和偏置影响。

因此稳态精度必须与动态鲁棒性共同设计。

## 31. Plant Pole 与 Closed-Loop Pole

裸 RL plant 的极点为：

$$
p_{\mathrm{plant}}
=
-\frac{R}{L}
$$

加入控制器后，闭环极点由：

$$
1+C(s)G(s)=0
$$

决定。

控制器的一项核心作用，就是重新塑造闭环极点位置。不能用 plant pole 直接代替 current-loop closed-loop pole，也不能把 plant 自然带宽直接当作控制带宽。

## 32. 系统阶数与状态数量

一阶传递函数的分母最高次为 $s^1$，二阶为 $s^2$。在最小实现中：

$$
\text{system order}
=
\text{independent dynamic states}
$$

但若存在 pole-zero cancellation、不可控或不可观状态，传递函数阶数可能低于原始状态空间维数。

因此系统阶数与状态数量的对应，需要在 minimal realization 的意义下理解。

### 32.1 PI-RL 闭环的两个状态

对第 26 节的净电压 PI 结构，取 $e=i^*-i$、$\dot\xi=e$、$u=K_p e+K_i\xi$，可写为：

$$
\frac{d}{dt}
\begin{bmatrix}i\\\xi\end{bmatrix}
=
\begin{bmatrix}
-(R+K_p)/L&K_i/L\\
-1&0
\end{bmatrix}
\begin{bmatrix}i\\\xi\end{bmatrix}
+
\begin{bmatrix}K_p/L\\1\end{bmatrix}i^*.
$$

精确匹配后，状态矩阵的特征多项式仍有 $-R/L$ 与 $-K_p/L$ 两个根（可重合），而参考到电流的零状态最简传递函数只有一个极点。前者回答内部动态有几个状态，后者回答特定输入输出通道最少需要几个状态描述。

若在 plant 净电压处加入独立扰动 $d$，则：

$$
\frac{I(s)}{D(s)}
=\frac{s}{Ls^2+(R+K_p)s+K_i}.
$$

该通道在理想匹配时通常仍保留两个模态，说明不能把参考通道的一阶约简当成所有输入通道或初始状态响应都变为一阶。模型约简中的最小实现与极零相消可参考 [MathWorks 官方说明](https://www.mathworks.com/help/control/ref/dynamicsystem.minreal.html)。

## 33. 两个快速读式例子

### 33.1 一阶系统

看到：

$$
G(s)=\frac{10}{s+10}
$$

应读出：

- 极点：$-10\ \mathrm{rad/s}$；
- 时间常数：$0.1\ \mathrm{s}$；
- DC 增益：1；
- 2% settling time：约 $0.4\ \mathrm{s}$；
- $-3\ \mathrm{dB}$ 带宽：$10\ \mathrm{rad/s}\approx1.59\ \mathrm{Hz}$。

### 33.2 二阶系统

看到：

$$
G(s)
=
\frac{10000}
{s^2+100s+10000}
$$

与标准分母比较：

$$
\omega_n=100\ \mathrm{rad/s}
$$

$$
\zeta=0.5
$$

极点为：

$$
p=-50\pm j86.6
$$

它是欠阻尼系统，会产生衰减振荡和超调，包络衰减率约为 $50\ \mathrm{s^{-1}}$。

## 34. 常见误区

1. **极点只是分母的根。** 计算上如此，物理上它对应自然动态模态。
2. **零点不影响系统性能。** 零点会塑造暂态、相位和可实现带宽。
3. **越靠左的极点永远主导响应。** 越靠左通常衰减越快，主导极点往往更靠近虚轴。
4. **Pole-zero cancellation 可以完全信赖。** 参数误差和隐藏内部模态会破坏这种理想化。
5. **一阶带宽等于所有系统的极点模。** $\omega_{\mathrm{BW}}=1/\tau$ 只直接适用于标准一阶低通。
6. **二阶带宽总等于 $\omega_n$。** 它还取决于阻尼比。
7. **带宽越高越好。** 延迟、噪声、共振和未建模动态会限制带宽。
8. **稳态误差小就表示控制器设计良好。** 还必须检查动态性能和鲁棒性。
9. **PI 积分器主要用于加速。** 它的核心作用之一是提高低频增益和消除阶跃稳态误差。
10. **Open-loop crossover 就是 closed-loop bandwidth。** 两者定义不同。
11. **Plant pole 就是 closed-loop pole。** 控制器会改变闭环特征方程。
12. **Final Value Theorem 对任何系统都可使用。** 它要求最终值存在并满足极点条件。
13. **高频只改变幅值，不改变相位。** RL 的幅值与相位都随频率变化。
14. **高频电流滞后必然有害或导致失稳。** 应区分开关纹波滤波、参考跟踪及完整环路稳定裕度。
15. **PI 一阶，所以 PI-RL 闭环也必然一阶。** 一般闭环有两个状态，理想相消只约简特定输入输出通道。
16. **Plant 就是整个电网，PI 不属于系统。** Plant 是选定被控过程，PI 属于闭环系统。
17. **$E/R=1-T$ 不需要看反馈结构。** 这里要求单位负反馈及指定的误差定义。
18. **$1/\sqrt2$ 是带宽的频率值。** 它是幅值门槛，角频率需从相应频响方程求出。

## 35. 下一步接口

- 从 Bode 图读取增益交越频率、相位裕度和增益裕度；
- 把采样、计算和 PWM 延迟加入电流环模型；
- 检查不同 current-loop bandwidth 下的鲁棒性；
- 研究 PI 零点放置与参数误差；
- 加入电压限幅和 anti-windup；
- 分析 dq 解耦和电网电压前馈的非理想性；
- 再连接 PLL、外环带宽分离和弱电网稳定性。

## 36. 相关笔记

- [Laplace 变换与传递函数](../mathematics/laplace-transform-and-transfer-functions.md)
- [微分方程与动态系统基础](../mathematics/differential-equations-and-dynamic-systems.md)
- [并网变流器电流动态](../../power-electronics/converter-current-dynamics.md)
- [Clarke 与 Park 坐标变换](../electrical-engineering/clarke-and-park-transformations.md)
- [复功率与 BESS PCS 的 P-Q 能力](../electrical-engineering/complex-power.md)
