# Today I Learned

面向电气工程、储能、电力电子、半导体与相关基础学科的长期学习笔记。

仓库按知识领域组织，而不是按学习日期堆放所有内容：可复用的知识进入对应主题目录；`weekly/` 记录每周真正建立的新理解、纠正的误区和后续问题。公式默认采用余弦参考；除非另有说明，交流相量采用 RMS 值。

## Knowledge Map

### Foundations

- [Mathematics](foundations/mathematics/README.md)
  - [三角、复数与相量](foundations/mathematics/trigonometry-complex-phasors.md)
- [Signals and Systems](foundations/signals-and-systems/README.md)
- [Control](foundations/control/README.md)

### Engineering Domains

- [Power Systems](power-systems/README.md)
  - [复功率与 BESS PCS 的 P-Q 能力](power-systems/concepts/complex-power.md)
- [Energy Storage](energy-storage/README.md)
- [Power Electronics](power-electronics/README.md)
- [Semiconductors](semiconductors/README.md)

### Practice and Learning Log

- [Experiments](experiments/README.md)
- [Weekly TIL](weekly/README.md)
  - [2026-W37：三角、复数、相量与复功率](weekly/2026/2026-week-37.md)

## Repository Structure

```text
Today-I-Learned/
├── README.md
├── foundations/
│   ├── mathematics/
│   ├── signals-and-systems/
│   └── control/
├── power-systems/
│   ├── concepts/
│   ├── papers/
│   └── standards/
├── energy-storage/
│   ├── batteries/
│   ├── bess/
│   ├── pcs/
│   └── standards/
├── power-electronics/
│   ├── converters/
│   ├── modulation/
│   └── control/
├── semiconductors/
│   ├── fundamentals/
│   ├── devices/
│   ├── sic/
│   └── gan/
├── experiments/
└── weekly/
    └── 2026/
```

如果实验或工具逐渐形成独立的软件、仿真模型、依赖和测试体系，再将其拆分成单独仓库；本仓库保留知识总结及外部项目链接。

## Writing Conventions

- 虚数单位写作 $j$，避免与电流符号 $i$ 混淆。
- 瞬时量用小写，如 $v(t)$、$i(t)$；相量用带下划线的大写，如 $\underline V$、$\underline I$。
- 相量默认使用 RMS；若使用峰值会明确标注。
- 复功率采用被动符号约定：$\underline S=\underline V\underline I^*=P+jQ$。
- 相角的主值区间只是表示约定；频率响应分析中可使用展开相位（unwrapped phase）。
- 理论笔记尽量包含定义、推导、工程意义、适用条件、常见误区和相关主题。

