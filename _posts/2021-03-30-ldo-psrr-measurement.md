---
layout:     post
title:      "怎样测量LDO的电源纹波抑制比（PSRR）"
subtitle:   " PSRR的测量原理及方法 "
date:       2021-03-30
author:     "huanqing"
header-img: "img/post-bg-LT3042.jpg"
mathjax: true
catalog: true
tags:
    - 电源
    - 模拟电路
---

## 电源抑制比（PSRR）的测量原理及解决方法

PSRR（Power supply rejection ratio）又称电源抑制比，是衡量电路对于输入电源中纹波抑制大小的重要参数，表示为输出纹波和输入纹波的对数比，单位为分贝（dB）[1]，其计算公式为:

<center> $ { PSRR } = 20 \lg \frac {  { Ripple } _ {  {input } } } {  { Ripple } _ {  {output } } } $ </center>

式中

${ Ripple } _ {  {input } }$ ：输入电压中纹波峰峰值

${ Ripple } _ {  {output } }$ ：输出电压中纹波峰峰值

从公式中可以看出PSRR越大，相同输入纹波在输出端的纹波越小，对于纹波有较高要求的射频和无线应用中，需要选用高PSRR的LDO。那么LDO的PSRR该如何测量呢？本文总结了各种测量方法。



### PSRR测量原理

![img](https://gitee.com/hawkingwu/PicGo/raw/master/2018_2D00_02_2D00_28_5F00_134043.png)

在LDO输入的直流电压 ${ V} _ {  {in\_DC} }$ 中叠加一定频率且峰峰值为 ${ Ripple } _ {  {input } }$ 的交流电压 ${ V} _ {  {in\_AC} }$（交流电压峰峰值一般为数百毫伏），然后测量LDO输出电压中 ${ V} _ {  {out\_DC} }$ 的交流电压 ${ V} _ {  {out\_AC} }$ 峰峰值 ${ Ripple } _ {  {output } }$ ，最后利用公式1计算出在该频率下的PSRR。

**LDO的输入电压在测试中需要满足以下条件：**

1. 输入电压最大值不能超过LDO的最大工作电压。

2. 输入电压最小值大于LDO输出电压与压降之和。


**PSRR测量原理十分简单，但是在实际测量的过程中却发现并不容易，主要体现为：**

1. 如何在直流电压中叠加交流电压呢？具有偏置电压功能的信号发生器好像可以满足要求，但是信号发生器最大输出电流一般为数十毫安，如果要测量输出为150mA的LP5907便无法满足要求。
2. 如何测量LDO输出电压中交流电压峰峰值呢？一般的示波器只能测量到毫伏级电压，当LDO的PSRR为60dB时，输出纹波通常小于1mV，示波器就无法准确测量了。

针对以上两个问题，本文将介绍相应的解决方法。



### 输入直流电压叠加交流电压
#### 1. 输入注入器

使用专业的输入注入器，比如J2120A，带宽10Hz-10MHz，直流电压最大值为50V，输出电流可达5A，配合网络分析仪分别测量LDO输入和输出的交流电压，利用软件绘制出LDO在设定频率范围内的PSRR。

![图1 输入注入器和网络分析仪测试PSRR](https://gitee.com/hawkingwu/PicGo/raw/master/2018_2D00_02_2D00_28_5F00_134111.png)

<center> 图1. 输入注入器和网络分析仪测试PSRR </center>



#### 2. 加法运放电路

使用运算放大器设计加法电路，将直流电压和交流电压叠加在输出端。运放的选择需要满足以下几个基本条件：

   1) 运放的带宽满足LDO测试范围。

   2) 运放的最大输出电流不小于LDO最大输出电流。

   3) 运放的输出电压范围覆盖LDO的输入电压范围。

TI满足以上要求的运算放大器有许多，比如OPA552、OPA564、THS3120等，加法电路图如图2所示（R1=R2），该电路的最低截止频率由C1和R1所决定[2]，最高截止频率由运放的带宽所决定。

![图2 加法运放电路](https://gitee.com/hawkingwu/PicGo/raw/master/2018_2D00_02_2D00_28_5F00_134142.png)

<center> 图2. 加法运放电路 </center>

如果信号发生器直流偏置电压最大值满足测量需求，也可以将运算放大器设计为电压跟随器。用该方法在测量PSRR时要去掉LDO的输入电容，避免运算放大器不稳定。



#### 3. LC节点法

利用电感和电容实现直流电压和交流电压叠加的方法如图3所示，该电路的最高频率由L1和C1所决定，最低频率由C1所决定。

![img](https://gitee.com/hawkingwu/PicGo/raw/master/2018_2D00_02_2D00_28_5F00_134157.png)

<center> 图3. LC节点法 </center>



### LDO输出交流电压测量

#### 1. 示波器测量

对于一般的示波器可以测量到毫伏级电压，当LDO的PSRR不高于40dB~50dB时，如果输入交流电压峰峰值为1V，那么LDO输出中的同频率交流电压峰峰值为3mV~10mV，可以用示波器直接测量。

#### 2. 放大器和示波器测量

当LDO的PSRR大于50dB时，由于输出纹波幅值通常小于1mV，无法利用示波器直接测量。这时可以考虑使用运算放大器将LDO输出交流电压放大100倍甚至更高，在设计运放时需要考虑:

   1) LDO输出有直流电压，电路需要将直流电压去掉。

   2) 放大电路自身产生的噪声要远远小于放大后交流电压。

   3) 运放输入失调电压不能太大，否则经放大电路放大后会输出很大的直流电压。

   4) 放大电路的带宽满足LDO的PSRR测量频率范围。

因此在设计时可以选择低噪声、低输入失调电压和高带宽的运算放大器，比如OPA211、OPA228、OPA189等。放大电路如图4所示，该电路的最低截止频率由C1和R1所决定，最高截止频率由运放的带宽所决定。

![img](https://gitee.com/hawkingwu/PicGo/raw/master/2018_2D00_02_2D00_28_5F00_134208.png)

<center> 图4. 放大电路 </center>

#### 3. 频谱分析仪测量

频谱分析仪可以测量微伏级电压信号，可以配合使用高阻抗输入探头来测量LDO输出交流电压。但是频谱分析仪高阻抗输入探头通常比较昂贵，一般实验室并没有配备，这时可以考虑用运算放大器搭建一个高输入阻抗探头，可参考Steve Hageman在扩展射频频谱分析仪可用范围的高阻抗FET探头[3]中提到的电路，如图5所示，运算放大器可以选用OPA656。

![img](https://gitee.com/hawkingwu/PicGo/raw/master/2018_2D00_02_2D00_28_5F00_134232.png)

<center>  图5. 高阻探头电路 </center>



### PSRR测量

本次测量的LDO是TPS7A4901，将TPS7A4901EVM的输出电压重新设计为1.2V，输出电容改为10uF。采用THS3120作为直流电压和交流电压叠加电路，利用THS3120EVM并将其改为图6所示的电路。选用OPA211设计为图7所示的100倍放大电路。

![img](https://gitee.com/hawkingwu/PicGo/raw/master/2018_2D00_02_2D00_28_5F00_134243.png)

<center>  图6. THS3120直流电压和交流电压叠加电路 </center>

![img](https://gitee.com/hawkingwu/PicGo/raw/master/2018_2D00_02_2D00_28_5F00_135453.png)

<center>  图7. OPA211放大电路 </center>

THS3120和OPA211供电电压为±15V，THS3120直流电压为3.2V，交流正弦电压为1kHz且峰峰值为1V。 TPS7A4901输出电流为150mA，NR/SS脚电容和前馈电容未接。图8为LDO放大后输出纹波和输入纹波，图9是TPS7A4901放大后输出纹波FFT变换。

![img](https://gitee.com/hawkingwu/PicGo/raw/master/2018_2D00_02_2D00_28_5F00_134309.png)

<center>  图8. LDO放大后输出纹波（黄线）和输入纹波（蓝线） </center>

![img](https://gitee.com/hawkingwu/PicGo/raw/master/2018_2D00_02_2D00_28_5F00_134320.png)

<center>  图9. TPS7A4901放大后输出纹波FFT变换 </center>

从图9中可以得出在1kHz时的输出纹波幅值为-26.46dbV，换算成未放大LDO输出电压中1kHz纹波峰峰值为0.95mV，利用公式1可得出PSRR为60.4dB，与datasheet中62dB较为接近，改变交流电压频率还可以测量在不同频率下的PSRR。

如果使用输入注入器和网络分析仪可以方便得测量出LDO在设定频率范围的PSRR曲线。如果没有输入注入器和网络分析仪，可以选择上诉所列举的输入和输出的一种组合，然后设定一个频率，测量输入输出电压中交流电压幅值 和 ，利用公式1得出PSRR，然后改变输入交流信号频率重复测量，最终得到整个频率范围内的PSRR曲线。



相关文献：

[LDO PSRR Measurement Simplified (Rev. A)](https://www.ti.com/lit/an/slaa414a/slaa414a.pdf)
