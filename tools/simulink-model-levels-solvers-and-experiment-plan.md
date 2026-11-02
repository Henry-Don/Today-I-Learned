# Simulink 模型层级、求解器与 PI-RL 实验计划

本篇整理建模方法和待执行实验，不代表已经创建或运行 MATLAB/Simulink 模型。软件设置需与实际版本、工具箱及模型结构核对。

## 1. 逐层建立模型

### 1.1 三个层级回答不同问题

| 层级 | 结构 | 目的 |
| --- | --- | --- |
| Level 1 | 连续 PI＋连续 RL | 对照解析式、方向、初值 |
| Level 2 | ADC＋离散 PI＋限幅/延迟＋ZOH＋连续 RL | 研究采样、时序及测量链 |
| Level 3 | 离散控制＋PWM＋功率桥＋L/LC/LCL＋电网 | 研究开关、纹波与谐振 |

先建立可解释基准，每次增加一个明确因素。Level 2 的反馈链为连续电流→传感器/抗混叠→ADC→误差→离散 PI/anti-windup→限幅/执行延迟→保持执行→plant。标明命令何时生效、积分初值和网压扰动端口；并非所有任务都需要开关级模型。

### 1.2 模块作用及容易混淆的接口

| 模块 | 作用与限制 |
| --- | --- |
| Integrator / State-Space | 连续状态积分或连续状态方程 |
| Discrete State-Space | 离散递推；须自行提供正确离散矩阵 |
| Discrete PID Controller | 指定 PI/PID 结构、积分方法、sample time 与限幅配置 |
| Zero-Order Hold | 采样保持；不等于一拍延迟 |
| Unit Delay / Transport Delay | 分别表达离散一拍与连续时延 |
| Rate Transition | 多速率数据交接，不是普通 ZOH 的同义词 |
| Saturation / Quantizer | 分别限幅和量化；不自动构成 anti-windup 或完整 ADC |
| Band-Limited White Noise | 核对 PSD 参数、相关时间、种子 |
| Scope / logging | 观察与记录，不提供自动可信度证明 |

[MathWorks ZOH 文档](https://www.mathworks.com/help/simulink/slref/zeroorderhold.html)明确提醒，不要用 ZOH 代替 fast-to-slow 的 Rate Transition。离散控制器细节见 [离散 PID 文档](https://www.mathworks.com/help/simulink/slref/discretepidcontroller.html)。

## 2. 求解器、采样与事件

### 2.1 固定步长与变步长

固定步长按基础时间间隔推进，可用于相应实时/代码生成调度及离线比较；变步长按动态与容限调整步长，常用于离线连续模型。刚性、事件、状态尺度和部署目的决定选择，没有通用最佳求解器。`ode45` 与 `ode15s` 等方法应按模型性质比较，见 [MathWorks 求解器比较](https://www.mathworks.com/help/simulink/ug/compare-solvers.html)。

### 2.2 求解器步长不等于控制周期

$h=10\ \mu\mathrm s$、$T_s=100\ \mu\mathrm s$ 可共存：连续 plant 在相邻控制更新间推进十个固定基础步，控制器只在指定 sample hit 更新。数值内部步骤不代表额外 ADC 采样；多速率传输、主步/次步与事件调度要按具体模型核对。

### 2.3 不连续事件与容限

开关、复位、限幅可能被过大步长跨越。Zero-Crossing Detection 可帮助定位适当事件，但仍需核对设置及事件时间。收紧相对/绝对容限不保证所有切换都正确处理；最大步长与信号记录间隔也会影响可见峰值。

## 3. 连续解析基准与十项实验

### 3.1 100 Hz 理想 PI-RL 基准

沿用已有连续设计，$L=0.002\ \mathrm H$、$R=0.1\ \Omega$、$\omega_0=2\pi100\ \mathrm{rad/s}$：

$$
K_p=L\omega_0\approx1.256637\ \Omega,\qquad
K_i=R\omega_0\approx62.831853\ \mathrm{\Omega/s}.
$$

理想单位负反馈、无额外补偿/延迟/限幅时，匹配参考通道 $T(s)=\omega_0/(s+\omega_0)$。归一化阶跃 $y=1-e^{-\omega_0t}$ 给出：

$$
\tau=1/\omega_0\approx1.59155\ \mathrm{ms},\quad
t_r=\ln9/\omega_0\approx3.4970\ \mathrm{ms},\quad
t_s=\ln50/\omega_0\approx6.2262\ \mathrm{ms}.
$$

该解析通道无超调、稳态误差为零；输入阶跃幅度可按线性比例缩放。这些是数学参考值，数字实现须重算，亦非 PCS 验收标准。完整连续推导见 [已有 PI 电流环笔记](../power-electronics/current-loop-frequency-domain-design.md)。

### 3.2 待执行测试矩阵

下表全部为计划，不包含仿真测量结果。每项固定其余条件、记录唯一或明确的一组变化及基准。

| ID | 变化 | 主要观察 |
| --- | --- | --- |
| T01 | 连续 PI＋RL | 与解析阶跃比较 |
| T02 | 比较步长或容限 | 指标是否数值收敛 |
| T03 | 离散 PI，$T_s=100\ \mu\mathrm s$ | 积分方法与数字化误差 |
| T04 | 独立一拍执行延迟 | 命令生效时序 |
| T05 | 有物理依据地增加延迟 | 离散闭环极点、裕度与振铃 |
| T06 | 测量端定义明确的噪声 | 误差 RMS、控制命令波动 |
| T07 | ADC 量化 | 小信号、量化相关性 |
| T08 | 电压饱和与 anti-windup | 积分状态、恢复、超调 |
| T09 | 扫描 $L,R$ | 参数敏感性 |
| T10 | 改变 $T_s$ | 采样、保持、延迟与闭环性能 |

### 3.3 执行前的配置与 LCL 扩展

记录软件版本/工具箱、模型版本、solver、$h,T_s,T_{sw},T_d$、容限、初值、仿真区间、日志通道及采样时刻。先固定连续 plant＋采样数字控制，再逐步引入噪声与 PWM。

扩展 LCL 后同时记录 $i_1,i_2,v_f,i_C$、阻尼、谐振频率、网阻抗和阻尼控制变化，比较平均与开关模型。一个网侧输出平滑不能排除内部隐藏或弱阻尼模态。

## 4. 边界与章节索引

### 4.1 当前状态与后续研究

本篇只定义模型结构、解析参考和实验计划，未进行 Simulink 运行、实测或 HIL。一般多速率、事件误差界和实时求解器理论属于后续任务；具体配置需实际安装环境确认。

### 4.2 相关笔记

- [数值方法与刚性](../foundations/mathematics/numerical-integration-and-solver-stability.md)
- [采样与精确离散 plant](../foundations/control/sampling-zoh-and-discrete-time-systems.md)
- [数字 PI 与时序](../power-electronics/digital-pi-zoh-and-control-timing.md)
- [测量噪声](../foundations/electrical-engineering/measurement-noise-quantization-and-jitter.md)
- [验证指标与测试判据](../verification-validation/simulation-credibility-and-test-criteria.md)
