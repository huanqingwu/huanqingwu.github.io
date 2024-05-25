---
layout:     post
title:      "ADI的精密半波整流电路"
subtitle:   "  "
date:       2024-05-22
author:     "huanqing"
header-img: "img/post-bg-half-wave-rectifier.jpg"
mathjax: true
catalog: true
tags:
    - 模拟电路
    - 理想二极管

---

## 电源抑制比（PSRR）的测量原理及解决方法

​	半波整流器是电子电路中一种基本的整流器件，该器件仅允许交流信号的半个周期（正半周或负半周）通过，而另一半阻止。提到整流器件，大部分工程师和学生第一个想到的一定是二极管。确实二极管是应用最多的整流器件，但在一些特殊领域，如需要对低于1V的信号进行整流，单个二极管由于无法避免的正向压降往往不能满足要求。

如果你在百度搜索“理想二极管电路”或“精密整流电路”，大概率会获得形如下图的电路：

<img src="https://gitee.com/hawkingwu/PicGo/raw/master/20240525100800.png" style="zoom: 50%;" />

​	此电路在低频或运放压摆率非常高的情况下可以实现理想二极管的功能，在高频下的问题我们后面再讨论。先来看一下ADI是如何实现半波整流的：

<img src="https://gitee.com/hawkingwu/PicGo/raw/master/20240525100852.png" style="zoom: 67%;" />

​	运放U3是我加的1倍反向放大电路，其余部分摘自ADI小型指南 MT-212 。两个电路有怎样的区别？ADI为什么要用更多的器件？先看一下仿真输出的波形：

<img src="https://mmbiz.qpic.cn/sz_mmbiz_png/ibePMJOV3W1kvT8BjV511NktWTn2LgfhWsaQLUACm2VFE2pxQKuRyIa0f81geq3fHRRWB6YHjoCNtC6xLuAlW3A/640?wx_fmt=png&from=appmsg&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1" alt="图片" style="zoom:67%;" />

​	蓝色输入信号为20kHz正弦波，似乎红色Vout1在交流信号过零处有一些“反应不过来”，而绿色的Vout2则好很多。

​	再来观察一下运放的输出端波形：

<img src="https://gitee.com/hawkingwu/PicGo/raw/master/20240525100956.png" style="zoom:67%;" />

​	看到这里应该破案了吧，回到ADI的经典电路：

<img src="https://gitee.com/hawkingwu/PicGo/raw/master/20240525101022.png" style="zoom: 67%;" />

​	对于正输入电压，运放会输出负电压，D2正向导通，运放只要输出约-0.7V的电压就能使Vin- = Vin+；当信号输入为负电压时，运放输出正电压，D2截止D1导通，就成了经典的反向放大电路。

​	由于输出极性与输入相反，所以我在输入端加了一个反相器。
