# 数字 PI、零阶保持与控制执行时序

从连续 PI 到数字控制，需要规定积分方法、状态初值及命令生效时刻。连续匹配设计是分析起点，不能直接代替数字闭环结论。

## 1. 从连续积分到离散程序

### 1.1 当前误差更新的 Backward Euler PI

连续 $\dot\xi=e$、$u=K_pe+K_i\xi$。选取当前误差先更新积分状态再计算命令：

$$
\xi[k]=\xi[k-1]+T_se[k],\qquad
u[k]=K_pe[k]+K_i\xi[k].
$$

零初始状态下 $(1-z^{-1})\Xi=T_sE$，所以：

$$
C_{\mathrm{PI}}(z)=K_p+\frac{K_iT_s}{1-z^{-1}}.
$$

“当前更新”是指定的计算约定，不表示计算与执行耗时自动为零；执行延迟应另建模。

### 1.2 三种积分方法

| 方法 | 积分状态增量 $\xi[k]-\xi[k-1]$ | 零初值积分传递函数 |
| --- | --- | --- |
| Forward Euler | $T_se[k-1]$ | $T_sz^{-1}/(1-z^{-1})$ |
| Backward Euler | $T_se[k]$ | $T_s/(1-z^{-1})$ |
| Trapezoidal | $T_s(e[k]+e[k-1])/2$ | $T_s(1+z^{-1})/[2(1-z^{-1})]$ |

Tustin 对应 $s\approx(2/T_s)(1-z^{-1})/(1+z^{-1})$，存在频率扭曲。积分器本身含单位圆极点，不能把积分方法的数值性质当作闭环稳定证明。具体块选项见 [MathWorks 离散 PID 文档](https://www.mathworks.com/help/simulink/slref/discretepidcontroller.html)，需核对控制器形式、增益与采样时间。

### 1.3 限幅与积分饱和

$u_{\mathrm{sat}}=\operatorname{sat}(u_{\mathrm{raw}})$ 不等于原命令时，积分仍累积可造成 windup。反算结构可写为：

$$
\dot\xi=e+K_{aw}(u_{\mathrm{sat}}-u_{\mathrm{raw}}).
$$

本式以积分误差状态 $\xi$ 为定义，$K_{aw}$ 的单位需使校正项与误差相同，不能与其他积分输出状态定义的增益直接互换。离散更新与饱和差值时刻也需规定；外接 Saturation 不自动构成 anti-windup。

## 2. 保持、延迟与 PWM 更新

### 2.1 理想保持器的相位与幅值

低频增益归一为 1 的保持等效频响为：

$$
H_{\mathrm{ZOH}}(j\omega)=e^{-j\omega T_s/2}
\frac{\sin(\omega T_s/2)}{\omega T_s/2}.
$$

低频主瓣内有约 $-\omega T_s/2$ 相位，且幅值含 sinc 衰减；不能在全频段将其替换为纯半拍延迟。另一些保持器定义带 $T_s$ 增益因子，比较前需核对归一化。

### 2.2 一拍延迟和连续时延

| 对象 | 表达式 | 含义 |
| --- | --- | --- |
| 一拍执行延迟 | $u_a[k]=u_c[k-1]$，$z^{-1}$ | 指定离散周期延迟 |
| $m$ 拍延迟 | $z^{-m}$ | 整数采样周期延迟 |
| 连续纯时延 | $e^{-sT_d}$ | 指定时长，可非整数拍 |

对纯延迟，幅值为 1、相位为 $-\omega T_d$。理想连续积分环路交越 100 Hz，额外独立纯延迟 0.5 ms 带来 $-18^\circ$，将原 $90^\circ$ PM 降到 $72^\circ$。这是交越不被幅值改变、尚无其他保持相位的特例；真实数字环路应重算交越与裕度。

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
