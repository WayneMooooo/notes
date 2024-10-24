# Pure Pursuit

**基于几何模型的方法**

## 算法主要内容

基于当前车辆后轮中心点$A$的位置，在参考路径$path$上以预瞄距离$l_d$匹配一个预瞄点$C$，之后根据$A, C$ 的位置确定旋转中心$O$，以其确定的一个半径为$R~$的圆弧段作为后轮跟踪到点$C~$的路径，也就是一种定圆运动。为了实现跟踪点$C~$的定圆运动，需要保持车辆的转弯半径始终为R，由此进一步可以确定前轮转向角$\delta_f$。

![](https://raw.githubusercontent.com/WayneMooooo/notes/main/学习笔记/算法/自动驾驶/pure%20pursuit.md/374903313240861.png =482x)

> 算法目标：为了得到一个关于控制量——前轮转角$\delta_f$的控制律

**具体计算过程：**

* $\angle AOC = \pi - 2\angle COA = \pi - 2(\frac{\pi}{2}-\alpha) = 2\alpha$

* 在$\triangle AOC$中，根据正弦定理有：
$$\frac{l_d}{\sin 2\alpha}=\frac{R}{\sin(\frac{\pi}{2} - \alpha)}$$

由此得到$R=\frac{l_d}{2sin{\alpha}}$
* 得到R后，根据Ackermann Steering中前轮转角的特性$\delta_f = \tan^{-1}(\frac{L}{R}) \cong \frac{L}{R}$，可以得到前轮转角$\delta_f$的控制律：
$$\delta_f(t)=\arctan(\frac{2L\sin\alpha(t)}{l_d})$$
其中预瞄距离$l_d = kv + l_0$，为一个根据速度的自适应值。

**进一步讨论：**

* 此处定义<mark>横向误差</mark>为预瞄点到车辆的距离：$e_y=l_d\sin\alpha$，联立此式与$\delta_f$的控制律式，可以得到：
$$e_y = \frac{l_d^2}{2L}\tan\delta_f\approx\frac{l_d^2}{2L}\delta_f$$
由此可看出Pure Pusuit算法近似一个P控制器，比例系数由预瞄距离决定。

**算法优缺点：**
* 优点：
1. 简单。容易设计与理解。
2. 相应比较迅速。
* 缺点：
1. 对于预瞄距离的敏感性：预瞄距离对算法性能的影响很大，过大容易导致反应迟钝，过短容易引起过于敏感和振荡。
2. 对于轨迹的要求比较严格：要求轨迹的连续性好，对于轨迹的依赖性很强，在路径转弯较多或者曲率变化频繁的情况下，该算法不能保证平滑的行驶效果，可能会出现突然的变化。

**mwm' comments:**

Pure Pursuit算法是一种用圆弧轨迹来跟踪一个参考点的算法，圆弧根据预瞄距离(作为圆弧对应的割线)、以及由车辆当前位置与预瞄点确定的转弯半径二值确定。下面开始纯主观评价：首先我会很好奇这个算法在实际实施时控制命令的下发频率是怎样的，或者说我很好奇预瞄点在一个控制周期内是不是需要让车辆到达，然后才会进入下一个周期；由于是用定圆运动来规划到预瞄点的路径，这样就会导致这个算法在参考轨迹曲率变化率比较平滑时才会有比较好的表现，如果像存在折线的路径大概效果不会很好吧，但仔细想想，车辆的轨迹大概也不会有折线这样的存在(想想也是比较危险的驾驶行为)，像Dubins或者ReedsSheep这种轨迹，在低速的情况下，Pure Pursuit算法大概也能效果不错吧。

**后记**
剩余的学习内容：
1. 基于原始的Pure Pursuit算法的改进算法研究，当然是看别人对其的研究= =；
2. 算法在工程实现上是不是技巧呢？可以去看看别人写的代码。
