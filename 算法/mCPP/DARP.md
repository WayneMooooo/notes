# DARP
## Multi-Robot Coverage Path Planning
### 问题描述
**地图区域描述**
$\mathcal{U}=\{x,y:x\in[1,rows],y\in[1,cols]\}~~~~~~~~~~~~n=rows \times cols$
**区域中障碍物(占据格数为$n_o$)描述**
$B=\{(x,y)\in\mathcal{U}:(x,y)~is~occupied\}$
**覆盖区域描述**
$\mathcal{L}=\mathcal{U}-B~~~~~~~~~~~~~~~~~~~~~$需要覆盖的格数$l=n-n_o$
**定义1** ：单元格$(x_i, y_i)$与$(x_j,y_j)$相邻，若：
$$||x_i-x_j||+||y_i-y_j||\leq 1$$
假定机器人在每个时刻可在地图中定位当前位置，并可以运动到任意与其当前位置相邻的单元格中。
**定义2** ：一条长度为m的可行路径为一个单元格序列 ：
$$X=((x_1,y_1),\cdots,(x_m,y_m))$$
约束如下：
* $(x_i,y_i)\in{\mathcal{L}}, \forall i\in{1,\cdots,m}$
* $(x_i,y_i)与(x_{i+1},y_{i+1})$为相邻单元格。
* 若路径为闭路径，则序列中首尾单元格需为相邻单元格。

机器人位置定义：
$$\chi_{i}(t)=\left(x_{i}, y_{i}\right) \in \mathcal{L}, \forall i \in\left\{1, \ldots, n_{r}\right\}$$
其中：t为时间戳，$n_r$代表机器人编号。每个机器人的初始位置(位于$\mathcal{L}$中)为$\chi_i(t_0)$
**mCPP问题描述** : 计算机器人编队的路径$X_{i}^{*} \forall i \in\left\{1, \ldots, n_{r}\right\}$ ，使得：
$$\begin{array}{l}
\underset{X}{\operatorname{minimize}} \max _{i \in\left\{1, \ldots, n_{r}\right\}}\left|X_{i}\right| \\
\text { subject to } X_{1} \cup X_{2} \cup \cdots \cup X_{n_{r}} \supseteq \mathcal{L}
\end{array}$$
其中$|X_i|$表示路径$X_i$的长度。
**最优解定义** ：若序列${L_1,L_2,\cdots,L_{n_r}}$为mCPP问题的最优解，则：
1. $L_i\cap L_j=\emptyset,\forall i,j\in1,\cdots,n_r,i\ne j$
2. $L_1 \cup L_2 \cup\cdots\cup L_{n_r}=\mathcal{L}$
3. $|L_1| \approx |L_2| \cdots \approx |L_{n_r}|$
4. $L_i$是一个连通区域，$\forall i \in1,\cdots,n_r$
5. $\chi_i(t_0)\in L_i$
## DARP(Divide Areas based on Robots Initial Positions)
**关键词 ：** 
* evaluation matrix $E_i$:  表示第$i$个机器人初始位置到$\mathcal{L}$中单元格的可达到程度(e.g. 距离)。
* assignment matrix $A$，其构造如下：
$$A_{x,y}=\underset{i\in\{1,\cdots,n_r\}}{\operatorname{argmin}}~E_{i|x,y},~\forall (x,y)\in\mathcal{L}$$
该矩阵用于构造每个机器人的工作区域：
$$L_i=\{(x,y)\in\mathcal{L}:A(x,y)=i\},~\forall i\in\{1,\cdots,n_r\}~~~~~k_i=|L_i|$$
### Equally Divide the Space
$E_i$的初值：$E_{i|x,y}=d(\chi_i(t_0),[x,y]^\tau),~\forall i\in{1,\cdots,n_r}$
定义修正因子$m_i$：$E_i=m_iE_i$
定义等分代价函数：$J=\frac{1}{2}\sum_{r=1}^{n_r}{(k_i-f)^2}$，其中$f$为全局的均分最优解。
### Spatial Connected Areas
$$\begin{array}{c}
\mathcal{C}_{i \mid x, y}=\min (\|[x, y]-r\|)-\min (\|[x, y]-q\|), \\
\forall r \in \mathcal{R}_{i}, q \in \mathcal{Q}_{i}
\end{array}$$
### Iterative Equation
$$E_{i}=\mathcal{C}_{i} \odot\left(m_{i} E_{i}\right)$$

## Ideal-Shaped Spanning Tree 
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/mcpp/darp.md/354323614242205.png =879x)
相同格数的区域存在不同的路径转弯数。
算法通过不同子区域间相互交换节点来减少各子区域规划路径的转弯数。希望达到如下效果：
$$\begin{array}{c}
T_{i} \leq T_{i 0}, \quad \forall i \in 1, \ldots, r \\
\min \sum_{i=1}^{n_{r}} T_{i}
\end{array}
$$
算法分为两方面内容：确定可交换的节点、交换规则。

### 可交换节点
前提：各子区域已构造好各自的生成树（各区域的路径转弯次数已确定）。
为了保证生成树的连通性，只有生成树的末端节点可被交换，分为以下三类：
* I-shaped end node：存在两次转弯，当该点被舍弃，仍存在两次转弯。
* L-shaped end node：存在四次转弯，当该点被舍弃时，转弯数减少两次。
* T-shaped end node：存在四次转弯，当该点被舍弃时，该点处不存在转弯。

![三种 end node](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/mcpp/darp.md/41145614260085.png =614x)

### 交换规则
为了确保能够减少转弯次数，节点交换需满足如下原则：
* 每次交换意味着一个子区域舍弃一个末端节点，另一个子区域接收此节点。
* 在舍弃一个末端节点时，T形末端节点首选，L形次之。在接收节点时，优先生成I形末端节点，L形次之。以此规则交换节点，经过一轮循环后，若此轮交换后不能满足算法目的，则舍弃此轮交换进入新的一轮循环。
* 每次循环遍历每个子区域中的每个可交换节点。
* 当转弯次数不再下降或达到迭代次数时循环结束，输出最优路径。

![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/mcpp/darp.md/1541715255191.png =668x)
左164 右148

### 算法流程
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/mcpp/darp.md/498571415257689.png =845x)

