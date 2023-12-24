# A Multi-Robot Coverage Path Planning Algorithm for the Environment with Multiple Land Cover Types

## Motivation
> **However, none of the existing studies has considered the complexity of the surface of the geographical environment in the task area. Especially in tasks, such as emergency search and rescue, the efficiency of robots or unmanned aerial vehicles is seriously affected by various surface features. Therefore, considering the impact of different land cover types in outdoor environmental conditions is of considerable importance.**

## MCPP-MLCT ALGORITHM
**定义移动速度、视野的概念**
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/论文阅读笔记/mcpp/a%20multi-robot%20coverage%20path%20planning%20algorithm%20for%20the%20environment%20with%20multiple%20land%20cover%20types.md/545701212250393.png)
移动速度及视野的值不仅跟机器人本身性能相关，还取决于机器人所处的环境。

**构造层次四叉树**
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/论文阅读笔记/mcpp/a%20multi-robot%20coverage%20path%20planning%20algorithm%20for%20the%20environment%20with%20multiple%20land%20cover%20types.md/136151712246948.png)
层次四叉树用于描述不同土地覆盖类型下的机器人视野。层次四叉树的顶层是由一组大小相同的正方形网格对整个任务区域进行划分，这些网格被称为单元格。从顶层开始，使用递归的方式计算下一层的单元格。将当前层的单元格分为四部分，得到下一层的单元格。

**算法概览**
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/论文阅读笔记/mcpp/a%20multi-robot%20coverage%20path%20planning%20algorithm%20for%20the%20environment%20with%20multiple%20land%20cover%20types.md/127032512242702.png)
1. 首先建立层次四叉树，用于描述不同土地覆盖类型下的机器人视野。同时，在层次四叉树中建立同层和不同层细胞之间的相邻拓扑关系，提高任务分配和路径规划的效率。
2. 使用方位角趋势法对区域进行划分。该算法将对应的覆盖类型中的单元格大小与移动速度的比值作为单元格的任务成本，以平衡每个机器人的任务成本。
3. 最后使用改进STC算法对各个子区域做路径规划。

## 构造层次四叉树
**参数：**
* $Tr$：层次四叉树
* $l$：四叉树层数
* $C_{i,r,c}$：第$i$层中$r$行$c$列的单元格
* $C_{S_i}$：第$i$层中单元格的大小
* $C_{V_{i,r,c}}$：$C_{i,r,c}$的土地覆盖类型
* $Fd_{CV_{i,r,c}}$：$C_{i,r,c}$中的机器人视野值
* $Sd_{CV_{i,r,c}}$：$C_{i,r,c}$中的机器人移动速度值
* 每个单元格都有相同层和不同层的相邻单元格。$Nc_{i,r,c}$：$C_{i,r,c}$的相邻单元格

### 基于机器人视野构造层次四叉树
通常，高度复杂的土地覆盖对应于四叉树中较小的机器人视野和细胞大小。相比之下，简单的土地覆盖对应于机器人较大的视野和四叉树中较大的单元格大小。
**视野值近似**
层次四叉树中的相邻层的单元格的大小应该是两倍的关系(单元格用于表示机器人视野大小)。而实际上的机器人在不同土地覆盖类型下的并不能满足这种关系。为了简化这个问题，对原始视野进行了近似。用$Fd_{max}$ 和$Fd_{min}$分别表示近似视野的最大值和最小值。用$Fd_t=Fd_{min}2^k$来近似各层的视野值大小。

![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/论文阅读笔记/mcpp/a%20multi-robot%20coverage%20path%20planning%20algorithm%20for%20the%20environment%20with%20multiple%20land%20cover%20types.md/168660213260582.png)

**计算层数**
$$l=1+log_2\frac{Fd_{max}}{Fd_{min}}$$
**构造四叉树**

从顶层向下，每一个下层通过上层的四叉树划分得到，直到单元格大小等于$Fd_{min}$的两倍。四叉树中的每一层对应一个视野，任务区域内所有土地覆盖类型中的视野都可以在四叉树中找到。然而，四叉树中的图层与任务区域的土地覆盖类型之间并不存在一一对应的关系。

四叉树中的每一层都可以完全覆盖任务区域，但是整个任务区域只需要覆盖一次。因此，四叉树中只有部分单元格必须与实际的土地覆盖类型相关联，避免不同图层单元格的重复和冗余覆盖。因此，我们将层次四叉树中的单元格分为两类：活性单元格和辅助单元格。活性单元格具有真实的地表覆盖类型，所有的活性单元格可以完全覆盖任务区域而不需要重复(如图4中的案例所示)。辅助单元格是土地覆盖类型设置为null的剩余单元格。

![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/论文阅读笔记/mcpp/a%20multi-robot%20coverage%20path%20planning%20algorithm%20for%20the%20environment%20with%20multiple%20land%20cover%20types.md/598934813258186.png)

### 构建相邻拓扑关系
**shared neighbor direction**
用来指示单元格与其父单元格的共享相邻单元格的方向。

![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/论文阅读笔记/mcpp/a%20multi-robot%20coverage%20path%20planning%20algorithm%20for%20the%20environment%20with%20multiple%20land%20cover%20types.md/216241814255688.png)

例如，如果该值为9，那么我们可以立即确定上层中的邻居细胞是父细胞在顶部和左侧方向上的邻居细胞。类似地，下层中父代细胞的邻居细胞包含子代细胞在顶部和左侧方向上的邻居细胞。

![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/论文阅读笔记/mcpp/a%20multi-robot%20coverage%20path%20planning%20algorithm%20for%20the%20environment%20with%20multiple%20land%20cover%20types.md/214523214236929.png)

## 基于方位趋势法的任务分配算法
**参数：** 矩形覆盖区域$D$，机器人总数$s$，$R_j$为第$j$个机器人，$Sp_j$为$R_j$的初始位置，$A_j$为机器人$R_j$的任务区域，$St_j$为$A_j$中的生成树，$P_j$为由其生成的STC路径，$T_j$为$A_j$中的覆盖时间，任务分配需要满足以下条件：
1. $A_{1} \cup A_{2} \cup \ldots \cup A_{s}=D ;$
2. $A_{j} \cap A_{k} \rightarrow \emptyset, \forall j, k \in[1, s]  and  j \neq k ;$
3. $\left|T_{1}\right| \approx\left|T_{2}\right| \approx \cdots \approx\left|T_{s}\right| ;$
4. $S t_{j}  is connected,  \forall j \in[1, s] ;$
5. $S p_{j} \in A_{j}, \forall j \in[1, s] .$

将所有活性单元格无重复的分配给各个机器人满足1， 2；将单元格大小与机器人在相应土地覆盖类型中移动速度的比值作为单元格的覆盖成本，计算并平衡每个机器人的任务成本以满足约束条件( 3 )；方位趋势法以满足约束条件( 4 )和( 5 )。

![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/论文阅读笔记/mcpp/a%20multi-robot%20coverage%20path%20planning%20algorithm%20for%20the%20environment%20with%20multiple%20land%20cover%20types.md/217195714259369.png)

分配方式为在机器人已得到的区域的相邻单元格集中选择一个单元格分配给该机器人，两个问题：(1)选择哪个机器人作为分配对象？(2)选择相邻单元格集中的哪个栅格进行分配？

### 确定分配对象$R_t$
在任务分配过程中，选择任务成本最小的机器人作为分配对象，从而达到均衡分配的目的。任务成本的计算方式如下：
$$Q a_{j}=\sum_{c_{i, r, c} \in A_{j}} \frac{2 S c_{i}}{S d_{i, r, c}}$$

### 确定分配栅格$C_t$
将机器人$R_t$的任务区域$A_t$的相邻单元格集$Na_t$分为两类：(1)已被分配给其他的机器人的单元格集$Na_{t,as}$;(2)未被分配的单元格集$Na_{t,un}$。待分配单元格$C_t$的选取规则如下：
1. $Na_{t,un}$中的单元格优先于$Na_{t,as}$被选择；
2. $Na_{t,un}$中的单元格选取规则：
首先计算$Na_{t,un}$中各单元格的成本值，然后按照成本增加的顺序选择待分配的单元，成本计算公式如下：
$$\operatorname{cost}_{u n, i}=\operatorname{Dis}\left(C_{t, u n, i}, C a_{t}\right)-\frac{\sum_{j \neq t} \operatorname{Dis}\left(C_{t, u n, i}, C a_{k}\right)}{n-1}$$
其中，$Ca_k$表示区域$A_k$的中心点，$C_{t,un,i}$表示$Na_{t,un}$中的单元格，$Dis(C_i, Ca_j)$表示$C_i$和$Ct_k$的距离。
3. $Na_{t,as}$中的单元格选取规则：
将$Na_{t,as}$划分成两个部分：$Na_{t,as,nu}$、$Na_{t,as,fu}$。$Na_{t,as,nu}$中的单元格优先于$Na_{t,as,fu}$中的单元格被选择。在这两个单元格集中的选择规则为根据单元格成本的升序来选择要分配的单元格。成本计算公式如下：
$$\operatorname{cost}_{a s, i}=\operatorname{Dis}\left(C_{t, a s, i}, C a_{t}\right)$$

![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/论文阅读笔记/mcpp/a%20multi-robot%20coverage%20path%20planning%20algorithm%20for%20the%20environment%20with%20multiple%20land%20cover%20types.md/319671416241185.png)

## IMPOVED STC
STC
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/论文阅读笔记/mcpp/a%20multi-robot%20coverage%20path%20planning%20algorithm%20for%20the%20environment%20with%20multiple%20land%20cover%20types.md/578792116231715.png)

Improved STC
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/论文阅读笔记/mcpp/a%20multi-robot%20coverage%20path%20planning%20algorithm%20for%20the%20environment%20with%20multiple%20land%20cover%20types.md/344732216234219.png) 

## 实验结果
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/论文阅读笔记/mcpp/a%20multi-robot%20coverage%20path%20planning%20algorithm%20for%20the%20environment%20with%20multiple%20land%20cover%20types.md/282665417243166.png)

![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/论文阅读笔记/mcpp/a%20multi-robot%20coverage%20path%20planning%20algorithm%20for%20the%20environment%20with%20multiple%20land%20cover%20types.md/449275417236051.png)