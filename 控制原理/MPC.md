# MPC轨迹跟踪
## 1. 模型预测控制原理
模型预测控制（MPC）的核心思想就是以优化方法求解最优控制器，其中优化方法大多时候采用二次规划（Quadratic Programming）。MPC利用一个已有的模型、系统当前的状态和未来的控制量，来预测系统未来的输出，然后与我们期望的系统输出做比较，得到一个损失函数（代价函数），即：
$$\text { 损失函数 }=(\text { 末来输出 (模型, 当前状态, 末来控制量 })-\text { 期望输出 })^{2}$$
由于上式中模型、当前状态、期望输出都是已知的，因此只有未来控制量一个自变量。采用二次规划的方法求解出某个未来控制量，使得损失函数最小。MPC控制器优化得到的控制输出也是系统在未来有限时间步的控制序列。 当然，由于理论构建的模型与系统真实模型都有误差，所以，实际上更远未来的控制输出对系统控制的价值很低，故MPC仅执行输出序列中的第一个控制输出。
MPC：利用一个比原始空间大很多的模型（较其他方法更大的计算成本）仅仅得到当前一步的最优控制器。
MPC在实现上有三个要素：
1. 预测模型，是模型预测控制的基础，用来预测系统未来的输出；
2. 滚动优化，一种在线优化，用于优化短时间内的控制输入，以尽可能减小预测模型输出与参考值的差距；
3. 反馈矫正，在新的采样时刻，基于被控对象的实际输出，对预测模型的输出进行矫正，然后进行新的优化，以防模型失配或外界干扰导致的控制输出与期望差距过大。
## 2. MPC流程
模型预测控制在k时刻共需三步：
### 第一步：获取系统的当前状态；
### 第二步：基于$\mathrm{u}_{\mathrm{k}},\mathrm{u}_{\mathrm{k+1}},\mathrm{u}_{\mathrm{k+2}},...,\mathrm{u}_{\mathrm{k+m}}$进行最优化处理。
### 第三步：只取$\mathrm{u}_{\mathrm{k}}$作为控制输出作用在系统上。
在下一时刻重复以上三步，在下一步进行预测时使用的就是下一步的状态值，我们将这样的方案称为滚动优化控制（Receding Horizon Control）。
## 3.MPC VS LQR
### 3.1 目标函数：求代价（误差，控制量）的最小值
$$M P C: \min J=\sum_{i=0}^{N p}\left(x_{(k+i+1)}^{T} Q x_{(k+i+1)}+u_{(k+i)}^{T} R u_{(k+i)}\right)$$
$$L Q R: \min J=\int_{t 0}^{t f}\left(x^{T} Q x+u^{T} R u\right) d t$$
MPC目标函数是一个累计和，LQR目标函数是一个积分，本质都是对代价的累计。
###  3.2 求解方法：变分法求解Riccati方程，QP求解器工具
 *LQR* : 采用变分法，通过求解Riccati方程进行逼近，最终获得控制序列。
 *MPC*：转化为二次规划问题，利用求解器（如qpOASES、cvxpy）进行求解，生成控制序列。
 
###  3.3 工作时域：求解一次；预测时域、控制时域、滚动优化
 *LQR*：求解控制周期内的控制序列，只求解一次，每个周期取对应的控制量即可。
 *MPC*：求预测时间段Np内的控制序列，并在下个周期进行滚动优化，每次只取控制序列的第一个值作为控制的输入。
 LQR在一个固定的时域上求解，且一个时域内只有一个最优解，而MPC在一个逐渐消减的时域内( in a receding time window )求解最优解，且最优解经常更新。
 
### 3.4 LQR和MPC的缺点
*LQR*:
1. LQR不滚动优化，预测时间段内的控制序列只求解一次。
2. LQR在控制周期内只进行一次计算，而MPC可在更小的时间窗口中求解优化问题。
3. LQR求解过程中假设控制量不受约束，但是实际情况下，控制量是有约束的。

*MPC*: 计算量大，实时性差，对于算力要求高。

## 4. MPC控制器设计
对于线性模型，MPC的求解问题可转化为一个二次规划问题。
对于以下线性模型：
$$\mathrm{x}_{\mathrm{k}+1}=\mathrm{Ax}_{\mathrm{k}}+\mathrm{B} \mathrm{u}_{\mathrm{k}}+\mathrm{C}\tag{1}$$
假定未来T步的控制输入已知，为$\mathrm{u}_{\mathrm{k}},\mathrm{u}_{\mathrm{k+1}},\mathrm{u}_{\mathrm{+2}},...,\mathrm{u}_{\mathrm{k+T}}$,根据以上模型及输入，我们可以预测未来T步的状态如下：
$$\begin{array}{l}
\mathrm{x}_{\mathrm{k}+1}=\mathrm{Ax}_{\mathrm{k}}+\mathrm{Bu}_{\mathrm{k}}+\mathrm{C} \\
\mathrm{x}_{\mathrm{k}+2}=\mathrm{Ax}_{\mathrm{k}+1}+\mathrm{Bu}_{\mathrm{k}+1}+\mathrm{C}=\mathrm{A}\left(\mathrm{Ax}_{\mathrm{k}}+\mathrm{Bu}_{\mathrm{k}}+\mathrm{C}\right)+\mathrm{Bu} \mathrm{u}_{\mathrm{k}+1}+\mathrm{C}=\mathrm{A}^{2} \mathrm{x}_{\mathrm{k}+1}+\mathrm{ABu}_{\mathrm{k}}+\mathrm{Bu} \mathrm{u}_{\mathrm{k}+1}+\mathrm{AC}+\mathrm{C} \\
\mathrm{x}_{\mathrm{k}+3}=\mathrm{A}^{3} \mathrm{x}_{\mathrm{k}}+\mathrm{A}^{2} \mathrm{Bu} \mathrm{u}_{\mathrm{k}}+\mathrm{AB}_{\mathrm{k}+1}+\mathrm{Bu}_{\mathrm{k}+2}+\mathrm{A}^{2} \mathrm{C}+\mathrm{AC}+\mathrm{C} \\
\ldots \\
\mathrm{x}_{\mathrm{k}+\mathrm{T}}=\mathrm{A}^{\mathrm{T}} \mathrm{x}_{\mathrm{k}}+\mathrm{A}^{\mathrm{T}-1} \mathrm{Bu}_{\mathrm{k}}+\mathrm{A}^{\mathrm{T}-2} \mathrm{Bu}_{\mathrm{k}+1}+\ldots+\mathrm{A}^{\mathrm{T}-\mathrm{i}} \mathrm{Bu}_{\mathrm{k}+\mathrm{i}-1}+\ldots+\mathrm{Bu}_{\mathrm{k}+\mathrm{T}-1}+\mathrm{A}^{\mathrm{T}-1} \mathrm{C}+\mathrm{A}^{\mathrm{T}-2} \mathrm{C}+\ldots+\mathrm{C}
\end{array}$$
将上面的T规划成矩阵形式：
$$\mathcal{X}=\mathcal{A} \mathrm{x}_{\mathrm{k}}+\mathcal{B} \mathbf{u}+\mathcal{C}\tag{2}$$
其中，
$$\begin{array}{l} 
\mathcal{X}=\left[\begin{array}{lllll}
\mathrm{x}_{\mathrm{k}+1} & \mathrm{x}_{\mathrm{k}+2} & \mathrm{x}_{\mathrm{k}+3} & \ldots & \mathrm{x}_{\mathrm{k}+\mathrm{T}}
\end{array}\right]^{\mathrm{T}}, \\
\mathbf{u}=\left[\begin{array}{lllll}
\mathrm{u}_{\mathrm{k}} & \mathrm{u}_{\mathrm{k}+1} & \mathrm{u}_{\mathrm{k}+2} & \ldots & \mathrm{u}_{\mathrm{k}+\mathrm{T}-1}
\end{array}\right]^{\mathrm{T}}, \\
\mathcal{A}=\left[\begin{array}{llllll}
\mathrm{A} & \mathrm{A}^{2} & \mathrm{~A}^{3} & \ldots & \mathrm{A}^{\mathrm{T}}
\end{array}\right]^{\mathrm{T}}, \\
\mathcal{B}=\left[\begin{array}{ccccc}
\mathrm{B} & 0 & 0 & \ldots & 0 \\
\mathrm{AB} & \mathrm{B} & 0 & \ldots & 0 \\
\mathrm{~A}^{2} \mathrm{~B} & \mathrm{AB} & \mathrm{B} & \ldots & 0 \\
\ldots & \ldots & \ldots & \ldots & \ldots \\
\mathrm{A}^{\mathrm{t}-1} \mathrm{~B} & \mathrm{~A}^{\mathrm{T}-2} \mathrm{~B} & \mathrm{~A}^{\mathrm{T}-3} \mathrm{~B} & \ldots & \mathrm{B}
\end{array}\right], \\
\mathcal{C}=\left[\begin{array}{c}
\mathrm{AC}+\mathrm{C} \\
\mathrm{A}^{2} \mathrm{C}+\mathrm{AC}+\mathrm{C} \\
\ldots \\
\mathrm{A}^{\mathrm{k}+\mathrm{T}-1} \mathrm{C}+\ldots+\mathrm{C}
\end{array}\right],
\end{array}$$
假定参考轨迹为$\overline{\mathcal{X}}=\left[\begin{array}{lllll}\overline{\mathbf{x}}_{\mathrm{k}+1} & \overline{\mathbf{x}}_{\mathrm{k}+2} & \overline{\mathbf{x}}_{\mathrm{k}+3} & \cdots & \overline{\mathbf{x}}_{\mathrm{k}+\mathrm{T}}\end{array}\right]^{\mathrm{T}}$，则MPC的一个简单目标代价函数如下：
$$\begin{array}{c}
\min \mathcal{J}=\mathcal{E}^{\mathrm{T}} \mathrm{Q} \mathcal{E}+\mathbf{u}^{\mathrm{T}} \mathrm{R} \mathbf{u} \\
\text { s.t. } \mathrm{u}_{\min } \leq \mathbf{u} \leq \mathrm{u}_{\max }
\end{array}\tag{3}$$
其中，$\mathcal{E}=\mathcal{X}-\overline{\mathcal{X}}=\left[\begin{array}{llll}\mathrm{x}_{\mathrm{k}+1}-\overline{\mathrm{x}}_{\mathrm{k}+1} & \mathrm{x}_{\mathrm{k}+2}-\overline{\mathrm{x}}_{\mathrm{k}+2} & \ldots & \mathrm{x}_{\mathrm{k}+\mathrm{T}}-\overline{\mathrm{x}}_{\mathrm{k}+\mathrm{T}}\end{array}\right]^{\mathrm{T}}$将（2）式代入式（3），则优化变量仅乘u。以上最优化问题可用二次规划方法求解，得到目标代价函数最优控制序列$\left\{\begin{array}{lllll}u_{k} & u_{k+1} & u_{k+2} & \ldots & u_{k+T}-1\end{array}\right\}$。