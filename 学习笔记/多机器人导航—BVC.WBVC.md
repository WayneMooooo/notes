# BVC(Buffered Voronoi Cells)
**优点**：只需要相对位置，而现存的其他方法还需要相邻机器人的速度信息。
**算法流程**：每次迭代中，更新计算每个机器人的Voronoi单元，对每个机器人在其Voronoi单元中距离目标点最近的点进行轨迹规划，直至每个机器人到达各自的目标点。
> Collision avoidance: the Voronoi cells are disjoint from one another, thus ensuring that, if each robot stays entirely within its cell at the next time step, there cannot be a collision in all the future.

> Collison Free Configuration(无碰撞构型): A collision free configuration for the group of N robots with identical safety radius $r_s$ is one where the distance between robot i and robot j satisfies $‖p_i − p_j‖ ≥ 2r_s,∀i,j∈{1,2,···,N}, i\ne j.$
## Buffered Voronoi Cell
假定有一组$N$个机器人，各个机器人的位置为$p_1,p_2,...,p_N$，所有机器人的安全半径为$r_s$。
$Voronoi~Cell$: 对于2D平面$\mathbb{R}^2$中的一组$N$个机器人，机器人$i$的Voronoi单元定义为：
$$\mathcal{V}_i=\{p \in \mathbb{R}^2|\|p-p_i\|\leq\|p-p_j\|,\forall j \ne i \} \tag{1}$$
其中，$\|\cdot\|$表示欧式距离。上式也可以表示为（点乘的几何意义）：
$$\overline{\mathcal{V}_i}=\{p \in \mathbb{R}^2|(p-\frac{p_i+p_j}{2})^T p_{i j} \leq 0,\forall j \ne i \} \tag{2}$$
$Buffered~Voronoi~Cell$: 对于2D平面$\mathbb{R}^2$中处于无碰撞构型中的一组$N$个机器人，机器人$i$的Buffered Voronoi单元定义为(在(2)式的基础上增加$p$到$p_i$和$p_j$中点的距离大于$r_s$的约束)：
$$\mathcal{V}_i=\{p \in \mathbb{R}^2|(p-\frac{p_i+p_j}{2})^T p_{i j}+r_s\|p_{i j}\| \leq 0,\forall j \ne i \}\tag{3}$$
## BVC Collision Avoidance Algorithms
假定规划步长为 $T$ 步，对于机器人 $i$ ，每步规划轨迹的位置为 $\bar{p}_{i ,1}、\bar{p}_{i ,2},...,\bar{p}_{i ,T}, \forall i \in \{1,2,...,N\}。$接下来的算法将介绍如何通过求解一个QP问题来得到机器人 $i$ 每次迭代中的规划路径及输入(速度)。
### QP Based Collision Avoidance Algorithm
**Problem1(QP Receding Horizon Path Planning):**
$$\underset{\bar{p}_{i ,1}、\bar{p}_{i ,2},...,\bar{p}_{i ,T}}{minimize}J_i=\sum_{t=0}^{T-1}((\bar{p}_{i ,t}-{p}_{i ,f})^TQ(\bar{p}_{i ,t}-{p}_{i ,f})+u^T_{i ,t}Ru_{i ,t})+(\bar{p}_{i ,T}-{p}_{i ,f})^TQ_f(\bar{p}_{i ,T}-{p}_{i ,f})$$
subject to:
$$\begin{aligned}
&\bar{p}_{i ,t+1}=A\bar{p}_{i ,t}+Bu_{i ,t},t=0,...,T-1, \\
&\bar{p}_{i ,t} \in \bar{V}_i, t=1, \cdots, T, \\
&\bar{p}_{i ,0}=p_i， \\
&\|u_{i, t, x}\|\leq u_{x, max},t=0,\cdots,T-1, \\
&\|u_{i, t, y}\|\leq u_{y, max},t=0,\cdots,T-1, &
\end{aligned}$$
### Analytical Geometric Algorithm
令$\mathcal{V} = (\varepsilon, e)$表示一个平面凸多边形，其中$\varepsilon、e$分别代表多边形的边集及顶点集。对于任意一点$g \in \mathbb{R}^2$，在$\mathcal{V}$中距离$g$最近的点$g^*$要么是$g$本身，要么是在$V$的一条边$\varepsilon^*$上，要么是$V$的一个顶点$e^*$。
**Algorithm: Finding Closest Point**
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/draft.md/102984715248883.png =441x)
### Collision Avoidance Guarantee
**定理：** 对一组处于无碰撞构型中的N个机器人，若其控制输入为$problem1$的解，即其初始位置$p_{1, 0},p_{2, 0},\cdots,p_{N, 0}$满足$\|p_{i, 0}-p_{j, 0}\|\geq2r_s,\forall i\ne j$，则其未来位置$p_{1, k},p_{2, k},\cdots,p_{N, k}$均会处于无碰撞构型之中。

![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%A4%9A%E6%9C%BA%E5%99%A8%E4%BA%BA%E8%B7%AF%E5%BE%84%E8%A7%84%E5%88%92.md/209675720248883.png)

# WBVC with Social Preference 
**Social Preference**：**egoism** and **altruism**
> **Social Value Orientation(SVO):** a metric from social psychology that relates how an individual weights their reward to self versus the reward to others in social dilemmas.
* 与自私对象协同：尽量减少与之的相互干扰
* 与无私对象协同：尽量提高自己的工作效率(more aggresive)
* 具体体现：自私对象拥有更加宽广的Voronoi边界

**WBVCs**: weighted buffered Voronoi cells
对于无私的个体其WBVC更小，因此更容易达到其WBVC的边界，当其触碰此边界时，通过右手定则绕路以避免与自私个体之间的相互干扰。
**研究对象**： 各有目的地的 $N$ 个智能体在凸闭区域 $Q \subset \mathbb{R}^2$ 内的协同导肮问题。定义智能体 $i$ 为半径为 $r_i$的移动机器人。$i$ 在 $t$时刻的位置为 $p_i(t)$ ，其目的地为 $g_i\in Q$。其动力学方程为：
$$\dot{p}_i(t)=u_i(t)$$
其中 $u_i(t)$ 为 $i$ 在t时刻的速度。
此外，$i$ 拥有一个恒定的性格属性 $\gamma_i\in[0, 1]$，  $\gamma_i=0$ 表示无私，$\gamma_i=0.5$ 表示中庸的，$\gamma_i=1$表示自私。$\gamma_i$可以取这个范围内的任意值，其反映了智能体所期望与他人合作的程度。

### Weighted Buffered Voronoi Cell (WBVC)
#### Buffered Voronoi Cell (BVC)
基于**BVC**，增加对智能体性格属性的考虑进一步产生了**WBVC**，其中智能体 $i$ 的Voronoi单元 $V_i$ 计算公式如下：
$$\begin{aligned}
\mathcal{W}_{i} & =\left\{{q} \in Q \mid\left\|{p}_{i}-{q}\right\|^{2} \leq\left\|{p}_{j}-{q}\right\|^{2}-w_{i j}, \forall j \neq i\right\} \\
w_{i j} & =\left(r_{i}+r_{j}\right)\left\|{p}_{i j}\right\|+\frac{1}{2}(\gamma_i-\gamma_j)\underline{\{(r_i+r_j)-\|p_{i j}\|\}\|p_{i j}\|}
\end{aligned}$$

**BVC**及**WBVC**生成的Voronoi图如下所示：
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%A4%9A%E6%9C%BA%E5%99%A8%E4%BA%BA%E5%AF%BC%E8%88%AA%E2%80%94bvc.wbvc.md/153571223256916.png =468x)
**WBVC的性质：**
* 对于处于无碰撞构型中的n个智能体，其$\mathcal{W}_i \ne \emptyset$。
* 对于智能体$i,j$对应的WBVC单元$\mathcal{W}_i,\mathcal{W}_j,~\forall \bar{p}_i\in\mathcal{W}_i,\bar{p}_j\in\mathcal{W}_j$，$\bar{p}_i与\bar{p}_j$之间的距离恒大于等于$r_i+r_j$。
* 对于$\bar{p}_i\in \mathcal{W}_i,\bar{p}_i\in \mathcal{W}_j,\forall i\ne j$，有$\mathcal{W}_i\cap\mathcal{W}_j= \emptyset$。
#### Collision-free Navigation with WBVC
**定理：** 对于一组处于无碰撞构型中的n个智能体，若所有智能体都在各自的Voronoi单元中运动，则在未来任意时刻该组智能体的构型都是无碰撞的。
#### Algorithm Overview
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%A4%9A%E6%9C%BA%E5%99%A8%E4%BA%BA%E5%AF%BC%E8%88%AA%E2%80%94bvc.wbvc.md/332621601249586.png =490x)
仿真结果：
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%A4%9A%E6%9C%BA%E5%99%A8%E4%BA%BA%E5%AF%BC%E8%88%AA%E2%80%94bvc.wbvc.md/267993601259775.png =542x)

# WBVC with Unknown Social Preference 
## Collision-free Navigation with Unknown and Heterogeneous Social
> Thus, cooperative navigation should adapt robots’ motions to different human social preferences.

问题背景：智能体未知其他智能体性格属性。
目标：多智能体在上述背景下实现无碰撞导航。
方法：(基于WBVC)
1. 智能体利用轨迹与性格属性之间的关系对其他智能体的性格属性进行估计。
2. 利用估计的性格属性修正Voronoi单元。智能体在各自的Voronoi单元中运动，以此来避免相互之间的碰撞。

**研究目标**：使如上所述的 $N$ 个智能体无碰撞地到达各自的目的地。
**已知条件**：
* 每个智能体已知自身的位置、半径大小、目的地及性格属性，并且能够获取其余智能体的位置及半径。
* 每个智能体无法获取其他智能体的性格属性。
#### Collision-free Navigation with WBVC
在 $t$ 时刻，智能体 $i$ 在Voronoi单位 $W_i(t)$ 中的离目的地 $g_i$ 最近的目标位置 $p_i^{\prime}$为：
$$p_i^{\prime}=\underset{q \in W_i(t)}{\arg \min}\|q-g_i\|$$
只要智能体在其Voronoi单元中运动就不会与其他智能体产生碰撞，但是在计算 $p_i^{\prime}$ 时可能会陷入死锁，导致 $p_i^{\prime}$ 无法接近目的地 $g_i$ 。为了避免死锁，采用如下的右手准则来计算$t$时刻智能体$i$的目标位置$p_i^*(t)$：
$${p}_{i}^{*}(t)=\left\{\begin{array}{l}
{p}_{i}^{\prime}(t) \text { if }\left\|{p}_{i}^{\prime}(t)-{p}_{i}(t)\right\|>\epsilon \\
{p}_{i}(t) \text { else if }\left\|{g}_{i}-{p}_{i}(t)\right\|=0 \\
\underset{q \in W_{i}(t)}{\arg \min }\left\|{q}-\left\{{p}_{i}(t)+l {R}\left(\frac{\pi}{2}\right) \frac{{g}_{i}-{p}_{i}(t)}{\left\|{g}_{i}-{p}_{i}(t)\right\|}\right\}\right\| otherwise
\end{array}\right.$$
其中 $\epsilon$ 为死锁检测的阈值，$l$为执行右手准则时智能体向右行驶的距离。$R(\theta)$为二维旋转矩阵。
$t$时刻智能体$i$的控制输入$u_i(t)$如下：
$${u}_{i}(t)=\left\{\begin{array}{l}
k({p}_{i}^{*}(t)-{p}_{i}(t)) \text { if }\|k({p}_{i}^{*}(t)-{p}_{i}(t))\| \leq v_{i}^{\max } \\
v_{i}^{\max } \frac{{p}_{i}^{*}(t)-{p}_{i}(t)}{\|{p}_{i}^{*}(t)-{p}_{i}(t)\|} \text { otherwise }
\end{array}\right.$$
其中$k$为正增益系数，$v_i^{max}$为智能体$i$的速度上限。
离散化机器人的动态方程得位置更新式为：
$${p}_{i}((m+1) \Delta t)={p}_{i}(m \Delta t)+u_{i}(m \Delta t) \Delta t$$
其中，$m \in \mathbb{Z}_{\ge 0}$，$\Delta t$表示控制时间步长。设定$k\Delta t\le1$以保证控制不会超调。
### COLLISION-FREE NAVIGATION WITH UNKNOWN SOCIAL PREFERENCES
#### Social Preferences Estimation
**所需条件：** $p_i(m\Delta t),\cdots,p_i((m-M)\Delta t),p_j(m\Delta t),\cdots,p_j((m-M)\Delta t)$，其中$M$为持有目标$j$位置信息时刻到当前时刻的控制步长数。
**原理：** 自私型的智能体拥有更大的Voronoi单元，其在每个控制步长中会比无私型移动得更多；故推断行驶距离更长的智能体其性格属性更偏向于自私型。
**预测结果**： 智能体$i$预测的智能体$j$的性格属性$r_{i j}$。
该预测策略具体表示如下：
$$\begin{aligned}
\hat{\gamma}_{i j}(m \Delta t) & =\left\{\begin{array}{ll}
\gamma_{i} \text { if } D_{i}(m \Delta t) \geq D_{j}(m \Delta t) & (j \neq i) \\
1 \text { otherwise }
\end{array}\right. \\
D_{i}(m \Delta t) & =\left\{\begin{array}{ll}
\sum_{l=0}^{M-1}\left\|p_{i}((m-l) \Delta t)-p_{i}(m-(l+1) \Delta t)\right\| \text{ if } m \geq M \\
\sum_{l=0}^{m-1}\left\|p_{i}((m-l) \Delta t)-p_{i}(m-(l+1) \Delta t)\right\| \text{ otherwise }
\end{array}\right.
\end{aligned}$$
#### Collision-free Navigation with Modified WBVC
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%A4%9A%E6%9C%BA%E5%99%A8%E4%BA%BA%E5%AF%BC%E8%88%AA%E2%80%94bvc.wbvc.md/109361313230458.png)
# B-UAVC
**问题描述**：一组$n$个机器人在一个$d$维空间中，$\mathcal{W} \subseteq \mathbb{R}^d,d\in \{2, 3\}$，空间中分布着$m$个静态多边形障碍物。对于每个机器人$i\in\mathcal{I}=\{1,\cdots,n\}$, $p_i\subseteq\mathbb{R}^d$表示其位置,每个机器人有一个安全半径$r_s$,该组机器人的目标位置为$\mathcal{G}=\{g_1,\cdots,g_n\}$。考虑机器人与障碍物位置由状态估计所得,机器人位置被描述为协方差为$\Sigma_{i}$的高斯分布$\mathbf{p}_{i} \sim \mathcal{N}(\hat{\mathbf{p}}_{i}, \Sigma_{i})$。对于每个障碍物$o\in\mathcal{I}_o=\{1,\cdots,m\}$,用$\hat{\mathcal{O}_o}\subset\mathbb{R}^d$来表示当其处于期望位置时其占据的空间,$\hat{\mathcal{O}_o}$是一组顶点集。由此可以得到障碍物实际占据的空间为$\mathcal{O}_{o}=\{\mathbf{x}+\mathbf{d}_{o} \mid \mathbf{x} \in \hat{\mathcal{O}}_{o}, \mathbf{d}_{o} \sim \mathcal{N}(0, \Sigma_{o})\} \subset \mathbb{R}^{d}$,其中$d_0$为障碍物位置的未知偏移量。
由于机器人组及障碍物位置均为随机变量，因此机器人组的无碰撞状态只能被概率式的满足。对于机器人$i，\mathbf{p}_{i} \sim \mathcal{N}(\hat{\mathbf{p}}_{i}, \Sigma_{i})$，若其与机器人$j，\mathbf{p}_{j} \sim \mathcal{N}(\hat{\mathbf{p}}_{j}, \Sigma_{j})$及障碍物$o,\mathbf{p}_{o} \sim \mathcal{N}(\hat{\mathbf{p}}_{o}, \Sigma_{o})$是概率无碰撞的，则其满足：
$$\begin{aligned}
\operatorname{Pr}(\operatorname{dis}(\mathbf{p}_{i}, \mathbf{p}_{j}) \geq 2 r_{s}) \geq 1-\delta, \quad \forall j \in \mathcal{I}, j \neq i, \\
\operatorname{Pr}(\operatorname{dis}(\mathbf{p}_{i}, \mathcal{O}_{o}) \geq r_{s}) \geq 1-\delta, \quad \forall o \in \mathcal{I}_{o},
\end{aligned}$$
其中$\operatorname{Pr}(\cdot)$表示事件发生的概率，$dis(\cdot)$表示距离函数，$\delta$为碰撞概率阈值。
**问题目标：** 对组内的每个机器人进行运动规划，使其在于其他机器人及障碍物处于概率无碰撞的条件下到达其目标位置。
**解决方法:B-UAVC(Buffered Uncertainty-Aware Voronoi Cell)** 
## B-UAVC(Buffered Uncertainty-Aware Voronoi Cell)
**定义：** 给定一组机器人$i\in\{1,\cdots,n\}$，已知其期望位置$\hat{p_i}\in\mathbb{R}^{d}$及协方差$\Sigma_{i}\in\mathbb{R}^{d\times d}$，且环境中障碍物的形状已知，其期望位置及协方差为${p_0}\in\mathbb{R}^{d}$、$\Sigma_{o}\in\mathbb{R}^{d\times d}$。每个机器人的B-UAVC定义为：
$$\begin{aligned}
\mathcal{V}_{i}^{u, b}=\left\{\mathbf{p} \in \mathbb{R}^{d}: \mathbf{a}_{i j}^{T} \mathbf{p}\right. & \leq b_{i j}-\beta_{i j}, \forall j \neq i, j \in \mathcal{I}, \\
\text { and } \mathbf{a}_{i o}^{T} \mathbf{p} & \left.\leq b_{i o}-\beta_{i o}, \forall o \in \mathcal{I}_{o}\right\}
\end{aligned}$$
其中，$a_{i j},a_{i o}\in\mathbb{R}^d$及$b_{i j},b_{i o}\in\mathbb{R}$描述分隔各个机器人与障碍物的超平面参数，其效果是将整个工区空间进行分解。$\beta_{i j},\beta_{i o}$为附加缓冲参数，其目的是为了构造各个机器人Voronoi单元之间的缓冲空间，以保证其概率无碰撞。
### Inter-robot separating hyperplane
与给定两个确定基点生成Voronoi单元不同，对于含未知性机器人需要借助其期望位置及协方差对其进行隔离，在此使用**best linear separator** （一种分离两个高斯分布的方法）。
给定$\mathbf{p}_{i} \sim \mathcal{N}(\hat{\mathbf{p}}_{i}, \Sigma_{i})$，$\mathbf{p}_{j} \sim \mathcal{N}(\hat{\mathbf{p}}_{j}, \Sigma_{j})$，考虑一个线性分离器$\mathbf{a}_{i j}^{T}\mathbf{p}=b_{i j}$，其中$\mathbf{a}_{i j}\in\mathbb{R}^d,b_{i j}\in\mathbb{R}$。该线性分离器将在空间中的$\mathbf{p}$分为两部分：1）$\mathbf{a}_{i j}^{T}\mathbf{p}\leq b_{i j}$ 2)$\mathbf{a}_{i j}^{T}\mathbf{p}\geq b_{i j}$，通过最小化误分类的最大值就可以求解分离器的参数$\mathbf{a}_{i j}$和$b_{i j}$。
$$\begin{aligned}
\operatorname{Pr}_{i}\left(\mathbf{a}_{i j}^{T} \mathbf{p}>b_{i j}\right) & =\operatorname{Pr}_{i}\left(\frac{\mathbf{a}_{i j}^{T} \mathbf{p}-\mathbf{a}_{i j}^{T} \hat{\mathbf{p}}_{i}}{\sqrt{\mathbf{a}_{i j}^{T} \Sigma_{i} \mathbf{a}_{i j}}}>\frac{b_{i j}-\mathbf{a}_{i j}^{T} \hat{\mathbf{p}}_{i}}{\sqrt{\mathbf{a}_{i j}^{T} \Sigma_{i} \mathbf{a}_{i j}}}\right) \\
& =1-\Phi\left(\left(b_{i j}-\mathbf{a}_{i j}^{T} \hat{\mathbf{p}}_{i}\right) / \sqrt{\mathbf{a}_{i j}^{T} \Sigma_{i} \mathbf{a}_{i j}}\right)
\end{aligned}$$
$$\begin{aligned}
\operatorname{Pr}_{j}\left(\mathbf{a}_{i j}^{T} \mathbf{p} \leq b_{i j}\right) & =\operatorname{Pr}_{j}\left(\frac{\mathbf{a}_{i j}^{T} \mathbf{p}-\mathbf{a}_{i j}^{T} \hat{\mathbf{p}}_{j}}{\sqrt{\mathbf{a}_{i j}^{T} \Sigma_{j} \mathbf{a}_{i j}}} \leq \frac{b_{i j}-\mathbf{a}_{i j}^{T} \hat{\mathbf{p}}_{j}}{\sqrt{\mathbf{a}_{i j}^{T} \Sigma_{j} \mathbf{a}_{i j}}}\right) \\
& =1-\Phi\left(\left(\mathbf{a}_{i j}^{T} \hat{\mathbf{p}}_{j}-b_{i j}\right) / \sqrt{\mathbf{a}_{i j}^{T} \Sigma_{j} \mathbf{a}_{i j}}\right)
\end{aligned}$$
$$\left(\mathbf{a}_{i j}, b_{i j}\right)=\arg \min _{\mathbf{a}_{i j} \in \mathbb{R}^{d}, b_{i j} \in \mathbb{R}}\left(\operatorname{Pr}_{i}, \operatorname{Pr}_{j}\right)$$
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%A4%9A%E6%9C%BA%E5%99%A8%E4%BA%BA%E5%AF%BC%E8%88%AA%E2%80%94bvc.wbvc.md/294062613230465.png =908x)
### Robot-obstacle separating hyperplane
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E5%A4%9A%E6%9C%BA%E5%99%A8%E4%BA%BA%E5%AF%BC%E8%88%AA%E2%80%94bvc.wbvc.md/176585713248891.png =474x)
### Properties of B-UAVC
* **(Inter-Robot Probabilistic Collision Free)** 对于$\forall\mathbf{p}_{i} \sim \mathcal{N}\left(\hat{\mathbf{p}}_{i}, \Sigma_{i}\right)$及$\forall\mathbf{p}_{j} \sim \mathcal{N}\left(\hat{\mathbf{p}}_{j}, \Sigma_{j}\right)$，其中$\hat{\mathbf{p}_{i}}\in\mathcal{V}_{i}^{u, b},\hat{\mathbf{p}_{j}}\in\mathcal{V}_{j}^{u, b}$，且$i\ne j\in \mathcal{I}$，则有
$$\operatorname{Pr}\left(\operatorname{dis}\left(\mathbf{p}_{i}, \mathbf{p}_{j}\right) \geq 2 r_{s}\right) \geq 1-\delta$$
即机器人$i,j$产生碰撞的概率低于阈值$\delta$。
* **(Robot–Obstacle Probabilistic Collision free)** 对于$\forall\mathbf{p}_{i} \sim \mathcal{N}\left(\hat{\mathbf{p}}_{i}, \Sigma_{i}\right)$，其中$\hat{\mathbf{p}_{i}}\in\mathcal{V}_{i}^{u, b}$，有
$$\operatorname{Pr}\left(\operatorname{dis}\left(\mathbf{p}_{i}, \mathcal{O}_{o}\right) \geq 2 r_{s}\right) \geq 1-\delta$$
即机器人$i$与障碍物$o$产生碰撞的概率低于阈值$\delta$。
