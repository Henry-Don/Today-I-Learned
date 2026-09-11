# Battery Energy Storage Systems

BESS 是本仓库的主要应用场景。这一层关注从电池约束到电站并网接口的系统结构，而不是单独深挖电芯材料或纯 BMS 算法。

## Battery and BMS Foundation

- cell、module、rack 与 container
- SoC、SoH、SoE 和 C-rate
- 一阶与二阶等效电路模型
- 容量衰减与内阻增长
- 充放电、电压、温度和功率约束
- thermal runaway 基本机理
- BMS 分级架构、保护及对 PCS/EMS 的约束

## Plant Architecture and Controls

- PCS、PPC、EMS 与 SCADA 的职责边界
- 辅助电源、HVAC、消防、变压器和集电线路
- AC-coupled 与 DC-coupled
- round-trip efficiency、augmentation 与 availability
- FAT、SAT、commissioning、warranty 与性能保证
- 站级 P/Q 分配、SoC 策略和调度接口

PCS 内部变流器原理参见 [Power Electronics](../power-electronics/README.md)，并网行为参见 [IBR and Grid Integration](../ibr-grid-integration/README.md)。
