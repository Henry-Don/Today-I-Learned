# Control Foundations

这一层连接动态模型、频域分析与变流器控制实现。目标是能够从 plant 方程出发，解释闭环性能、选择控制器结构，并识别带宽、稳定裕度、饱和和模型不确定性之间的工程折中。

## Core Topics

- 极点、零点、时间常数与主导模态
- 一阶和二阶标准系统
- Bode、Nyquist、增益裕度与相位裕度
- 闭环传递函数、灵敏度函数与扰动抑制
- 稳态误差与 system type
- PI/PID、前馈、解耦与 anti-windup
- 采样、离散化、计算延迟与 PWM 延迟
- 状态空间、可控性、可观性与状态反馈
- 鲁棒性、参数不确定性与模型边界

## Notes

- [极点、零点、带宽与稳态误差](poles-zeros-bandwidth-and-steady-state-error.md)

数学入口见 [Laplace 变换与传递函数](../mathematics/laplace-transform-and-transfer-functions.md)。变流器 plant 和 dq 电流控制接口见 [并网变流器电流动态](../../power-electronics/converter-current-dynamics.md)。
