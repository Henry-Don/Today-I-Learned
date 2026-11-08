# 数字 PI、零阶保持与控制执行时序

从连续 PI 到数字控制，需要规定积分方法、状态初值及命令生效时刻。连续匹配设计是分析起点，不能直接代替数字闭环结论。

## 1. 从连续积分到离散程序

### 1.1 当前误差更新的 Backward Euler PI

连续 $\dot\xi=e$、$u=K_pe+K_i\xi$。比例项使用当前误差，积分状态 $\xi(t)=\xi(0)+\int_0^t e(\tau)\,d\tau$ 保存此前的累积面积。选取当前误差先更新积分状态，再用更新后的状态计算命令。

$$
\xi[k]=\xi[k-1]+T_se[k],\qquad
u[k]=K_pe[k]+K_i\xi[k].
$$

~~~text
integral = integral + Ts * error
command  = Kp * error + Ki * integral
~~~

令初始历史 $\xi[-1]=0$。在单侧 Z 变换中，延迟一拍相当于把求和指标平移一位，得到 $\mathcal Z\{\xi[k-1]\}=z^{-1}\Xi(z)$。非零的 $\xi[-1]$ 会额外产生边界项。对两条程序更新式分别变换，可得

$$
\Xi(z)-z^{-1}\Xi(z)=T_sE(z),\qquad
\frac{\Xi(z)}{E(z)}=\frac{T_s}{1-z^{-1}},
$$

$$
U(z)=K_pE(z)+K_i\Xi(z).
$$

除以 $E(z)$ 后得到零初始状态下的 PI 传递函数。

$$
C_{\mathrm{PI}}(z)=K_p+\frac{K_iT_s}{1-z^{-1}}.
$$

“当前更新”是指定的计算约定，不表示计算与执行耗时自动为零；执行延迟应另建模。

### 1.2 三种积分方法

每一步要近似的是 $\int_{(k-1)T_s}^{kT_s}e(t)\,dt$。Forward Euler 取左端的旧误差作矩形高度，Backward Euler 取右端的当前误差，Trapezoidal 取两端平均值构成梯形。比如旧误差 2 A、当前误差 4 A，$T_s=1\ \mathrm{ms}$，三种增量依次是 $0.002$、$0.004$、$0.003\ \mathrm{A\cdot s}$。连续误差若在该区间恰为直线，梯形面积就是该区间的精确积分。

| 方法 | 积分状态增量 $\xi[k]-\xi[k-1]$ | 零初值积分传递函数 |
| --- | --- | --- |
| Forward Euler | $T_se[k-1]$ | $T_sz^{-1}/(1-z^{-1})$ |
| Backward Euler | $T_se[k]$ | $T_s/(1-z^{-1})$ |
| Trapezoidal | $T_s(e[k]+e[k-1])/2$ | $T_s(1+z^{-1})/[2(1-z^{-1})]$ |

Tustin 对应 $s\approx(2/T_s)(1-z^{-1})/(1+z^{-1})$，存在频率扭曲。积分器本身含单位圆极点，不能把积分方法的数值性质当作闭环稳定证明。具体块选项见 [MathWorks 离散 PID 文档](https://www.mathworks.com/help/simulink/slref/discretepidcontroller.html)，需核对控制器形式、增益与采样时间。

数字 PI 的积分规则按 $T_s$ 更新误差累积，连续 plant 的求解器按自身步长 $h$ 计算状态。两处可以使用同名积分规则，各自有独立的配置和时序。

### 1.3 限幅与积分饱和

$u_{\mathrm{sat}}=\operatorname{sat}(u_{\mathrm{raw}})$ 不等于原命令时，积分仍累积可造成 windup。反算结构可写为：

$$
\dot\xi=e+K_{aw}(u_{\mathrm{sat}}-u_{\mathrm{raw}}).
$$

本式以积分误差状态 $\xi$ 为定义，$K_{aw}$ 的单位需使校正项与误差相同，不能与其他积分输出状态定义的增益直接互换。离散更新与饱和差值时刻也需规定；外接 Saturation 不自动构成 anti-windup。

例如 PI 计算出 20 V，而执行上限为 5 V。若误差持续为正，普通积分器还会继续累积，解除饱和时可能需要较长时间回退。对上式的反算结构，$u_{\mathrm{sat}}-u_{\mathrm{raw}}=-15\ \mathrm V$。在 $K_{aw}>0$ 时，这一项会减小积分增长率。也可按指定条件暂停积分，例如在误差会把命令继续推向饱和方向时暂缓累积。

## 2. 保持、延迟与 PWM 更新

### 2.1 理想保持器的相位与幅值

一个单位样值的保持对应宽度为 $T_s$ 的矩形。为使直流增益为 1，将其高度归一为 $1/T_s$。对该矩形作傅里叶变换，有

$$
H_{\mathrm{ZOH}}(j\omega)
=\frac1{T_s}\int_0^{T_s}e^{-j\omega t}\,dt
=\frac{1-e^{-j\omega T_s}}{j\omega T_s}.
$$

将分子写成 $e^{-j\omega T_s/2}(e^{j\omega T_s/2}-e^{-j\omega T_s/2})$，利用括号内的差等于 $2j\sin(\omega T_s/2)$，得到

$$
H_{\mathrm{ZOH}}(j\omega)=e^{-j\omega T_s/2}
\frac{\sin(\omega T_s/2)}{\omega T_s/2}.
$$

低频主瓣内有约 $-\omega T_s/2$ 相位，且幅值含 sinc 衰减；不能在全频段将其替换为纯半拍延迟。另一些保持器定义带 $T_s$ 增益因子，比较前需核对归一化。

中点 $T_s/2$ 决定了指数相位因子。sinc 为正的主瓣内，按连续展开的相位，保持脉冲相位就是 $-\omega T_s/2$。把整个频响近似成纯半拍延迟，还需要 $|\omega T_s|\ll1$ 使 sinc 接近 1。跨越 sinc 零点时应处理幅值过零及相位跳变。上述积分描述保持重构脉冲，分析完整反馈还要纳入采样和离散控制器。

取 $T_s=1\ \mathrm{ms}$、$f=100\ \mathrm{Hz}$，有 $\angle H_{\mathrm{ZOH}}=-2\pi fT_s/2=-18^\circ$，幅值约 0.984。这个相位量随频率和采样周期变化。时序建模时应将其归入保持环节。

### 2.2 一拍延迟和连续时延

| 对象 | 表达式 | 含义 |
| --- | --- | --- |
| 一拍执行延迟 | $u_a[k]=u_c[k-1]$，$z^{-1}$ | 指定离散周期延迟 |
| $m$ 拍延迟 | $z^{-m}$ | 整数采样周期延迟 |
| 连续纯时延 | $e^{-sT_d}$ | 指定时长，可非整数拍 |

对纯延迟，幅值为 1、相位为 $-\omega T_d$。理想连续积分环路交越 100 Hz，额外独立纯延迟 0.5 ms 带来 $-18^\circ$，将原 $90^\circ$ PM 降到 $72^\circ$。这是交越不被幅值改变、尚无其他保持相位的特例；真实数字环路应重算交越与裕度。

精确离散 RL 的分子已有 $z^{-1}$，因为当期执行输入首次影响下一采样点电流。保持输入的作用已通过区间积分纳入这个离散 plant。只有时序明确显示命令在下一拍才成为执行电压时，才在命令通道额外乘 $z^{-1}$。参见 [RL 离散传递函数推导](../foundations/control/sampling-zoh-and-discrete-time-systems.md#32-差分方程到传递函数)。

### 2.3 命令何时真正生效

按顺序记录传感器与滤波、ADC 采样/转换、误差计算、积分更新、限幅、计算结束、PWM 寄存器载入及桥臂执行时刻。若需等待下个更新边沿，应在模型中表达。

总延迟若已包含保持或 PWM 等效相位，不应再次机械叠加半拍。一条阶梯保持命令只是平均执行模型，并不是实际桥臂开关电压。

## 3. 模型核对与章节索引

### 3.1 数字闭环应重新验证

先明确积分初值、$T_s$、执行时间、饱和及输入输出边界，再由精确离散 plant 与控制器状态建立闭环。连续 PI-RL 的极零匹配不会自动在离散 PI 下原样成立；更复杂 PWM 和多速率分析留待后续。

### 3.2 相关笔记

- [连续 PI 电流环设计](current-loop-frequency-domain-design.md)
- [精确 ZOH 与离散稳定性](../foundations/control/sampling-zoh-and-discrete-time-systems.md)
- [Simulink 模型层级与十项实验计划](../tools/simulink-model-levels-solvers-and-experiment-plan.md)
- [测试指标与结论边界](../verification-validation/simulation-credibility-and-test-criteria.md)
