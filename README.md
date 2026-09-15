# Today I Learned

面向 BESS、IBR、电力系统、变流器控制与模型验证的长期工程学习笔记。

本仓库以 BESS 和新能源并网为主要应用场景，围绕三条相互连接的能力主线组织内容：

- Power Systems
- Power Electronics and Converter Controls
- Engineering Automation and Model Validation

电池基础、EMS/PPC/SCADA、澳洲并网规则和工程软件分别作为系统知识、应用扩展、本地规则层和实现工具。仓库不按热门行业平均分配篇幅，而按这条技术主线积累可复用知识。

## Learning Map

| 学习层 | 目录 | 主要内容 |
| --- | --- | --- |
| 1 | [Foundations](foundations/README.md) | 数学、电路、信号与系统、控制基础 |
| 2 | [Power Systems](power-systems/README.md) | 潮流、短路、稳定性、保护、系统强度与网络阻抗 |
| 3 | [Power Electronics](power-electronics/README.md) | 功率器件约束、变流器、滤波器、调制与控制 |
| 4 | [BESS](bess/README.md) | Battery/BMS 基础、PCS、PPC、EMS、SCADA 与电站系统 |
| 5 | [IBR and Grid Integration](ibr-grid-integration/README.md) | GFL/GFM、PLL、弱电网、限流、频率扫描与控制交互 |
| 6 | [Australia Grid Connection](australia-grid-connection/README.md) | NEM、NER、GPS、模型提交与验收、测试与合规 |
| 7 | [Verification and Validation](verification-validation/README.md) | V 模型、需求追踪、回归测试、RMS/EMT 对比、HIL 与故障诊断 |

支撑内容：

- [Tools](tools/README.md)：MATLAB、Python、PSCAD、PSS®E、PowerFactory 与 HIL 工作流
- [Papers](papers/README.md)：论文与行业资料的结构化阅读记录
- [Experiments](experiments/README.md)：用于验证单一概念的小型可复现实验
- [Weekly TIL](weekly/README.md)：每周新理解、误区与下一步问题

## Current Notes

- [三角函数与复数](foundations/mathematics/trigonometry-and-complex-numbers.md)
- [矩阵与坐标变换](foundations/mathematics/matrices-and-coordinate-transformations.md)
- [正弦稳态与相量](foundations/electrical-engineering/sinusoidal-steady-state-and-phasors.md)
- [复功率与 BESS PCS 的 P-Q 能力](foundations/electrical-engineering/complex-power.md)
- [Clarke 与 Park 坐标变换](foundations/electrical-engineering/clarke-and-park-transformations.md)
- [2026-W37：三角、复数、相量与复功率](weekly/2026/2026-week-37.md)
- [2026-W38：矩阵与 Clarke/Park 坐标变换](weekly/2026/2026-week-38.md)

## Repository Structure

```text
Today-I-Learned/
├── foundations/
│   ├── mathematics/
│   └── electrical-engineering/
├── power-systems/
├── power-electronics/
├── bess/
├── ibr-grid-integration/
├── australia-grid-connection/
├── verification-validation/
├── tools/
├── papers/
├── experiments/
└── weekly/
    └── 2026/
```

只有在确有内容时才增加下一层目录，避免用大量空目录代替知识。完整的软件、仿真模型、依赖、测试和发布版本放在独立工程仓库；这里保存原理、推导、实验结论以及到工程项目的索引。

## Writing Conventions

- 虚数单位写作 $j$，避免与电流符号 $i$ 混淆。
- 瞬时量使用小写，例如 $v(t)$ 和 $i(t)$；相量使用带下划线的大写，例如 $\underline{V}$ 和 $\underline{I}$。
- 除非另有说明，相量采用 RMS 值，正弦稳态采用余弦参考。
- 复功率采用被动符号约定：$\underline{S}=\underline{V}\underline{I}^{*}=P+jQ$。
- 理论笔记至少说明定义、推导、工程意义、适用条件、符号约定、常见误区和相关主题。
- 公式使用 GitHub 支持的数学语法；避免不受支持的宏，并让行内公式与中文标点之间保留空格。
