# DMSTC

## Problem Formulation

 mCPP问题通常包含装备了某些传感工具(如相机，雷达等)的$r$个机器人，其中的机器人能够完成一个面积至少比其自身尺寸大的区域$D$。目标环境$\mathbb{A} \in \mathbb{R^2}$为一个有界的平面区域，其中分布有限且已知的障碍物。根据机器人的覆盖能力$D$的大小，环境$\mathbb{A}$通过*cell decomposition based method or grid_base method*被定义为一个栅格区域grid $G$，由一系列等大cell $2D$组成。有些cell被定义为occupied： 这些cell中存在障碍物或者是不可通行的且形成一个集合$\mathcal{B}$；其余的cell属性为free，形成一个集合$\mathcal{L}$，它们是可通行的并且需要被机器人至少覆盖一次。free cell 和 occupied cell对应图1(画一个描述栅格分解的图，标注小栅格subcell，大栅格cell，free栅格，occupied栅格)中的黑白cells。
 
mCPP问题的解包含一系列开始于机器人初始位置的栅格序列路径$X_i\forall i\in\{1,\cdots,r\}$，每条路径对应一个机器人。栅格区域$G$的free cells属于这些路径中的至少一条。这些路径$X_i\forall i\in\{1,\cdots,r\}$应满足如下条件
$$\begin{array}{l}
\underset{X}{\operatorname{minimize}} \max _{i \in\left\{1, \ldots, r\right\}}\left|X_{i}\right| \\
\text { subject to } X_{1} \cup X_{2} \cup \cdots \cup X_{r} \supseteq \mathcal{L}
\end{array}$$
其中其中$|X_i|$表示路径$X_i$的长度。

本文中，基于mCPP的平衡性要求，我们聚焦于如何去得到一个能够实时调整的解——通过机器人的实际覆盖情况来调整任务分配，以期尽可能多的利用多机器人系统的效能。在这种情况下我们希望mCPP问题的解是以均衡多机器人系统的整体效能为目的最小化整体区域覆盖时间而得到的，不同于一般的均分式的mCPP解决方法。因此定义$W_i=\frac{|X_i|}{v_i}\forall i\in\{1,\cdots,r\}$为机器人$i$完成其分配到的覆盖任务的总体时间，其中$v_i$为机器人$i$的平均速度。Then the problem can be formulated as follows：
$$\begin{array}{l}
\underset{X}{\operatorname{minimize}} \max _{i \in\left\{1, \ldots, r\right\}} W_{i} \\
\text { subject to } X_{1} \cup X_{2} \cup \cdots \cup X_{r} \supseteq \mathcal{L}
\end{array}$$

## THE DSTC ALGORITHM

