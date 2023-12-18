# 非线性project
**Buck的拓扑结构**
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E9%9D%9E%E7%BA%BF%E6%80%A7project.md/31625114248995.png)
## 占空比选择
开关导通时，电感两端电压为$V_i-V_o$；开关断开时，电感两端电压为$V_d+V_o$。
电路稳定后，为了保证负载电流稳定，(伏秒平衡)一个周期内电感电流增大量等于减小量。因为$U=L\frac{di}{dt},~\frac{di}{dt}=\frac{U}{L}$,L不变，所以电感电流变化速度与电压成正比。
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E9%9D%9E%E7%BA%BF%E6%80%A7project.md/339970315257028.png =540x)
电感变化斜率与电压成正比，电感电流上升的高度与下降高度相同，故电感电流的上升时间和电压成反比，从而有：
$$\frac{T_{on}}{T_{off}}=\frac{(V_o+V_d)}{(V_i-V_o)}$$
再根据$T=T_{on}+T_{off}=\frac{1}{f}$，可计算得到导通时间，关断时间，占空比如下：
导通时间： $T_{on}=\frac{(V_o+V_d)}{(V_i+V_d)}*\frac{1}{f}$
关断时间： $T_{off}=\frac{(V_i-V_o)}{(V_i+V_d)}*\frac{1}{f}$
占空比：$D=\frac{T_{on}}{T_{off}}=\frac{(V_o+V_d)}{(V_i+V_d)}$
如果是同步buck，则$V_d=0$，则公式变为如下：
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E9%9D%9E%E7%BA%BF%E6%80%A7project.md/529982015246252.png =540x)
## 功率电感选择
电感选型参数：**电感感量**和**电感电流**。
电感电流分为**平均电流$I_L$**和**纹波电流$\Delta I_L$**。
**对于平均电流**：显然输出电压$V_o$基本不变，也就是输出滤波电容两端电压没有变化，故电容的平均电流为0，根据输出节点的基尔霍夫电流定律，节点电流和为0，因此电感的平均电流就等于负载的平均电流$I_o$，即$I_L=I_o=\frac{V_o}{R}$。
**对于纹波电流**：电感电流就是个三角波，在开关导通时电感电流增大，在关断时，电感电流减小。纹波电流的大小等于开关导通时电感电流增大的值，也等于关断时电感电流减小的值。开关导通时，电感两端电压为$U=V_i-V_o$，根据$U=L \frac{di}{dt}$可以求出电感电流纹波$\Delta I_L=d_i=\frac{U}{L}*T_{on}$。
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E9%9D%9E%E7%BA%BF%E6%80%A7project.md/57083815259886.png =720x)
同时，可以得到电感的峰值电流为$I_{LP}=I_L+\frac{\Delta I_L}{2}$。
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E9%9D%9E%E7%BA%BF%E6%80%A7project.md/65464015257490.png =540x)
**对于电感感量：**在电感选型时，电感的饱和电流必须大于$I_{LP}$，并且要保留一定裕量，因此$\Delta I_L$应该是$I_L$的20%~40%为宜，即：$\Delta I_L=（0.2 \sim 0.4)*I_L$，根据这个范围，可以求得电感值范围为：
$$L=\frac{V_{o}+V_{d}}{f *(0.2 \sim 0.4) * I_{o}} * \frac{V_{i}-V_{o}}{V_{i}+V_{d}}$$
## 输出滤波电容选择
关注输出纹波$\Delta V_o$的大小，由输出电容容量和ESR决定。
**电容电荷量变化引起的$U_q$**：对于输出节点，该节点的电流由3个，一个来自负载，它可以看作是恒定的，为$I_o=\frac{V_o}{R_L}$，一个节点是输出滤波电容，另外一个节点是电感。根据基尔霍夫电流定律，节点电流和为0，并且负载的电流恒定，那么电感电流的变化量必然等于电容电流的变化量，因为最终3者的和为0。因为电感的纹波电流是$\Delta I_L$，那么电容的纹波电流也是$\Delta I_L$，又因为电容的平均电流为0，所以电容的充电电流和放电电流都是$\frac{\Delta I_L}{2}$。由此可算的电容的充电电荷量$Q=\frac{1}{2}*\frac{T}{2}*\frac{\Delta I_L}{2}=\frac{\Delta I_L}{8*f}=C_o*U_q$，进而由电容电荷两变化引起的$U_q=\frac{V_o+V_d}{8*f^2*C_o*L}*\frac{V_i-V_o}{V_i+V_d}$。
**电流流过电容的ESR造成的压降Uesr**：$U_{esr}=\frac{\Delta I_L}{2}*ESR-(-\frac{\Delta I_L}{2}*ESR)=\Delta I_L*ESR=\frac{V_{o}+V_{d}}{f * L} * \frac{V_{i}-V_{o}}{V_{i}+V_{d}} * E S R$
根据以上两点，可得总的输出电压纹波大小$\Delta V_o=\frac{V_{o}+V_{d}}{f * L} * \frac{V_{i}-V_{o}}{V_{i}+V_{d}} * (\frac{1}{8*f*C_o}+E S R)$，由此可以进行输出滤波电容的选择。
**公式汇总：**
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E9%9D%9E%E7%BA%BF%E6%80%A7project.md/533981816254992.png =540x)
**BUCK的CCM模式传递函数求解：**
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E9%9D%9E%E7%BA%BF%E6%80%A7project.md/580645216236233.png =426x)
二端口等效：将MOS管和二极管部分（上图中阴影部分）看作一个整体，及一个二端口网络，电路变为如下：
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E9%9D%9E%E7%BA%BF%E6%80%A7project.md/137995416253809.png =430x)
对$i_1(t)，i_2(t)，v_1(t)，v_2(t)$取开关周期的平均值，分别为：$I_1(t)，I_2(t)，V_1(t)，V_2(t)$，它们都是时间的函数。那么电路就变成了下面这样：
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E9%9D%9E%E7%BA%BF%E6%80%A7project.md/347485616247355.png =443x)
当没有任何干扰信号时，$I_1(t)，I_2(t)，V_1(t)，V_2(t)$周期平均值和全时间平均值是一样的，令这些值为$I_1，I_2，V_1，V_2$，它们是常量，此时的开关信号的占空比也是恒定的，用$D$表示。当存在干扰信号时，干扰信号会引起I1(t)，I2(t)，V1(t)，V2(t)随时间小范围变化，它们分别以I1，I2，V1，V2为中心进行波动，同理，占空比也会围绕D为中心进行波动，分别用符号$\Delta I1(t)，\Delta I2(t)，\Delta V1(t)，\Delta V2(t)，\Delta D(t)$来表示这些波动量。
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E9%9D%9E%E7%BA%BF%E6%80%A7project.md/35830417240489.png =417x)
下面来推导这五个量之间的关系。
首先，$V_2(t)=V_1(t)*D(t)$，因为在MOS不导通的时候，那么二极管导通，v2(t)为0，而在MOS导通的时候，v2(t)等于v1(t)。同理，$I_1(t)= I_2(t)*D(t)$，因为MOS在不导通的时候，i1(t)为0，而在MOS管导通的时候，i1(t)等于i2(t)，所以，i1(t)在周期内的平均值I1(t) 就等于导通时间的百分占比乘以I2(t)。将这两个式子代入上述式中可得到小信号的表达式：
$$\begin{aligned}
\Delta I_{1}(t) & =D * \Delta I_{2}(t)+I_{2} * \Delta D(t)+\Delta D(t) * \Delta I_{2}(t) \\
\Delta V_{2}(t) & =D * \Delta V_{1}(t)+\Delta D(t) * V_{1}+\Delta D(t) * \Delta V_{1}(t)
\end{aligned}$$
忽略高阶微小量，进而可得：
$$\begin{aligned}
\Delta I_{1}(t) & =D * \Delta I_{2}(t)+I_{2} * \Delta D(t)\\
\Delta V_{2}(t) & =D * \Delta V_{1}(t)+\Delta D(t) * V_{1}
\end{aligned}$$
目标为求解Gvd，也就是$\Delta V_o/\Delta D$的值，由上式已知$\Delta V_2$与$\Delta D$的关系，下面求解$\Delta V_2$与$\Delta V_o$的关系。
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E9%9D%9E%E7%BA%BF%E6%80%A7project.md/360261417231019.png =443x)
理想情况下，$V_i$是恒定的，$V_1(t)$也是恒定不变的，因此$\Delta V_1(t)=0$，上式可变为：
$$\Delta V_2(t)=\Delta D(t)*V_i$$
由分压关系，可得：
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E9%9D%9E%E7%BA%BF%E6%80%A7project.md/342961817235355.png =443x)
在结合前面得到的式子$\Delta V_2(t)=\Delta D(t)*V_i$，可得buck开关变换器的传递函数：
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E9%9D%9E%E7%BA%BF%E6%80%A7project.md/297771917235964.png =443x)
## 整个电路的开环传递函数
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E9%9D%9E%E7%BA%BF%E6%80%A7project.md/458922717234430.png)
![](https://raw.githubusercontent.com/WayneMooooo/notes/main/%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/%E9%9D%9E%E7%BA%BF%E6%80%A7project.md/312372717245132.png)
