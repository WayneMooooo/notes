# Autonomous Robotic Exploration Based on Multiple Rapidly-exploring Randomized Trees
## A new strategy for detecting frontier points using RRT.
**该探索算法的整体框图：**

![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/论文阅读笔记/rrt%20exploration/autonomous%20robotic%20exploration%20based%20on%20multiple%20rapidly-exploring%20randomized%20trees.md/218914714240743.png)
### RRT-Based Frontier Detector Module
基于占据栅格地图，栅格值为-1，0，1，分别代表unknow、free、occupied。初始状态下地图未知，栅格地图中均为未知栅格。
#### Local Frontier Detector

![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/论文阅读笔记/rrt%20exploration/autonomous%20robotic%20exploration%20based%20on%20multiple%20rapidly-exploring%20randomized%20trees.md/17504914259169.png =682x)

mwm的简述：探索过程是在已探索的自由空间中随机采样$x_{rand}$, 然后计算已构建的树$G(V, E)$中距离$x_{rand}$最近的顶点$x_{nearest}$，最后沿着$x_{nearest} - x_{rand}$方向延伸$\eta$得到点$x_{new}$, 在检测$x_{new}$的占据状态之后决定是否将其加入$G(V, E)$。

#### Global Frontier Detector

![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/论文阅读笔记/rrt%20exploration/autonomous%20robotic%20exploration%20based%20on%20multiple%20rapidly-exploring%20randomized%20trees.md/281625314247036.png =412x)

#### The Need for Global and Local Frontier Detectors
* Local detector可以让机器人探索边界点的速度更快。因为机器人总是在靠近某个边界点的路径上，因此以机器人当前位置进行RRT探索更有机会探索到未知区域（边界点）。
* Global detector是为了保证不漏掉那些处于地图小角落中的边界点，同时可以检测到那些里机器人当前位置较远的边界点。
#### Why RRT？
* RRT更倾向于探索未知区域。因为邻近未知区域的顶点会拥有更大的Voronoi区域(所有顶点作为Voronoi基点)面积，因此生成树会有更大概率通过这些点向未知区域生长。

![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/论文阅读笔记/rrt%20exploration/autonomous%20robotic%20exploration%20based%20on%20multiple%20rapidly-exploring%20randomized%20trees.md/584945314267202.png =496x)

* RRT不限于二维空间，同样可以应用于三维空间。
* RRT是概率完备的，这能保证整个地图的完全探索。
### Filter
* Filter模块首先对从local detector和global detecor传来的边界点进行聚类，然后只保留各聚类的中心点，其余点舍去（降采样）。
* 同时会删除无效的边界点（invalid and old frontier points）。
### Task Allocator
根据如下几个因素来分配边界点：
* Navigation cost(N): 用机器人当前位置与边界点之间的欧氏距离来度量。
* Information gain(I): 用到达一个边界点所能探索到的未知区域面积大小来度量。
定义探索边界点$x_{fp}$的收益如下：
$$\begin{aligned}
R\left(x_{f p}\right) & =\lambda h\left(x_{f p}, x_{r}\right) I\left(x_{f p}\right)-N\left(x_{f p}\right), \\
h\left(x_{f p}, x_{r}\right) & =\left\{\begin{array}{ll}
1, & \text { if }\left\|x_{r}-x_{f p}\right\|>h_{\text {rad }} \\
h_{\text {gain }}, & h_{\text {gain }}>1
\end{array}\right.
\end{aligned}$$
其中，$\lambda$为一个正常数权重，用于配置对于navigation cost和information gain之间的偏向。$h\left(x_{f p}, x_{r}\right)$是一个滞后增益，用于使机器人更加偏向于探索邻近区域，这样能够避免路径重复。
### Implementation diagram

![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/论文阅读笔记/rrt%20exploration/autonomous%20robotic%20exploration%20based%20on%20multiple%20rapidly-exploring%20randomized%20trees.md/29395514259871.png =511x)