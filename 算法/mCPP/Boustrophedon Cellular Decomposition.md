# Boustrophedon Cellular Decomposition

## CPP

![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/算法/mcpp/boustrophedon%20cellular%20decomposition.md/324113616265283.png =500x)

## trapezoidal decomposition approach

扫描线：slice

polygonal obstacles

decomposition: free configuration space ——> trapezoidal cells

分解过程：cells通过一系列$open$和$close$过程来形成。

$event$:

1. $IN$: old cell完成构建，两个new cell开始构建；

![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/算法/mcpp/boustrophedon%20cellular%20decomposition.md/135660716259988.png =500x)

2. $OUT$: 两个old cell完成构建，一个new cell开始构建；

![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/算法/mcpp/boustrophedon%20cellular%20decomposition.md/37220916256543.png =500x)

3. $MIDDLE$:在$IN$和$OUT$两个过程中，按顶点作为分界将cell分割。

![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/算法/mcpp/boustrophedon%20cellular%20decomposition.md/136402016252297.png =500x)


## boustrophedon cellular decomposition

![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/算法/mcpp/boustrophedon%20cellular%20decomposition.md/96042416270177.png =500x)

![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/算法/mcpp/boustrophedon%20cellular%20decomposition.md/9843216267781.png =500x)







