# Clarke 与 Park 坐标变换

## 1. 目标与符号约定

本笔记解释以下坐标链：

$$
\begin{bmatrix}
v_a\\
v_b\\
v_c
\end{bmatrix}
\xrightarrow{\mathrm{Clarke}}
\begin{bmatrix}
v_\alpha\\
v_\beta
\end{bmatrix}
\xrightarrow{\mathrm{Park}}
\begin{bmatrix}
v_d\\
v_q
\end{bmatrix}
$$

全文采用以下一套常见约定：

- 正序三相按 $a\rightarrow b\rightarrow c$ 排列；
- 三相正弦采用余弦参考；
- Clarke 采用 amplitude-invariant 的 $2/3$ 缩放；
- $\alpha$ 轴与 a 相轴对齐，$\beta$ 轴按正旋转方向定义；
- Park 采用 $P(\theta)=R(-\theta)$，d 轴与参考角 $\theta$ 对齐。

不同教材、控制器和仿真软件可能采用不同相序、q 轴方向、角度正方向和归一化。形式不同不一定代表推导错误，但同一模型内必须始终一致。

## 2. 平衡三相为什么只有两个自由度

定义：

$$
\mathbf{v}_{abc}=
\begin{bmatrix}
v_a\\
v_b\\
v_c
\end{bmatrix}
$$

理想平衡三相满足：

$$
v_a+v_b+v_c=0
$$

因此：

$$
v_c=-v_a-v_b
$$

知道其中两个量后，第三个量已经确定。虽然 $abc$ 使用三个坐标表示，所有合法点却只位于三维空间中的一个二维平面内，因此只有两个独立自由度。

Clarke 变换的作用，是为这个二维平面选择更适合分析和控制的坐标。

## 3. Clarke 变换

本笔记采用的二维 Clarke 变换为：

$$
\begin{bmatrix}
v_\alpha\\
v_\beta
\end{bmatrix}
=
\frac{2}{3}
\begin{bmatrix}
1&-\frac{1}{2}&-\frac{1}{2}\\
0&\frac{\sqrt{3}}{2}&-\frac{\sqrt{3}}{2}
\end{bmatrix}
\begin{bmatrix}
v_a\\
v_b\\
v_c
\end{bmatrix}
$$

即：

$$
v_\alpha=
\frac{2}{3}
\left(
v_a-\frac{1}{2}v_b-\frac{1}{2}v_c
\right)
$$

$$
v_\beta=
\frac{2}{3}
\left(
\frac{\sqrt{3}}{2}v_b
-\frac{\sqrt{3}}{2}v_c
\right)
$$

矩阵第一行定义 $\alpha$ 坐标，第二行定义 $\beta$ 坐标。矩阵尺寸为 $2\times3$，表示三个相坐标被映射为两个平面坐标。

## 4. Clarke 系数的几何来源

三相轴在平面中相隔 $120^\circ$，可以取：

$$
\theta_a=0^\circ,
\qquad
\theta_b=120^\circ,
\qquad
\theta_c=240^\circ
$$

这些轴在 $\alpha\beta$ 坐标上的投影包含：

$$
\cos120^\circ=-\frac{1}{2},
\qquad
\sin120^\circ=\frac{\sqrt{3}}{2}
$$

$$
\cos240^\circ=-\frac{1}{2},
\qquad
\sin240^\circ=-\frac{\sqrt{3}}{2}
$$

所以 $-1/2$ 和 $\pm\sqrt{3}/2$ 来自三相轴的几何投影，不是人为拼出的常数。前面的 $2/3$ 则来自所选归一化目标。

## 5. 平衡三相经过 Clarke 后的结果

设平衡正序三相为：

$$
v_a=V\cos\theta
$$

$$
v_b=V\cos\left(\theta-\frac{2\pi}{3}\right)
$$

$$
v_c=V\cos\left(\theta+\frac{2\pi}{3}\right)
$$

代入上述 Clarke 变换可得：

$$
v_\alpha=V\cos\theta,
\qquad
v_\beta=V\sin\theta
$$

因此：

$$
\mathbf{v}_{\alpha\beta}
=V
\begin{bmatrix}
\cos\theta\\
\sin\theta
\end{bmatrix}
$$

Clarke 变换把平衡三相正弦重新表示为二维静止坐标系中的旋转向量。它没有在物理上把三相电压变成两相电压。

## 6. Park 变换

Clarke 变换后的向量仍在静止 $\alpha\beta$ 坐标系中旋转。若让新坐标系以相同角度和速度旋转，该向量在新坐标系中就会变成常量。

本笔记采用：

$$
\begin{bmatrix}
v_d\\
v_q
\end{bmatrix}
=
\begin{bmatrix}
\cos\theta&\sin\theta\\
-\sin\theta&\cos\theta
\end{bmatrix}
\begin{bmatrix}
v_\alpha\\
v_\beta
\end{bmatrix}
$$

这个矩阵等于 $R(-\theta)$。它可以理解为：物理向量不动，把坐标轴旋转到参考角 $\theta$，因此属于被动坐标变换。

## 7. 为什么稳态交流在 dq 中变成常量

将：

$$
v_\alpha=V\cos\theta,
\qquad
v_\beta=V\sin\theta
$$

代入 Park 变换：

$$
v_d
=v_\alpha\cos\theta+v_\beta\sin\theta
=V\cos^2\theta+V\sin^2\theta
=V
$$

$$
v_q
=-v_\alpha\sin\theta+v_\beta\cos\theta
=0
$$

最终：

$$
\begin{bmatrix}
v_d\\
v_q
\end{bmatrix}
=
\begin{bmatrix}
V\\
0
\end{bmatrix}
$$

常说的“Park 把 AC 变成 DC”只表示稳态交流量在同步旋转坐标中成为常量。三相导线中的瞬时电压和电流仍然是交流。

## 8. 参考角误差与 q 轴分量

设真实电压空间向量角度为 $\theta_v$，Park 坐标系使用的参考角为 $\theta_r$。则：

$$
\mathbf{v}_{\alpha\beta}
=V
\begin{bmatrix}
\cos\theta_v\\
\sin\theta_v
\end{bmatrix}
$$

经过 $R(-\theta_r)$ 后：

$$
v_d=V\cos(\theta_v-\theta_r)
$$

$$
v_q=V\sin(\theta_v-\theta_r)
$$

令角度误差为：

$$
\delta=\theta_v-\theta_r
$$

则：

$$
v_d=V\cos\delta,
\qquad
v_q=V\sin\delta
$$

当参考角与电压向量对齐时，$\delta=0$，所以 $v_q=0$。SRF-PLL 正是利用这一关系调节参考角，使旋转坐标系逐渐与电网电压向量对齐。若系统采用相反的 q 轴约定，误差信号符号也会相反。

## 9. Park 逆变换

由于 Park 矩阵是纯旋转矩阵：

$$
P^{-1}(\theta)=R(\theta)=P^T(\theta)
$$

因此：

$$
\begin{bmatrix}
v_\alpha\\
v_\beta
\end{bmatrix}
=
\begin{bmatrix}
\cos\theta&-\sin\theta\\
\sin\theta&\cos\theta
\end{bmatrix}
\begin{bmatrix}
v_d\\
v_q
\end{bmatrix}
$$

若 $v_d=V$ 且 $v_q=0$，逆变换就恢复 $v_\alpha=V\cos\theta$ 和 $v_\beta=V\sin\theta$。

## 10. 二维 Clarke 为什么能恢复平衡 abc

二维 Clarke 矩阵为 $2\times3$，不是方阵，因此没有普通意义上的双侧逆矩阵。但是平衡三相受约束：

$$
v_a+v_b+v_c=0
$$

输入实际位于二维子空间。在这个受约束子空间内，可以由 $\alpha\beta$ 唯一恢复三相：

$$
\begin{bmatrix}
v_a\\
v_b\\
v_c
\end{bmatrix}
=
\begin{bmatrix}
1&0\\
-\frac{1}{2}&\frac{\sqrt{3}}{2}\\
-\frac{1}{2}&-\frac{\sqrt{3}}{2}
\end{bmatrix}
\begin{bmatrix}
v_\alpha\\
v_\beta
\end{bmatrix}
$$

这不表示任意 $2\times3$ 矩阵都有普通逆，而是利用了输入的平衡约束。

## 11. 不平衡与零序分量

若：

$$
v_a+v_b+v_c\neq0
$$

则三相中存在不能由 $\alpha\beta$ 表示的零序信息。定义：

$$
v_0=\frac{1}{3}(v_a+v_b+v_c)
$$

与本文归一化一致的一种完整 Clarke 形式为：

$$
\begin{bmatrix}
v_\alpha\\
v_\beta\\
v_0
\end{bmatrix}
=
\frac{2}{3}
\begin{bmatrix}
1&-\frac{1}{2}&-\frac{1}{2}\\
0&\frac{\sqrt{3}}{2}&-\frac{\sqrt{3}}{2}\\
\frac{1}{2}&\frac{1}{2}&\frac{1}{2}
\end{bmatrix}
\begin{bmatrix}
v_a\\
v_b\\
v_c
\end{bmatrix}
$$

对应的逆变换为：

$$
\begin{bmatrix}
v_a\\
v_b\\
v_c
\end{bmatrix}
=
\begin{bmatrix}
1&0&1\\
-\frac{1}{2}&\frac{\sqrt{3}}{2}&1\\
-\frac{1}{2}&-\frac{\sqrt{3}}{2}&1
\end{bmatrix}
\begin{bmatrix}
v_\alpha\\
v_\beta\\
v_0
\end{bmatrix}
$$

理想平衡三相三线系统通常有 $i_a+i_b+i_c=0$，所以可只使用 $\alpha\beta$。三相四线系统、不平衡故障、接地问题以及共模或零序控制中，不能随意丢弃 $v_0$ 或 $i_0$。

## 12. Clarke 与 Park 的复合

设：

$$
\mathbf{v}_{\alpha\beta}=C\mathbf{v}_{abc}
$$

$$
\mathbf{v}_{dq}=P(\theta)\mathbf{v}_{\alpha\beta}
$$

则：

$$
\mathbf{v}_{dq}=P(\theta)C\mathbf{v}_{abc}
$$

展开后得到一种常见的直接 $abc\rightarrow dq$ 形式：

$$
\begin{bmatrix}
v_d\\
v_q
\end{bmatrix}
=
\frac{2}{3}
\begin{bmatrix}
\cos\theta&
\cos\left(\theta-\frac{2\pi}{3}\right)&
\cos\left(\theta+\frac{2\pi}{3}\right)\\
-\sin\theta&
-\sin\left(\theta-\frac{2\pi}{3}\right)&
-\sin\left(\theta+\frac{2\pi}{3}\right)
\end{bmatrix}
\begin{bmatrix}
v_a\\
v_b\\
v_c
\end{bmatrix}
$$

矩阵从右向左执行，所以 $C$ 在右边表示先做 Clarke，$P$ 在左边表示再做 Park。

## 13. 完整数值例子

取 $V=1$、$\theta=30^\circ$：

$$
v_a=\cos30^\circ\approx0.866
$$

$$
v_b=\cos(-90^\circ)=0
$$

$$
v_c=\cos150^\circ\approx-0.866
$$

所以：

$$
\mathbf{v}_{abc}=
\begin{bmatrix}
0.866\\
0\\
-0.866
\end{bmatrix}
$$

经过 Clarke：

$$
\mathbf{v}_{\alpha\beta}=
\begin{bmatrix}
0.866\\
0.5
\end{bmatrix}
=
\begin{bmatrix}
\cos30^\circ\\
\sin30^\circ
\end{bmatrix}
$$

再用同一个 $30^\circ$ 参考角做 Park：

$$
v_d=0.866\times0.866+0.5\times0.5\approx1
$$

$$
v_q=-0.5\times0.866+0.866\times0.5=0
$$

因此：

$$
\begin{bmatrix}
0.866\\
0\\
-0.866
\end{bmatrix}_{abc}
\longrightarrow
\begin{bmatrix}
0.866\\
0.5
\end{bmatrix}_{\alpha\beta}
\longrightarrow
\begin{bmatrix}
1\\
0
\end{bmatrix}_{dq}
$$

## 14. 为什么 dq 适合控制

直接控制 $i_a$、$i_b$、$i_c$ 时，稳态参考量持续按正弦变化。同步旋转后，稳态电流可表示为近似常量：

$$
i_d\approx\mathrm{constant},
\qquad
i_q\approx\mathrm{constant}
$$

控制任务由跟踪正弦参考转化为调节两个近似直流量，因此常规 PI 控制器更容易实现零稳态误差。这是 dq 坐标广泛用于 BESS PCS、电流内环、P/Q 外环、DC-link 控制、GFL、GFM 和 PLL 的重要原因。

当 d 轴与电网电压对齐，即 $v_q=0$ 时，采用本文 amplitude-invariant 约定：

$$
p=\frac{3}{2}v_di_d
$$

而无功与 $v_di_q$ 的具体正负号取决于 q 轴和功率方向约定。工程中不能脱离控制模型直接套用“$i_d$ 控有功、$i_q$ 控无功”的符号关系。

## 15. Park 是时变矩阵

若 $\theta=\theta(t)$，则 $P=P(t)$。对：

$$
\mathbf{x}_{dq}=P(t)\mathbf{x}_{\alpha\beta}
$$

求导得到：

$$
\dot{\mathbf{x}}_{dq}
=\dot{P}\mathbf{x}_{\alpha\beta}
+P\dot{\mathbf{x}}_{\alpha\beta}
$$

额外的 $\dot{P}\mathbf{x}_{\alpha\beta}$ 来自坐标系自身的旋转。把电感、电机或网络动态方程变换到 dq 坐标后，会因此出现与 $\omega$ 成比例的 d-q 交叉耦合项。项的具体正负号必须由当前 Park 定义推导，不能只凭记忆抄写。

## 16. Amplitude-invariant 与 power-invariant

Clarke/Park 变换的缩放并不唯一。

### 16.1 Amplitude-invariant

本文采用 $2/3$ 系数。对于幅值为 $V$ 的平衡三相正弦，变换后空间向量的幅值仍为 $V$，因此幅值关系直观。

在无零序条件下，三相瞬时功率为：

$$
p=\frac{3}{2}
(v_\alpha i_\alpha+v_\beta i_\beta)
$$

Park 是正交旋转，所以内积不变：

$$
p=\frac{3}{2}(v_di_d+v_qi_q)
$$

功率公式中的 $3/2$ 与归一化直接相关，并非额外的物理常数。

### 16.2 Power-invariant

另一类常见变换使用 $\sqrt{2/3}$ 等缩放，使变换矩阵保持欧氏内积，从而让功率表达式不再需要相同的 $3/2$ 系数。

两种归一化各有用途。阅读论文、软件模块或厂商模型时，应同时核对正变换、逆变换和功率公式，不能只比较矩阵中的一个系数。

## 17. 常见误区

1. **Clarke 把真实三相电压变成了两相电压。** 它改变的是坐标表示，不是物理系统。
2. **任意三个相量都能无损压缩成两个数。** 只有在无零序等约束下，三相才只有两个独立自由度。
3. **二维 Clarke 矩阵有普通逆矩阵。** 它不是方阵；平衡条件下的恢复只在受约束子空间内成立。
4. **Park 把电网物理上变成了直流。** 变成常量的是同步旋转坐标中的分量。
5. **$v_q=0$ 在所有 Park 定义下都使用同一个反馈符号。** q 轴方向改变时，误差信号符号也会改变。
6. **所有 Clarke/Park 矩阵都必须完全一样。** 相序、轴方向、角度定义和归一化都会改变矩阵形式。
7. **变换后求导只需要乘 Park 矩阵。** Park 随时间变化，还必须包含 $\dot{P}\mathbf{x}$。
8. **dq 功率公式永远带 $3/2$。** 比例系数取决于所选归一化。

## 18. 下一步接口

- 令 Park 参考角故意偏差 $10^\circ$，计算并绘制 $v_d$ 和 $v_q$；
- 从 $v_q=V\sin\delta$ 推导 SRF-PLL 的小信号误差信号；
- 从时变 Park 变换正式推导 dq 电感方程中的交叉耦合项；
- 进入 BESS PCS 的 dq 电流内环、P/Q 外环与 DC-link 外环；
- 比较 GFL 与 GFM 对参考角和坐标系的不同使用方式。

## 19. 相关笔记

- [矩阵与坐标变换](../mathematics/matrices-and-coordinate-transformations.md)
- [三角函数与复数](../mathematics/trigonometry-and-complex-numbers.md)
- [正弦稳态与相量](sinusoidal-steady-state-and-phasors.md)
- [复功率与 BESS PCS 的 P-Q 能力](complex-power.md)
- [Power Electronics](../../power-electronics/README.md)
- [IBR and Grid Integration](../../ibr-grid-integration/README.md)
