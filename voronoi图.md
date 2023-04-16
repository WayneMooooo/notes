# voronoi图
**定义：** Voronoi图，又叫泰森多边形或Dirichlet图，它是由一组由连接两邻点直线的垂直平分线组成的连续多边形组成。N个在平面上有区别的点，按照最邻近原则划分平面；每个点与它的最近邻区域相关联。
**数据结构：**
* Voronoi Site
* Voronoi Edge
* Voronoi Vertex
* Voronoi Cell

## voronoi图的性质
**基本性质：** Cell里面任意一点到该Cell所属的Site的直线距离，比这点到其他Site的直线距离都短；
**定理7.2：** 给定由平面上任意n 个点构成的集合P。若所有点都共线，则Vor( P )由n-1 条平行直线构成；否则，Vor( P )将是连通的，而且其中的边不是线段就是射线。
**定理7.3：** 若n ≥ 3，则在与平面上任意n 个基点相对应的Voronoi 图中，顶点的数目不会超2n-5，而且边的数目不会超过3n-6。
**定理7.4：** 对于任一点集P 所对应的Voronoi 图Vor( P )，下列命题成立：
1. 点q 是Vor( P )的一个顶点，当且仅当在其最大空圆CP(q)的边界上，至少有三个基点；
2. pi 和pj 之间的平分线确定了Vor( P )的一条边，当且仅当在这条线上存在一个点q，CP(q)的边界经
过pi 和pj，但不经过其它基点。

[Voronoi图：基本概念和性质](https://blog.csdn.net/fengkeyleaf/article/details/123699207)