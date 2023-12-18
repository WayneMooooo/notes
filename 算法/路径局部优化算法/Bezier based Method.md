# Bezier based Method
两段对称的*Bezier*曲线：
$\mathbf{B}_{2}(u)=\sum_{i=0}^{3}\left({ }_{3-i}^{3}\right) \mathbf{B}_{2(3-i)} u^{i}(1-u)^{3-i}$
$$\mathbf{B}_{2}(u)=\sum_{i=0}^{3}\left({ }_{3-i}^{3}\right) \mathbf{B}_{2(3-i)} u^{i}(1-u)^{3-i}$$
如下图所示：
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/算法/路径局部优化算法/bezier%20based%20method.md/87985015249685.png)
两组控制点：
* $B_{1j}(j\in {0,1,2,3})$
* $B_{2j}(j\in {0,1,2,3})$
其中，$B_{j0}(j\in{1,2})$为直线与*Bezier*曲线的连接点，而$B_{j3}$为两段*Bezier*曲线的连接点。

定义