# PLL 与 SRF-PLL

## 1. PLL 的同步目标

PLL 是 Phase-Locked Loop，即锁相环。它通过反馈跟踪输入电压的相角 $\hat\theta$，并通常给出频率估计 $\hat\omega$。GFL 变流器常使用它与 PCC 电压同步：

$$
\hat\theta\approx\theta_g,
\qquad
\hat\omega\approx\omega_g
$$

可靠角度随后用于 dq 测量、控制和逆 Park 调制。准确的 $v_d,v_q,i_d,i_q$ 是同步的重要结果，PLL 的任务本身是建立和跟踪参考相位。

本文沿用 $P(\hat\theta)=R(-\hat\theta)$、ABC 正相序和 amplitude-invariant Clarke。这里 $V$ 为基波空间矢量幅值，对应相电压峰值尺度。

## 2. SRF-PLL 的基本闭环

SRF 是 Synchronous Reference Frame。SRF-PLL 用自身估计的角度旋转电压坐标，再从 q 轴电压得到误差信号。

~~~text
PCC vabc → Clarke → vαβ → Park(θ̂) → vq → normalization → PI → Δω
                              ↑                                ↓
                              └──── θ̂ ← integrate ← ω̂ = ω0 + Δω
~~~

$\omega_0$ 是名义角频率；PI 产生频率修正量 $\Delta\omega$，频率积分得到相角：

$$
\hat\omega=\omega_0+\Delta\omega,
\qquad
\dot{\hat\theta}=\hat\omega
$$

这个相角再次进入 Park，因此整个结构是反馈环，不是一次坐标变换。[imperix 的 SRF-PLL 技术说明](https://imperix.com/doc/implementation/synchronous-reference-frame-pll)给出了这一实现链以及归一化方法。

## 3. 为什么 vq 可以表示相位误差

设平衡正序电压空间矢量为 $Ve^{j\theta_g}$，使用 PLL 角度 $\hat\theta$ 后：

$$
v_d=V\cos(\theta_g-\hat\theta),
\qquad
v_q=V\sin(\theta_g-\hat\theta)
$$

令：

$$
\delta=\theta_g-\hat\theta
$$

则小角度下：

$$
v_q\approx V\delta
$$

在本文 q 轴约定下，$\delta>0$ 表示估计角滞后，正的 $v_q$ 应推动 $\hat\omega$ 增大，缩小角度差。反转 q 轴时，反馈符号也必须调整。

在平衡正序、正常幅值和预期正向锁定附近，$v_q\to0$ 对应角度误差趋近于零。但 $v_q=0$ 也可能发生在 $\delta=\pi$，因此正常电压定向还需 $v_d>0$。极低电压时，仅靠接近零的 $v_q$ 不能证明角度估计有效。

## 4. PI、幅值归一化与小信号关系

若采用正的幅值估计 $\hat V$ 归一化：

$$
e_\theta=\frac{v_q}{\hat V}\approx\delta
$$

则 PI 可写成：

$$
\Delta\omega=K_{p,\mathrm{PLL}}e_\theta
+K_{i,\mathrm{PLL}}\xi,
\qquad
\dot\xi=e_\theta
$$

在幅值估计准确、平衡正序和小角度条件下，环路增益为：

$$
L_{\mathrm{PLL}}(s)
=\frac{K_{p,\mathrm{PLL}}+K_{i,\mathrm{PLL}}/s}{s}
$$

因此角度跟踪传递函数为：

$$
\frac{\Delta\hat\theta(s)}{\Delta\theta_g(s)}
=
\frac{K_{p,\mathrm{PLL}}s+K_{i,\mathrm{PLL}}}
{s^2+K_{p,\mathrm{PLL}}s+K_{i,\mathrm{PLL}}}
$$

PI 与频率到角度的积分一起构成二阶同步动态。若直接用未归一化 $v_q$，相位检测增益还包含 $V$，不能直接照搬同一组 PI 参数。低电压时应对归一化分母及频率修正作合理限制，避免幅值估计接近零时放大误差。

## 5. PLL 输出的用途与边界

| 输出或作用 | 控制中的用途 |
|---|---|
| $\hat\theta$ | dq 变换、逆 Park 与调制参考 |
| $\hat\omega$ | 同步、控制模型中的角速度以及频率监测 |
| 电压定向 | 在 $v_q\approx0$ 时简化 P/Q 与电流分量的关系 |

角度误差会混合 d/q 分量，影响电压前馈、P/Q 定向和电流控制。坐标变换本身仍然可逆，错误在于它不再按预期与目标电压对齐。

PLL 估计的是输入电压的选定相位，通常是基波或正序基波相位。在不平衡、谐波和故障下，必须说明跟踪对象；“电网角度”不意味着所有地点、所有频率成分都拥有一个完全相同的相角。

GFM 的控制角通常由内部功率同步或振荡器动态生成。部分实现仍会使用 PLL 做测量或并网前同步，但不能据此把 PLL 当成所有 dq 控制的必需角度来源。

## 6. 不平衡电压对单个 SRF-PLL 的影响

正序以 $+\omega$ 旋转，负序以 $-\omega$ 旋转。在正序同步 dq 中，负序留下 $-2\omega$ 的相对旋转，导致 $v_d,v_q$ 出现二倍频项。

传统 SRF-PLL 若直接使用这类 $v_q$，频率和相角估计可能含纹波。滤波、正序提取或双同步坐标解耦能够改善这一问题，但也会增加延迟与模型动态。应按研究问题选择方案，而不是假设不平衡电压还能天然得到恒定 dq。负序二倍频及 DDSRF 解耦见 [imperix 技术说明](https://imperix.com/doc/implementation/synchronous-reference-frame-pll)。

零序由完整 Clarke 的第三维保留，不能只依赖二维 $\alpha\beta$ 恢复；正负序处理与零序回路分析是相关但不同的问题。

## 7. PCC 电压为何把 PLL 带进稳定性问题

PCC 是公共耦合点。PLL 常使用这里的测量电压，而该电压可能受到变流器注入电流和电网阻抗共同影响。沿 PCC 向远端电源的电流方向，简单 Thevenin 等值为：

$$
\underline V_{\mathrm{PCC}}
=\underline E_g+Z_g\underline I
$$

因此形成：

$$
i
\longrightarrow
v_{\mathrm{PCC}}
\longrightarrow
\mathrm{PLL}
\longrightarrow
\hat\theta
\longrightarrow
\text{dq control}
\longrightarrow
v_c
\longrightarrow
i
$$

电网阻抗小的刚性电压近似下，这种反馈较弱；电网阻抗增大后，角度估计和电流控制可能明显相互影响。PLL 带宽需要与电流环、外环、滤波和网络动态一起检查，不能只以相角跟踪快慢评价整体稳定性。

## 8. 常见误区

1. **PLL 只是为计算两个 dq 数字。** 它建立同步参考，dq 定向是输出用途之一。
2. **只要 vq 为零就一定正确锁相。** 还要检查电压幅值、正向对齐和所跟踪的分量。
3. **角度估计错误会破坏 Park 的数学可逆性。** 变换仍可逆，但失去预期的电压定向。
4. **PLL 的 PI 参数就是电流 PI 参数。** 两者的被控对象、单位和动态都不同。
5. **不平衡正弦在单个 dq 中都变成常量。** 负序在正序 dq 中留下二倍频。
6. **PLL 只是测量模块，不影响稳定性。** 它可以经由 PCC 电压和电流形成动态闭环。

## 9. 下一步接口

- 比较未归一化与归一化相位检测器的幅值依赖。
- 分析正序提取滤波对 PLL 延迟和带宽的影响。
- 从 SRF-PLL 扩展到 DDSRF-PLL 及不平衡测试。
- 在包含电网阻抗的模型中检查 PLL 与电流环交互。

## 10. 相关笔记

- [Clarke 与 Park 坐标变换](../foundations/electrical-engineering/clarke-and-park-transformations.md)
- [对称分量、零序与接地](../power-systems/symmetrical-components-zero-sequence-and-grounding.md)
- [并网变流器电流动态](../power-electronics/converter-current-dynamics.md)
- [极点、零点、带宽与稳态误差](../foundations/control/poles-zeros-bandwidth-and-steady-state-error.md)
