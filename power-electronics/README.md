# Power Electronics

这一层关注从功率器件约束到并网变流器控制的完整链路。器件物理学习以能解释选型、损耗、驱动、保护和热约束为目标，不延伸为半导体工艺主线。

## Converter Hardware

- Buck、Boost 与双向 DC/DC
- 两电平与三电平变流器
- IGBT、MOSFET 和 SiC 器件的基本约束
- 导通损耗、开关损耗与热设计
- 采样、驱动、隔离和硬件保护
- DC-link 与 L/LCL 滤波器
- EMC 基础

## Modulation and Control

- PWM 与 SVPWM
- [abc、αβ 与 dq 坐标变换](../foundations/electrical-engineering/clarke-and-park-transformations.md)
- [并网变流器电流动态](converter-current-dynamics.md)
- P/Q 外环和 DC-link 外环
- 电压控制与有源阻尼
- 采样、数字时延和 PWM 时延
- 限幅、饱和与 anti-windup

并网控制特性、GFL/GFM 以及弱电网交互继续放在 [IBR and Grid Integration](../ibr-grid-integration/README.md)。
