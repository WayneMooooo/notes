# Bezier based Method
转换轨迹优化：两段对称的*Bezier*曲线：
$\mathbf{B}_{1}(u)=\sum_{i=0}^{3}\left({ }_{i}^{3}\right) \mathbf{B}_{1i} u^{i}(1-u)^{3-i}$
$$\mathbf{B}_{2}(u)=\sum_{i=0}^{3}\left({ }_{3-i}^{3}\right) \mathbf{B}_{2(3-i)} u^{i}(1-u)^{3-i}$$
如下图所示：
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/算法/路径局部优化算法/bezier%20based%20method.md/273250116231259.png)
两组控制点：
* $B_{1j}(j\in {0,1,2,3})$
* $B_{2j}(j\in {0,1,2,3})$
其中，$B_{j0}(j\in{1,2})$为直线与*Bezier*曲线的连接点，而$B_{j3}$为两段*Bezier*曲线的连接点。

定义转换长度(transition length)其值为$d_1 = ||\mathbf{P_2-B_{10}}||$，$d_2 = ||\mathbf{P_2-B_{20}}||$，上述*Bezier*转换曲线的八个控制点可以如下公式计算：
$$\begin{array}{l}
\begin{array}{l}
\mathbf{B}_{10}=\mathbf{P}_{2}-\mathbf{T}_{1} d \\
\mathbf{B}_{11}=\mathbf{P}_{2}-\mathbf{T}_{1}\left(1-c_{1} c_{3}\right) d \\
\mathbf{B}_{12}=\mathbf{P}_{2}-\mathbf{T}_{1}\left(1-c_{1} c_{3}-c_{3}\right) d \\
\mathbf{B}_{13}=\mathbf{B}_{12}+\eta d \mathbf{u}_{d}
\end{array}\\
\begin{array}{l}
\mathbf{B}_{20}=\mathbf{P}_{2}+\mathbf{T}_{2} d \\
\mathbf{B}_{21}=\mathbf{P}_{2}+\mathbf{T}_{2}\left(1-c_{1} c_{3}\right) d \\
\mathbf{B}_{22}=\mathbf{P}_{2}+\mathbf{T}_{2}\left(1-c_{1} c_{3}-c_{3}\right) d \\
\mathbf{B}_{23}=\mathbf{B}_{22}-\eta d \mathbf{u}_{d}
\end{array}
\end{array}$$
其中$c_1=2(\sqrt{6}-1)/5, c_2=(c_1+4)(c_1+1),c_3=(c_1+4)/(c_2+6), \eta=6c_3\cos{\beta}/(c_1+4),\mathbf{T}_{1}=\overrightarrow{\mathbf{P}_{1} \mathbf{P}_{2}} /\|\overrightarrow{\mathbf{P}_{1} \mathbf{P}_{2}}\|, \mathbf{T}_2=\overrightarrow{\mathbf{P}_2 \mathbf{P}_3} /\|\overrightarrow{\mathbf{P}_2 \mathbf{P}_3}\|, u_d = \overrightarrow{\mathbf{B}_{12} \mathbf{B}_{22}} /\|\overrightarrow{\mathbf{B}_{12} \mathbf{B}_{22}}\|$。此外转换长度及转换过程中的速度上限需满足如下约束：
* 转换长度$d$
    1. 最大转弯曲率$\kappa_{\max }=\frac{2 c_{3} \sin \beta}{3 d \eta^{2}}$
    2. $d \leq \frac{\epsilon_{\max }}{\left(1-c_{1} c_{3}-c_{3}\right) \sin \beta}=c_{4} \epsilon_{\max } \csc \beta$
    其中$\epsilon_{\max }$为最大偏离值，$c_4 = \frac{1}{(1-c_1c_3-c_3)}$。
* 转换过程中的速度上限：
    $v = \min{(\frac{2}{T_s}\sqrt{r^2-(r^2-\delta^2)^2}, \sqrt{rA_{max}})}$
    其中$T_s$为插值周期或控制周期，$A_{max}$为加速度上限，$r = \frac{1}{\kappa_{max}}$为转换曲线的最小曲率，$\delta$为最大转弯误差。

# Footnotes
[1] Dong W, Liu S, Ding Y, et al. An artificially weighted spanning tree coverage algorithm for decentralized flying robots[J]. IEEE Transactions on Automation Science and Engineering, 2020, 17(4): 1689-1698.








