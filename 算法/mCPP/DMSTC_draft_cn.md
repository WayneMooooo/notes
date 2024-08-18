# DMSTC

## abstract

尽可能充分地利用多机器人系统的效能时解决multi-robot coverage path planning (mCPP) problem的目标之一。在执行覆盖任务时，多机器人系统中如果出现未预期的不均衡状态(如速度变化)，会导致多机器人系统的效能未被充分利用，进一步会reduce the task execution efficiency of the system。In this papaer, we propose an area-division based mCPP algorithm that can 根据个机器人的覆盖情况实时调整division，通过一exchange based mechanism, 以尽量利用多机器人系统的效能。numerical simulations are carried out at last. The result demonstrate the proposed algoritm 在机器人效率恒定均衡的场景下获得一个较优的解，which is quite close to the state of the art mCPP method，同时在执行覆盖任务时多机器人系统出现效率差异时，the proposed 算法也能提供a dynamic solution，以提高整体的覆盖效率。


## Introduction

Complete path planning <mark>[enric]</mark> is the task to determine a path that covers all points of an area of interest,either by physical contact or sensor detection, while avoiding obstacles and optimizing the travel time, processing speed, cost energy, and so on.CPP has many robotic applications such as vacuum cleaning robot<mark>[Viet]</mark>，lawn mowing<mark>[Schirmer]</mark>,mine countermeasures<mark>[Morin]</mark>, structure inspection<mark>[Jing]</mark>, agriculture<mark>[Maini]</mark>, marine surveying and mapping<mark>[Ma]</mark>, search and rescue operation<mark>Ai</mark>.

In recent decades, researchers have devoted considerable attention to CPP, yielding numerous and effective methods. Within this domain, the single robot coverage planning problem has seen the development of several mature methods. These include cellular decomposition[Choset,Coombes], spanning tree coverage[Gabriely], graph-based coverage[Le], dynamic programming based coverage[Morin], and hex-decomposition-based coverage[Kan].

The use of multi-robot teams in coverage path planning (forming the mCPP problem) significantly enhances operational efficiency and reduces the time required for complete coverage, particularly in large or complex environments. A critical aspect and challenge of mCPP lies in how coverage tasks are distributed among the robots. Current research indicates that existing methods struggle with uneven coverage speeds within these systems. Tasks are typically statically assigned to each robot and then executed, which may appear balanced initially. However, disparities in actual coverage performance, such as differences in coverage speed or the varying times required to cover different areas, prevent the multi-robot system from maximizing its potential effectiveness.

This paper presents a novel algorithm named Multi-robot Dynamic Spanning Tree Coverage (MDSTC) to address the issue of real-time imbalances in mCPP.  We treat mCPP as the problem of dividing the operational area into sub-areas, with each sub-area assigned to a single robot for independent coverage. To find a well-balanced division of regions, MDSTC initially conducts a rough partitioning of the area and subsequently adjusts the divisions iteratively through an exchange-based mechanism. For single robot coverage within sub-areas, we propose a variant of the Spanning Tree Coverage (STC) method, namely Dynamic Spanning Tree Coverage (DSTC). DSTC plans coverage incrementally while constructing a spanning tree and executing the path, allowing reserved unplanned areas for dynamic adjustments by MDSTC. Our algorithm is an online mCPP solution that can make timely adjustments in response to imbalances that arise during the execution of coverage tasks by multiple robots. The proposed method is evaluated in simulations featuring dynamic imbalance scenarios, and further tests are also designed to demonstrate other performance capabilities of the algorithm.


## Related Work

single-robot coverage problem已经被研究得很好了，其中spanning tree coverage (STC)展现了更好的表现，不受制于机器人初始位置及障碍物分布。STC是一种cell-decomposition base method, 先计算 spanning tree of the cell graph，然后机器人circumnavigate the spanning tree。Figure 1 shows an example of STC。基于STC，several methods have been proposed to tackle the multi-robot coverage path planning problem. Hazon and Kaminka在[hazon]中首次提出了一种基于STC算法的名为MSTC的mCPP解决方案。该方法基于机器人的初始位置将整个STC路径拆分后分配给各个机器人，该算法可以有效应对robot failure。但是该方法很依赖于机器人的初始位置以及生成树的构造。为了改进均衡性能以提高覆盖效率，Zheng等人在[zheng]中提出了一种MFC的方法。该方法通过构造一个forest of trees with one tree for each robot提高了整体效率。该方法被证明是有效的即便是存在复杂障碍物并且计算复杂度仍是polynomial。为了追求在线效率，Senthilkumar和Bharadwaj[Senthilkumar]提出了一种同时构建生成树的在线式算法，其在没有先验知识的环境下仍然有效且能达到很高的覆盖率。Dong等人在提出了AWSTC的算法，as a suitable solution for multi-UAV area coverage in a distributed manner.[dong，Chleboun]。在AWSTC中，同时为各个机器人构建生成树，生成树的生长方向为the center of inertia of the uncovered area. 此外该作者还做了实物实验in a controlled environment, with no unexpected obstacles.recently，Kapoutsis等人首次提出了使用divide-area的算法来解决mCPP问题的方法名为DARP[Kapoutsis, Gao,Xin, Idir]，以追求一个最优coverage solution。该方法先将concerned area等分给各个机器人，各个机器人再在各自的区域里进行STC路径构造。DARP通过区域划分的方式，将mCPP问题reduces成了多个single-robot CPP问题。

建立在上述works的视角上，目前的算法已经可以很好的求解mCPP问题对于已知的静态环境，而且解能兼顾均衡和效率。这些方法一般求出一个静态的均衡解，然而as aforementioned, 实际的运行情况中多机器人系统可能并不如想象的那么均衡，运行环境也可能导致个机器人之间的运行效率出现差异。我们的方法考虑到了这个问题，提出了一个对于这种不平衡问题的mCPP动态解决方案。


## Problem Formulation

 mCPP问题通常包含装备了某些传感工具(如相机，雷达等)的$r$个机器人，其中的机器人能够完成一个面积至少比其自身尺寸大的区域$D$。目标环境$\mathbb{A} \in \mathbb{R^2}$为一个有界的平面区域，其中分布有限且已知的障碍物。根据机器人的覆盖能力$D$的大小，环境$\mathbb{A}$通过*cell decomposition based method or grid_base method*被定义为一个栅格区域grid $\mathcal{G}$，由一系列等大$D$-size cell 组成。In the following context,  2D-size cells are called *cells*, and D-size cells are called *subcells*.  有些cell被定义为*occupied*： 这些cell中存在障碍物或者是不可通行的且形成一个集合$\mathcal{B}$；其余的cell属性为*free*，形成一个集合$\mathcal{L}$，它们是可通行的并且需要被机器人至少覆盖一次。free cell 和 occupied cell对应<mark>图1</mark>(画一个描述栅格分解的图，标注小栅格subcell，大栅格cell，free栅格，occupied栅格)中的黑白cells。
 
mCPP问题的解包含一系列开始于机器人初始位置的栅格序列路径$X_i\forall i\in\{1,\cdots,r\}$，每条路径对应一个机器人。栅格区域$G$的free cells属于这些路径中的至少一条。这些路径$X_i\forall i\in\{1,\cdots,r\}$应满足如下条件
$$\begin{array}{l}
\underset{X}{\operatorname{minimize}} \max _{i \in\left\{1, \ldots, r\right\}}\left|X_{i}\right| \\
\text { s.t. }~ X_{1} \cup X_{2} \cup \cdots \cup X_{r} \supseteq \mathcal{L}
\end{array}$$
其中其中$|X_i|$表示路径$X_i$的长度。

本文中，基于mCPP的平衡性要求，我们聚焦于如何去得到一个能够实时调整的解，使得这个解能够最小化多机器人系统的整体区域覆盖时间，不同于一般mCPP的均衡目标。因此定义$W_i=\frac{|X_i|}{v_i}\forall i\in\{1,\cdots,r\}$为机器人$i$完成其分配到的覆盖任务的总体时间，其中$v_i$为机器人$i$的速度。Then the problem can be formulated as follows：
$$\begin{array}{l}
\underset{X}{\operatorname{minimize}} \max _{i \in\left\{1, \ldots, r\right\}} W_{i} \\
W_i=\frac{|X_i|}{v_i}\forall i\in\{1,\cdots,r\}\\
\text { s.t. }~ X_{1} \cup X_{2} \cup \cdots \cup X_{r} \supseteq \mathcal{L}.
\end{array}$$

## THE DSTC ALGORITHM

在本文中我们提出了一种基于Spiral-STC改进<mark>cite-STC</mark>的在线式cpp算法DSTC for a single robot，以适用于之后要介绍的MDSTC for multi-robot。我们将*sliding window*这个概念加入到原始的STC算法中。对于一个在<mark>covering graph(找个地方把这个概念定义一下，可以参考MFC)</mark>$\mathcal{G}$机器人with current positioin $p_{cur}$, *sliding window*被定义成一个包含$p_{cur}$所在cell的一系列<mark>相邻(定义)</mark>cells$\mathcal{G}_{window}$，如<mark>图2</mark>(画一个滑动窗口的图，可以考虑将DSTC中的某些部分也画进去)。DSTC在一个规划周期内只会规划覆盖区域内容一部分区域而不是整个整个区域。DSTC算法基于*sliding window*进行规划，在每个*sliding window period*内输出一段路径供机器人执行，以此形成一个在线的CPP过程。

We demonstrate the detailed process for DSTC in Algorithm 1。WIth cell decomposition得到的Covering graph $\mathcal{G}$, 机器人开始于初始cell $p_{initial}$。同时，定义每个规划周内输出的window path length $\mathcal{P}_{cur}$ 为 $l$，机器人已走过的路径记为$\mathcal{P}_{pre}$。在一个规划周期内，DSTC根据机器人当前位置$p_{cur}$拓展新的*sliding window* $\mathcal{G}_{window}$，并在其中生成Spiral-STC路径$\mathcal{P}_{window}$。倘若当前的$\mathcal{G}_{window}$不为空，也就意味着机器人周围仍存在free cell可以用来构造 STC路径$\mathcal{P}_{window}$，然后这个路径将被分成两部分：一部分作为当前规划周期内的路径输出$\mathcal{P}_{cur}$；除$\mathcal{P}_{cur}$以外的部分在删除重复或inappropriate部分后与剩余路径$\mathcal{P}_{rest}$进行拼接(<mark>见图2</mark>)。如果$\mathcal{G}_{window}$为空，which means $p_{cur}$被*occupied* cell 包围，这时DSTC将沿着$\mathcal{P}_{rest}$进行回溯。在途中一旦检测到free cell，新的*sliding window*与随之而生的STC路径$\mathcal{P}_{back}$将会产生，当前规划周期的输出路径$\mathcal{P}_{cur}$将由回溯历经的$\mathcal{P}_{rest}$部分和$\mathcal{P}_{back}$的一部分组成，$\mathcal{P}_{cur}$的剩余部分同样会与$\mathcal{P}_{rest}$进行拼接式地融合。就如以上所描述的，DSTC可以分为两个规划动作：*开拓*：通过*sliding window*探索free cell并规划覆盖路径；*回溯*：根据$\mathcal{P}_{rest}$完成之前剩余的路径的同时找寻扩展新的*sliding window*的可能性。通过不断重复*开拓*和*回溯*这两个过程，DSTC能探索完所有的free cells，并在线式生成一系列路径which completly cover $\mathcal{G}$所对应的区域。但是这个路径不一定是non-backtracking的，因为DSTC算法在拓展新的*sliding window*时会free<mark>(这点得加入algorithm 1中)</mark> $p_{cur}$所在的cell，这样会导致新的*sliding window*与旧的*sliding window*之间存在一个公共的cell，为了应对这种情况，我们在DSTC中设置了关于这个公共cell中路径的删除环节，但是有些情况下为了保证路径的规整性我们保留了这些重复路径，<mark>如图3(画一个不能删除重复边的情况)</mark>。<mark>(画一个算法运行中间状态的图，画在图2中的子图)</mark>。

此外，在每个规划周期，根据覆盖区域的变化(比如区域大小)DSTC会更新除已规划区域之外的$\mathcal{G}$的内容。由于并未一次性将所有区域规划完毕，DSTC能够在一定程度上应对覆盖区域$\mathcal{G}_{window}$的大小变化 instead of replanning。这一点将会在之后的mCPP问题求解过程中得到利用。


<mark>加入一个判断过程，判断$\mathcal{G}$或$\mathcal{P}_{window}$是否为空，然后按照我写的python程序的流程来修改Algoritm 1；$\mathcal{G}$包含的cell 都得令成occupied</mark>
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/算法/mcpp/dmstc.md/303884221240650.png)

## MULTIROBOT DYNAMIC SPANING-TREE COVERAGE

In this section，MDSTC is introduced：a new multirobot  coverage algorithm based on area division that 该算法能实时地调整任务分配以求最小的全局任务完成时间。MDSTC由region expansion algorithm和exchange-based adjustment algorithm组合而成。受DARP<mark>cite-DARP</mark>启发，MDSTC采用基于area division的region expansion algorithm将整个区域划分成$r$个robot-exclusive的子区域，然后各个机器人在各自的子区域执行DSTC。与此同时MDSTC将根据各个机器人即时覆盖情况，基于exchange-based adjustment algorithm对子区域进行微调以最大化多机器人系统的效能。

### Region Expansion

The certain environment 通过 the cell decomposition based method建模成一个二值map $\mathcal{M}$。In $\mathcal{M}$，the *occupied* cell 的值为-1，whereas the *free* cell的值为0。区域划分的结果用分配矩阵$A$表示，一旦一个cell被分配给一个机器人，该cell在$A$中对应的值被赋为该机器人的编号值。

The region expansion 算法works as follows:初始partition包含其对应机器人的初始位置cell，然后各个partition在$\mathcal{M}$中轮流扩张其占有区域。Partitions will stop expanding once all cells have been allocated to a robot.The region expansion算法的具体流程 is decribed in Algorithm 2. 其中$NL_k$为各个partition的adjacent cell set，由此partition $k$ 的扩张方式为在其轮次时从$NL_k$中选取一个cell $M(x, y)$并mark it as $k$ in $A(x, y)$, indicating that the cell has been 分配给机器人$k$。

通过region expansion，覆盖区域被粗略地划分成$r$个子区域，各子区域$\mathcal{L}_i$ can be computed by the assignment matrix $A$ as follows：

$$\mathcal{L_i}=\{(x,y)\in\mathcal{L}:A(x,y)=i\},~\forall i\in\{1,\cdots,r\}.$$

<mark>Figure 5</mark> illustrates an example of the region expansion algorithm, in which each partition expand around its starting cells and the adjacent cells  are marked in gray. 

![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/算法/mcpp/dmstc.md/159510116240653.png)

### Exchange-based Adjustment

通过the region expansion算法我们得到一个区域划分的粗略解以供机器人执行DSTC。在机器人运行的同时，a exchange-based adjustment algorithm is 被加入的 to 实现子区域之间的均衡。

正如之前所提到的，DSTC不会将整个区域全部规划完：DSTC会利用分配给机器人的一些*free* cells来构建覆盖路径供机器人进行覆盖工作，除此之外的 *free* cells 实际上是可调整的，在不破坏子区域连通性的前提下。假设一个如<mark>图6(两个机器人，子区域大小相等，用深色cell来代表机器人已覆盖的cell featuring two robots, subregions of equal size, with darker cells representing those covered by the robots)</mark>场景：机器人$a和b$以相同的速度在各自子区域内执行DSTC，且这两个机器人被分配到的子区域大小一致；若在某一时刻，$b$的速度由于某些原因下降了，且显著小于$a$的速度，此时倘若我们不做任何处理，让$a$和$b$依据这个完全均分的区域划分结果来进行覆盖工作，当$a$完成了其任务，而$b$却仍在进行覆盖。此时多机器人系统的覆盖工作并未完成，而$a$的工作效能是被浪费了的，或者说存在调整使得整个覆盖工作能更快的可能性。在图6中，连接$a，b$子区域的边界cell此时是*free*，如果我们将$b$的部分边界 cells单方面交换给$a$，这样由$a$和$b$组成的多机器人系统就能取得一个更快的覆盖时间。基于这种想法，the exchange-based adjustment algorithm is introduced to 均衡多机器人覆盖系统的效能。

The exchange-based adjustment algorithm的具体流程如Algorithm 3所示。其中，$C$为覆盖矩阵集，用于表示各机器人子区域内还未被遍历的区域数量；$N_k$为机器人$k$的子区域内uncovered cells 数量；$BL = \{BL_1, \cdots, BL_r\}$ is defined as the sets of connected cells for $r$ partitions, where $BL_r$ is the set of cells of partition $\mathcal{L}_r$ connected to other partition。通过计算当前的剩余任务数于期望分配之间的差值，差值最大项对应的partition作为同$b$一样的交换的付出方*start*，而差值最小项对应的partition作为同$a$一样的交换的接收方*dest*。如果*start*与*dest*是adjacent，二者可以直接通过boundary cells进行交换。若*start*与*dest*是non-adjacent，此时如要实现他们之间的交换，a search tree is needed to find a cell-exchange path between two partition. The search tree is built based on a graph in which vertices represent partitions , and the edge denotes that partitions at the end of edge are adjacent. 在确定cell-exchange path之后，*start*与*dest*便可沿着该路径进行*free* cell的交换。交换过程将循环进行，直至最大差值$|max(Dval_k)|$达到阈值或迭代次数$n_{iter}$达到上限值。

![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/算法/mcpp/dmstc.md/56994717259079.png)

### MDSTC Implementation

总结一下MDSTC解决mCPP问题的流程：首先通过region expansion algorithm将concerned area 粗略地划分成$r$个partition；之后各个机器人在各自的partition对应的子区域内执行DSTC，在线式去覆盖任务区域；与此同时，exchange-based adjustment algorithm会根据各个机器人的工作情况对任务分配进行调整；通过以上流程MDSTC将允许多机器人系统动态地、可调整地完成整个区域覆盖任务。This procedure is illustrated in 图7。

## Simulation


在本节，我们将展示一些仿真实验用于验证proposed method的有效性。所有的tests are written using Python and run on a laptop with Ubuntu 22.04, AMD Ryzen 7 7735H CPU at 3.2GHz $\times$ 8, 32G memory.

### Comparsion With DARP

我们首先在常规的mCPP场景中(多机器人系统中机器人速度恒定)测试了我们的算法。The first test 开始于一个边长为10的正方形目标区域，其中存在一些随机分布的障碍物，且机器人的初始位置也是随机生成的，as shown in Fig. 6. Utillizing the region expansion algoritm, a initial division 是被获得的，and robot 在各自的subregion中执行DSTC. stage1(如图Fig. 6-(a))为开始阶段，由于机器人的初始位置及障碍物分布，该阶段的区域划分并不是均衡的，可以看出位于右上角的机器人的子区域明显小于其余两个机器人的。然后the exchange-based adjustment algorithm介入了，通过交换边界上的free cells，使得各子区域之间到达了一个较为均衡的水平(如Fig. 6-(b))。MDSTC最终生成的路径如Fig .6-(c)所示。作为对比, the path generated by DARP with the same scenario is also illustrated in Fig. 6-(d). It should be mentioned that 在the region expansion algorithm 中是可以自定义子区域扩散的方式的。本文中使用的方式为优先扩散距离机器人初始位置近的cell。由于这个原因，the proposed menthod 计算得到的子区域是一种均匀扩散的形式。

为了对比the proposed MDSTC 和 the state-of-the-art method DARP, a series of tests is carried out in a larger terrain with a size of 49 $\times$ 49. Similar to the "outdoor" simulation set-up in \cite{darp}, obstacles are randomly assigned in this terrain, and robot also randomly select the initial positions. The path planning of 2, 4, 8, 14, 20 robot is then 以此测试，and the result are shown in Table 1.

在Table 1中，the path length is simply equal to the number of cells covered by the corresponding robot. 需要特别说明一下，其中"Max" and "Min"分别对应largest and smallest覆盖路径长度，用于表示个体机器人之间的覆盖任务量的差异；"Ideal Max"为理想的覆盖工作量for each robot, 这个值是用总的subcells的数量除以机器人个数被计算的。此外，the ratio of the largest cover path length and the ideal cover path length is reported,namly "Ratio", 用于展示每个算法对于每个场景的整体得分，越接近1越优。

比较表中两个算法的result, the "Ratio" of the proposed MDSTC method 与DARP的是相当接近的。二者都是area-division based方法，通过将concerned area等分的方式来解决mCPP问题，这样能显著地减小the "Ratio"。In summary, the proposed MDSTC can well cope various obstacle terrain and achieve 很好的表现 in view of the state of the art.

### Dynamic Imbalance Scenario  

To demonstrate the merits of the proposed MDSTC, 一个类似于图4的test scenario with dynamic imbalance is illustrated in Fig. 7。 There are two robots(R-0, R-1) trying to cover the concerned area at very beginning如图Fig 7, and by相同的覆盖速度, 通过DSTC，they have planned他们各自子区域的一部分并执行了一部分路径，他们的子区域大小是相等的， 如图Fig. 7-(a)。However, one of the robots, R-0, 他的覆盖速度减至原来的一半在运行了一段时间后。在以这种覆盖速度差异持续了一会后，DMSTC检测到了R-0和R-1剩余覆盖区域大小间的差距，并通过交换二者subregion边界上的free cells去弥补速度差异带来的效能未被充分利用。In this way,  the robot team能够互补地完成the concerned task efficiently， which is illustrated in Fig, 7-(d)。R-0 和R-1最终的coverage path length 分别为128， 244，这二者的比很接近于他们的覆盖速度比。It can be seen from this test, 对于运行时出现的机器人之间的不均衡，MDSTC能够通过实时地调整机器人之间的子区域以达到整体任务完成效率最大化的效果。

## Conclusion

In this article, a MDSTC strategy is proposed 去解决存在实时不均衡情况的mCPP问题。With the concerned area 被分割，各个机器人在各自的子区域内通过DSTC动态地执行各自的覆盖任务，与此同时，通过比较各个机器人的实时覆盖状态，各个子区域之间通过exchange base mechanism实时地调整workload distribution. Based on this, the proposed method offers a dynamic solution for complete coverage problem. 尽可能充分地利用多机器人系统的效能来加快覆盖效率。extensive numerical simulations are carried out to verify the effectiveness of this development. Results verify that the proposed MDSTC method can divide the target area 以实现更快地覆盖任务完成. 关于future work目前有几个可供探索的方向。一是DSTC算法中每个规划周期输出路径的长度有待进一步探究，这有可能能减少生成路径的重复率；此外，MDSTC在动态调整时只会涉及边界上的free cell，这限制了调整的可能性，后续可能可以考虑将已规划但未被执行的cell纳入考虑，这样能增加MDSTC的灵活性。