# ORCA
## 问题描述
用圆形表示机器人
其可被观测的属性：设机器人A半径为$r_A$；机器人位置$P_A$；当前速度$v_A$。
其不可被观测的属性：最大速度$v^{max}$；首选速度$V_{pref}$（大小等于最大速度，方向指向目标位置）。
对于一组具备上述属性的机器人，求其新速度$v^{max}$，使得：
* 保证所有机器人在时间$\tau$内互不碰撞；
* 新速度尽可能的接近$V^{pref}$。

## 速度障碍区$VO^{\tau}_{A|B}$（Velocity Obstacle）
对于机器人A,B
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/orca.md/272251322230748.png)
对于A,B，速度障碍区$VO^{\tau}_{A|B}$定义：A在此区域中选择此速度时，将在时间$\tau$内与B发生碰撞。