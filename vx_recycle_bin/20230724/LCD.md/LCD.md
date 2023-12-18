# LCD
> **Loop Closure Detection** originally being introduced as “the revisiting problem,” it concerns the robot’s ability to recognize whether the sensory data just captured matches with any already collected, i.e., a previously visited area, aiming for SLAM to revise its position
## Foundation of Loop Closure Detection
**早期手段：** 测量轴承转数，测距仪
**当前方向：** 利用图像，更具区分度；大范围场景的LCD技术已经很成熟，当前的研究重点是识别变化大且复杂的场景（比如光照变化，季节改变）。
**步骤**： 提取特征；计算相似性：similarity threshold，consistency checks
**研究目标：**
* 更好的鲁棒性：能够在大范围场景以及视角变化中完成LCD任务。
* 对场景更深刻的理解能力：超越基本的手工方法，得到对于场景的高层次理解和语义信息。
* 更好的数据管理：剔除不相关的感知数据；复杂程度适应当前任务的环境表达方式。
## Structure Of A Visual Loop Closure Detection System
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/lcd.md/6045715230546.png =485x)
