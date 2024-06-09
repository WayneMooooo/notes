# Online Exploration and Coverage Planning in Unknown Obstacle-Cluttered Environments
***Non-holonomic mobility constraints(非完整性运动约束)***: 机器人或车辆在运动过程中受到的限制，使其无法在所有方向上自由移动。
* 非滑动约束（Non-sliding constraint）：机器人或车辆在运动过程中受到的约束使其无法在某些方向上发生滑动，例如汽车的驱动轮与地面之间的摩擦力限制了车辆的横向滑动。
* 非旋转约束（Non-rotational constraint）：机器人或车辆在运动过程中受到的约束使其无法绕某些轴旋转，例如一辆只能前后移动的小推车。

**问题：online coverage planning in unknown environments for vehicles with non-holonomic constraints.**

基于六边形栅格地图分解

unknow, bounded environment

## 问题描述

单个机器人在一个未知的有限空间$\mathcal{S}$中作业，空间中的障碍物位置杂乱且未知。机器人需要在空间$\mathcal{S}$中完成全覆盖工作。
**坐标系：** 二维笛卡尔坐标系，立方体坐标系。世界坐标系$W$为二维笛卡尔坐标系，坐标轴为${ }^{W} x,{ }^{W} y$

![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/论文阅读笔记/cpp/未知地图存在障碍物cpp.md/351754120231266.png)

机器人建模成一个杜宾斯车辆如下：
$${ }^{W} \dot{x}={ }^{W} v \cos \theta,{ }^{W} \dot{y}={ }^{W} v \sin \theta, \dot{\theta}=u_{d}$$
其中，$({ }^{W} \dot{x}, { }^{W} \dot{y})$为机器人的位置坐标，$\dot{\theta}$为航向角，速度$v$为常数，$u_d \in \{-1,0,1\}$。
**一些变量名：**
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/论文阅读笔记/cpp/未知地图存在障碍物cpp.md/562275120249692.png)
在六边形网格平面中，一个六边形子区域$S_k$的立体坐标系坐标为$({ }^{H} {x_k}, { }^{H} {y_k}, { }^{H} {z_k})^3$。$S_k$的中心位置在坐标系$W$中的坐标为：
$$\left[\begin{array}{l}
W_{x} \\
W_{y}
\end{array}\right]=\left[\begin{array}{ccc}
\frac{3}{2} r & 0 & 0 \\
0 & \frac{\sqrt{3}}{2} r & -\frac{\sqrt{3}}{2} r
\end{array}\right]\left[\begin{array}{lll}
x_{k} & y_{k} & z_{k}
\end{array}\right]^{T} .$$
每个$S_k$有一个相邻单元格集：
$$\begin{aligned}
\mathcal{N}\left(S_{k}\right)=\{ & \left(x_{k}, y_{k}-1, z_{k}+1\right),\left(x_{k}+1, y_{k}-1, z_{k}\right), \\
& \left(x_{k}+1, y_{k}, z_{k}-1\right),\left(x_{k}, y_{k}+1, z_{k}-1\right) \\
& \left.\left(x_{k}-1, y_{k}+1, z_{k}\right),\left(x_{k}-1, y_{k}, z_{k}+1\right)\right\}
\end{aligned}$$

定义圆形路径的半径为$r_t$，则$r_{min} \leq r_t \leq l_r$，理想情况下设定$r_t=l_r$以达到无冗余的覆盖。

六边形网格的边长$r$取决于机器人的覆盖能力(这里机器人的覆盖范围为一个半径为$l_r$的圆盘)。$r = l_r + r_t$。
在满足以下条件的情况下:
1. 导航传感器至少能够检测到相邻子区域中的障碍物;
2. 机器人无需考虑续航问题;
3. 合理的机器人最小转弯半径。
<center><b>问题的解决目标为：机器人全覆盖每个六边形单元的外接圆 </b></center>

## ONLINE HIERARCHICAL COVERAGE PLANNING
两层式的规划方法：(1)在六边形网络中，规划机器人在接下来的几个时间步中要访问的子区域序列；(2)在二维笛卡尔坐标系中，用Dubins路径来规划机器人的路径用于覆盖当前的子区域并移动至下一子区域。

### Hex Decomposition Coverage Planning
两种模式：
* ***Observing Mode***:当机器人访问一个子区域时，其进入***Observing Mode***，当再次访问该子区域时不再触发；
* ***Transitioning Mode***:当机器人完成对一子区域的覆盖，其进入***Transitioning Mode***来移动至下一子区域。
只有***Observing Mode***能触发观测传感器，而导航传感器在两个模式中一直处于收集信息的状态。

**算法流程：**

![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/论文阅读笔记/cpp/未知地图存在障碍物cpp.md/355795421237559.png)

其中，$(\tau,S_k)$表示机器人在第$\tau$步访问子区域$S_k$；$\mathcal{P}_{\tau}=\left\{\left(t, S_{k}\right) \mid t \in[1, \tau], S_{k} \in \mathcal{S}\right\}$表示机器人从起始到第$\tau$步的路径；$\mathcal{V}_{\tau}$表示到第$\tau$步为止已访问过的子区域集合；$\xi_{\tau} = \left\{\left(S_{k}, u\right) \mid S_{k} \in \mathcal{S}, u \in\{0,1\}\right\}$表示已探测的子区域及其状态的集合，其中$u=0$表示无障碍物的子区域。$\mathcal{Q}_{\tau}(S_i) = \{S^*_j \in \mathcal{N}(S_i)|S^*_j\notin  \mathcal{C}_{\tau}\}, 0 \leq \mathcal{Q}_{\tau}(S_i) \leq 6$，其中$\mathcal{C}_{\tau}=\mathcal{V}_{\tau} \cup \mathcal{E}_{\tau, u=1}$， $\mathcal{Q}_{\tau}(S_i)$表示机器人下一目标子区域的候选。

* **$\mathcal{Q}_{\tau}(S_i) \neq 0$时选择下一目标子区域的标准：Eq.(1)**
$$\begin{array}{rl}
\arg \max _{S_{j}^{*}} & f\left(S_{j}^{*}\right) \\
\text { s.t. } & S_{j}^{*} \in \mathcal{Q}_{\tau}\left(S_{i}\right),\left|\mathcal{Q}_{\tau}\left(S_{i}\right)\right|>0 .
\end{array}$$
其中$f(\cdot)$为计算候选对象$S_i$的已访问或被障碍占据的邻居数量。该策略保证了机器人不会留下任何孤立的未被访问的子区域，避免了返回该区域的需要。

* $\mathcal{Q}_{\tau}^{\prime} \neq 0$时
若$\mathcal{Q}_{\tau}(S_i)$为0，则表示$S_i$周围的子区域已全部被覆盖，这种情况下，下一目标子区域$S_j$从集合$\mathcal{Q}_{\tau}^{\prime}=\left\{S_{j}^{*} \in \mathcal{V}_{\tau} \mid \mathcal{N}\left(S_{j}^{*}\right) \backslash \mathcal{C}_{\tau} \neq \emptyset\right\}$中选取，该集合元素为拥有未被访问且无障碍邻居的已被访问子区域。
**选择下一目标子区域的标准：Eq.(2)**
$$\begin{array}{rl}
\arg \max _{S_{j}^{*}} & t \\
\text { s.t. } & \left(t, S_{j}^{*}\right) \in \mathcal{P}_{\tau}, S_{j}^{*} \in \mathcal{Q}_{\tau}^{\prime}, t \in[1, \tau] .
\end{array}$$
该策略使机器人重访一个最近的访问过的子区域$S_j$，该子区域具有未访问过的无障碍相邻子区域。

![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/论文阅读笔记/cpp/未知地图存在障碍物cpp.md/90660023257725.png)

## 仿真结果
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/论文阅读笔记/cpp/未知地图存在障碍物cpp.md/270441913250396.png)

![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/论文阅读笔记/cpp/未知地图存在障碍物cpp.md/316612113246951.png)

* 六边形分解的优势：即在障碍物杂乱的环境中，将障碍物标记为更多的"类圆形"六边形，而不是正方形细胞。











