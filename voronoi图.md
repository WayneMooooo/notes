# voronoi图
**定义：** Voronoi图，又叫泰森多边形或Dirichlet图，它是由一组由连接两邻点直线的垂直平分线组成的连续多边形组成。N个在平面上有区别的点，按照最邻近原则划分平面；每个点与它的最近邻区域相关联。
**更数学一点的定义：** 定义任意两点之间的欧式距离为$dist(p, q)$，就平面情况而言，有：
$$dist(p,q)=(px-qx)^2+(py-qy)^2$$
&emsp; &emsp;设$P=\{p_1, \cdots, p_n\}$为平面上任意n个互异的点；这些点也就是几点。所谓P对应的Voronoi图，就是平面的一个子区域划分——整个平面被划分成n个单元（cell），它们具有如下性质： 
$$任一点q位于p_i所对应的单元中，当且仅当对于任何的p_j\in P,j\neq i，都有dist(q,p_i)<dist(q,p_j)。$$
&emsp; &emsp;我们将与$P$对应的Voronoi图记作$Vor(P)$。“$Vor(P)$”或者“Voronoi图”所指示的仅仅只是组成该子区域划分的边和顶点。在$Vor(P)$中，与基点$p_i$所对应的单元记作$V(p_i)$——称作$p_i$对应的Voronoi单元。 
&emsp; &emsp;任给平面上的两点$p$和$q$，所谓$p$和$q$的平分线(bisector)，就是线段$pq$的垂直平分线。该平分线将平面划分成两张半平面(half-plane)。点$p$所在的那张开半平面记作$h(p,q)$，点$q$所在的那张开半平面记作$h(q,p)$。注意，$r\in h(p,q)$当且仅当$dist(r,p)\leq dist(r,q)$。据此，可以得出如下观察结论：
$$V(p_i)=\cap h(p_i, p_j), 1 \leq j \leq n, j\neq i$$
也就是说，$V(p_i)$是(n-1)张半平面的公共交集；他也是一个（不见得有界）开的凸多边形(convex polygon)子区域。很显然，Voronoi顶点到相邻的三个site距离相等；Voronoi边上任意一点到相邻的两个site距离相等。
&emsp; &emsp;对于任何点$q$，我们将以$q$为中心、内部不包含$P$中任何基点的最大圆，称作$q$关于$P$的最大空圆(largest empty circle)，记作$C_P(q)$。以下定理指出了Voronoi图的顶点及边所具有的特征：
对于任一点集$P$所对应的Voronoi图$Vor(P)$，下列命题成立：
1）点$q$是$Vor(P)$的一个顶点，当且仅当其最大空圆$C_P(q)$上至少有三个基点；（Voronoi顶点是三个site的外接圆的圆心）
2）$p_i$和$p_j$之间的平分线确定了$Vor(P)$的一条边，当且仅当在这条线上存在一个点$q$，$C_P(q)$的边界经过$p_i$和$p_j$，但不经过其它站点。
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/voronoi%E5%9B%BE.md/262345710230657.png)
**数据结构：**
* Voronoi Site
* Voronoi Edge
* Voronoi Vertex
* Voronoi Cell
* 复杂度为O(n)

## voronoi图的性质
* 维诺图把平面划分成n个非空多边形域，每个多边形内有且只有一个生成元；
* 每个多边形内的点到该生成元距离短于到其他生成元距离；
* 多边形边界上的点到生成此边界的两个生成元的距离相等；
* Voronoi图至多有2 * n-5个顶点和3 * n-6条边；
* 多边形内的生成元是形成三边的三点构成的三角形的外界圆圆心，而且所有的这些外接圆内部不含任何除这三点之外的生成元(Site)(空心圆特性)

## 建立Voronoi图的方法
(三角剖分算法)：生成Voronoi图时先生成其对偶元Delaunay三角网，再找出三角网每一三角形的外接圆圆心，最后连接相邻三角形的外接圆圆心，形成以每一三角形顶点为生成元的多边形网。如下图所示。
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/voronoi%E5%9B%BE.md/192260914230656.png)
建立Voronoi图算法的关键是离散数据点合理地连成三角网，即构建Delaunay三角网。
建立Voronoi图的步骤具体如下：
*  离散点自动构建三角网，即构建Delaunay三角网。对离散点和形成的三角形编号，记录每个三角形是由哪三个离散点构成的。
* 计算每个三角形的外接圆圆心，并记录。
* 遍历三角形链表，寻找与当前三角形pTri三边共边的相邻的三角形TriA，TriB和TriC。
* 如果找到，则把寻找到的三角形外心与pTri的外心连接，存入维诺边链表中。如果找不到，则求出最外边的中垂线射线存入维诺边链表中。
* 遍历结束，所有维诺边被找到，根据边画出维诺图。

### Delaunay三角网的生成
(Bowyer逐点插入法)Bowyer逐点插入法是一个很经典的实现方法，网上的资料大多数都是采用的这种算法。这里使用的算法是按照在论文中提供的伪代码实现的，以下摘自他的论文
> subroutine triangulate
**input :** vertex list 　　输入：顶点链表
**output :** triangle list　　输出：三角形链表
&emsp; &emsp;initialize the triangle list　　初始化三角形链表
&emsp; &emsp;determine the supertriangle　　确定超级三角形
&emsp; &emsp;add supertriangle vertices to the end of the vertex list 将超级三角形的顶点加入到顶点链表中
&emsp; &emsp;add the supertriangle to the triangle list　　将超级三角形加入到三角形链表中
&emsp; &emsp;for each sample point in the vertex list　　对顶点链表中的每一个点
&emsp; &emsp;&emsp;initialize the edge buffer　　初始化边数组
&emsp; &emsp;&emsp;&emsp;for each triangle currently in the triangle list　　对于三角形链表中的每一个三角形
&emsp; &emsp;&emsp;&emsp;calculate the triangle circumcircle center and radius　　计算出外接圆圆心和半径
&emsp; &emsp;&emsp;&emsp;if the point lies in the triangle circumcircle then　　如果这个点在三角形的外接圆内
&emsp; &emsp;&emsp;&emsp;&emsp; &emsp;add the three triangle edges to the edge buffer　　把这个三角形的三条边加入到边数组中
&emsp; &emsp;&emsp;&emsp;&emsp; &emsp;remove the triangle from the triangle list　　从三角形链表中将这个三角形删除
&emsp; &emsp;&emsp;&emsp;endif
&emsp; &emsp;&emsp;&emsp;endfor
&emsp; &emsp;&emsp;&emsp;delete all doubly specified edges from the edge buffer　　把边数组中所有重复的边都删除，注意这里是把所有的重复边都删除，比如有边e1,e2,e2,e3，删除后应该只剩e1和e3
&emsp; &emsp;&emsp;&emsp;&emsp;this leaves the edges of the enclosing polygon only　　这步会得到一个闭合的多边形
&emsp; &emsp;&emsp;&emsp;add to the triangle list all triangles formed between the point 用这个点和边数组中的每一条边都组合成一个三角形，加入到三角形链表中
&emsp; &emsp;&emsp;&emsp;&emsp;and the edges of the enclosing polygon
&emsp; &emsp;endfor
&emsp; &emsp;remove any triangles from the triangle list that use the supertriangle vertices从三角形链表中删除使用超级三角形顶点的三角形
&emsp; &emsp;remove the supertriangle vertices from the vertex list将超级三角形的顶点从顶点链表中删除
end

上述算法基本步骤如下：
* 构造一个超级三角形，包含所有的离散点，并将这个三角形放入三角形链表。
* 将点集中的离散点依次插入，在三角形链表中找出其外接圆包含插入点的三角形（称为该点的影响三角形），删除影响三角形的公共边，将插入点同影响三角形的全部顶点连接起来，从而完成一个点在Delaunay三角形链表中的插入。
* 根据优化准则对局部新形成的三角形进行优化。将形成的三角形放入Delaunay三角形链表。
* 循环执行上述第2步，直到所有离散点插入完毕。
关键步骤2如下图所示：
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/voronoi%E5%9B%BE.md/525900820230656.png)
步骤3的局部优化的准则指的是：
* 对新形成的三角形进行优化，将两个具有共同边的三角形合成一个多边形。
* 以最大空圆准则做检查，看其第四个顶点是否在三角形的外接圆之内。
* 如果在，修正对角线即将对角线对调，即完成局部优化过程的处理。
LOP(Local Optimization Procedure)处理过程如下图所示：
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/voronoi%E5%9B%BE.md/198831220249082.png)
