# Multi-AGV’s Temporal Memory-Based RRT Exploration in Unknown Environment
**系统结构：**
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E8%AE%BA%E6%96%87%E9%98%85%E8%AF%BB%E7%AC%94%E8%AE%B0/multi-agv%E2%80%99s%20temporal%20memory-based%20rrt%20exploration%20in%20unknown%20environment.md/132643221235939.png =802x)
## **Focus work:**
### Temporal Memory-Based RRT Goal Assigner Module
#### Revenue Function With Relative Position
在增益函数引入相对位置信息去避免AGV之间的“近距离探索”现象。
修改如下：
$$R\left(x_{f p}\right)=\left(\lambda h\left(x_{f p}, x_{r}\right) I\left(x_{f p}\right) f\left(x_{f p}, x_{G_{\{1, \ldots, n\}}}\right)\right)-C\left(x_{f p}\right)$$
其中，$f(x_{f p}, x_{G_{\{1, \ldots, n\}}}))$为相对位置函数，用于计算相对位置信息，具体如下：
$$f\left(x_{f p}, x_{G}\right)=\left\{\begin{array}{ll}
0.01 & \frac{\min \left(\left\|\mathrm{x}_{\mathrm{G}}-\mathrm{x}_{\mathrm{fp}}\right\|\right)}{r p_{\text {dist }} }<0.01 \\
\frac{\min \left(\left\|\mathrm{x}_{\mathrm{G}}-\mathrm{x}_{\mathrm{fp}}\right\|\right)}{r p_{\text {dist }}} & 0.01 \geq \frac{\max \left(\left\|\mathrm{x}_{\mathrm{G}}-\mathrm{x}_{\mathrm{fp}}\right\|\right)}{r p_{\text {dist }}} \leq 1.00 \\
1.00 & \frac{\min \left(\left\|\mathrm{x}_{\mathrm{G}}-\mathrm{x}_{\mathrm{fp}}\right\|\right)}{r p_{\text {dist }}}>1.00
\end{array}\right.$$
其中，$x_{G_{\{1, \ldots, n\}}}$为当前时刻各个AGV的目标任务点，$rp_{dist}$是距离阈值。
#### Temporal Memory-Based Goal Assignment
* temporal-based goal assignment
对于每个分配个AGV的任务点，引入一个自适应时间，来避免AGV在一个任务点上花费过多时间或者陷入无法到达的任务点。这个时间的计算过程如下：
$$k\left(x_{f p}, x_{r}, z\right)=\left\{\begin{array}{ll}
t_{p m} & \left\|x_{r}-x_{f p}\right\|<h_{r a d} \\
t_{p m} \times\left\|x_{r}-x_{f p}\right\| & h_{r a d} \geq\left\|x_{r}-x_{f p}\right\| \leq z, \\
t_{p m} \times z & \left\|x_{r}-x_{f p}\right\|>z
\end{array},\right.$$
$$t_{ge,x_{fp}}=t_{gs} + k\left(x_{f p}, x_{r}, z\right)$$
其中$z$为距离阈值，
* memory-based goal assignment
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E8%AE%BA%E6%96%87%E9%98%85%E8%AF%BB%E7%AC%94%E8%AE%B0/multi-agv%E2%80%99s%20temporal%20memory-based%20rrt%20exploration%20in%20unknown%20environment.md/585541923236549.png =534x)
## Comparision
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E8%AE%BA%E6%96%87%E9%98%85%E8%AF%BB%E7%AC%94%E8%AE%B0/multi-agv%E2%80%99s%20temporal%20memory-based%20rrt%20exploration%20in%20unknown%20environment.md/5732423245717.png)·












