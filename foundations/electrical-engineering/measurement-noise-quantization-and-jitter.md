# 测量噪声、ADC 量化与采样抖动

测量链会改变控制器看到的信号。噪声、量化和时刻误差需要按注入位置、单位与统计假设分别建模。

## 1. 测量模型与频谱

### 1.1 测量噪声与物理扰动

简化电流测量为 $i_m[k]=Q(i(kT_s)+n(kT_s))$，反馈误差为 $e[k]=i^*[k]-i_m[k]$。噪声通过控制器影响命令，电网电压扰动则直接进入 plant；不能把两者放在同一注入位置。真实传感器还可有增益、偏置、带宽与采样前滤波。

### 1.2 Gaussian、White、RMS 与 PSD

| 对象 | 表示什么 | 必须声明的边界 |
| --- | --- | --- |
| Gaussian | 幅值概率分布 | 不保证频谱平坦 |
| White | 理想平坦功率谱密度 | 连续无限带宽白噪声不是有限功率真实信号 |
| RMS | $\sqrt{\sum x[k]^2/N}$ | 需要统计窗口，非零均值时不等于标准差 |
| PSD | 每单位频带的功率分布 | 单边/双边、Hz/rad/s 与信号单位 |

总体矩有 $x_{\mathrm{RMS}}^2=\mu_x^2+\sigma_x^2$；有限样本对应相同分母 $N$ 的均方恒等式，不能随意换成无偏方差分母 $N-1$。

### 1.3 噪声块参数与抗混叠

[MathWorks Band-Limited White Noise](https://www.mathworks.com/help/simulink/slref/bandlimitedwhitenoise.html)的 `Noise power` 表示 PSD 高度，输出协方差按该功率除以相关时间（块 Sample time）缩放。不能想要 RMS=0.1 就直接填 0.01；还须核对相关时间和单位。该块用有限相关时间随机序列近似白噪声。

真实 ADC 前应考虑抗混叠滤波；改变其截止频率既改变噪声带宽，也可能引入反馈相位滞后。采样后数字滤波不能一般地消除已经折回基带的混叠。

## 2. 量化和时间误差

### 2.1 舍入量化的有界误差

对步距 $q>0$ 的最近等级舍入：

$$
y_q=q\operatorname{round}(y/q),\qquad e_q=y_q-y,\qquad |e_q|\le q/2.
$$

上界假设没有饱和且采用该舍入规则。误差近似均匀分布于 $[-q/2,q/2]$、相关性较弱时，由均匀分布二阶矩得到 $\sigma_q^2\approx q^2/12$。小幅周期输入可产生高度相关的量化误差，不能默认独立白噪声；Quantizer 也不等于完整 ADC。

### 2.2 采样抖动的一阶误差

实际时刻 $t_k=kT_s+\epsilon_k$，光滑信号的一阶 Taylor 展开为：

$$
x(t_k)\approx x(kT_s)+\dot x(kT_s)\epsilon_k,
\qquad e_{\mathrm{jitter}}[k]\approx\dot x(kT_s)\epsilon_k.
$$

相同时间误差下，斜率较大时幅值误差可能更大。该近似要求时间扰动足够小、信号可微；jitter 是时序误差，不是独立于信号的普通幅值噪声。

## 3. 可复现实验与章节索引

### 3.1 记录条件与研究边界

记录注入位置、单位、分布、均值、RMS/PSD 约定、滤波、采样/相关时间、量化范围、随机种子、窗口与重复次数。固定种子便于复现，但单次有利实现不能证明一般性能。一般随机谱理论、Kalman 滤波及不确定性置信界未在本节完成。

### 3.2 相关笔记

- [采样与混叠](../control/sampling-zoh-and-discrete-time-systems.md#12-不同连续频率产生同一序列)
- [数字 PI 的反馈时序](../../power-electronics/digital-pi-zoh-and-control-timing.md)
- [均值、RMS 与测试窗口](../../verification-validation/simulation-credibility-and-test-criteria.md#23-稳态偏差与波动)
