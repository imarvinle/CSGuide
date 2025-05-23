---
title: 如何理解计算机网络中的带宽、吞吐量与时延？
shortTitle: 带宽 VS 吞吐量 VS 时延
description:  
head:
  - - meta
    - name: keywords
      content: 带宽, 吞吐量, 时延, 网络性能, 传播时延, 传输时延, 处理时延, 排队时延, 网络拥塞, TCP协议
---

---

## 如何理解计算机网络中的带宽、吞吐量与时延？  

大家好，我是小北。

带宽、吞吐量、时延是网络中经常用来量化的指标，比如我们经常说网速好快、网络好卡、延迟好大等等。

这几个概念之间有一定关系，但是也经常容易混为一谈。

今天就让我们一起来理清这些概念。


因为网络其实和道路交通有一定相似性，所以我们就用高速公路来类比网络，这样方便理解。


## 一、带宽（Bandwidth）

**带宽指的是网络的传输能力，表示单位时间内能够传输的最大数据量。**

通常使用比特/秒（b/s）作为单位，常见的还有 Kb/s、Mb/s、Gb/s 等。


带宽呢，就像是高速公路的车道数：

* 2车道的公路（小带宽）一次能通过的车辆少
* 8车道的公路（大带宽）一次能通过的车辆多

举个例子：如果你的网络带宽是 100 Mbps，理论上每秒最多可以传输 100 兆比特的数据。

> 这里要补充下，我们日常生活经常说办的宽带是 100M、1000M，这里的单位是 bit 比特，而平常  MB/s 则是 Byte
> 而 1 Byte = 8 bit，所以 100 M 的宽带，1s 最多下载  100/8 = 12.5 MB


但是要注意，**带宽是一个理论上的最大值**，实际中很难达到这个值。

因为实际速度可能会受到网络拥塞、路由器性能等大打折扣，如同高峰期的八车道高速仍会堵车。


## 二、吞吐量（Throughput）

**吞吐量是指实际单位时间内成功传输的数据量。**

也是使用比特/秒（b/s）作为单位。

如果说带宽是高速公路的最大通行能力，那么吞吐量就是实际通过的车流量：

即使是8车道高速，如果遇到交通管制、事故，实际通过的车辆数量也会减少。

同样，即使网络带宽很大，但受到网络拥塞、服务器性能、路由器处理能力等因素影响，实际吞吐量往往小于带宽

吞吐量 ≤ 带宽，在理想情况下，吞吐量可以接近带宽，但实际中通常会小于带宽。

举个例子，大家平常用百度网盘下载的时候，刚开始速度非常快，10MB/s，但是如果你没有充值会员，速度将会逐渐降低到 几十 KB/s。

下载文件时速度从10MB/s掉到 几十 KB/s 就是吞吐量下降。

一般原因可能是：路由器过热、ISP限速、服务器过载... 

也可能是你没充值。。。。。


## 三、时延（Delay/Latency）

**时延是指数据从源主机传输到目标主机所需的时间，通常以毫秒（ms）为单位。**

比如我们用一些网络工具的时候就特别在意它的时延：

![](https://cdn.how2cs.cn/gzh/2025-04-26-083934.png)



时延并不是一个单独的指标，在《计算机网络: 自顶向下》 这本书中，提到时延由以下几部分组成：

* **1. 发送时延：主机将数据包的所有比特推向传输介质所需的时间**

计算公式：发送时延 = 数据帧长度(bit) / 发送速率(bit/s)


* **2. 传播时延：电磁信号在链路上传播所需的时间**

计算公式：传播时延 = 链路长度 / 信号传播速度
电磁波在真空中的传播速度约为3×10^8 m/s，在铜线或光纤中会略慢

* **3. 处理时延：路由器或交换机等设备处理数据包所需的时间，包括检查头部、查找路由表等**


* **4. 排队时延：数据包在网络设备缓冲区中等待被处理的时间**


> 总时延 = 发送时延 + 传播时延 + 处理时延 + 排队时延


反正就是数据包在整个网络设备中，每一步都有对应的耗时，总结起来大概是这么几块。


我们还是用高速路来类比：

| 时延类型 | 类比 | 影响因素 |
| --- | --- | --- |
| 传输时延 | 装车时间（数据发到链路上） | 数据大小/带宽 |
| 传播时延 | 卡车行驶时间 | 物理距离/介质速度 |
| 处理时延 | 分拣中心处理时间 | 路由器/交换机的性能 |
| 排队时延 | 高速收费站堵车时间 | 网络拥塞程度 |


### RTT（往返时延）

RTT（Round-Trip Time）是指数据包从源主机发送到目标主机，再返回到源主机所需的总时间。

因为日常生成中打开网页、视频通话等应用都是给对方发送请求，然后服务器处理后返回响应，所以 RTT 在实际中应用更加广泛。

比如我们经常用ping命令测试的就是RTT。

### 延迟带宽积（BDP）

为了后面更好理解这三者的关系，这里再引入一个**延迟带宽积**的概念

延迟带宽积（Bandwidth-Delay Product，简称BDP），顾名思义，是指链路的传播时延与带宽的乘积。


那这个指标到底是啥意思呢？ 

其实很好理解，想象一条横跨大洋的光缆：

带宽：光缆的粗细（每秒能传输多少数据）

往返时延（RTT）：数据从一端到另一端再回来的时间

延迟带宽积（BDP）：BDP = 带宽 × RTT也就是这条光缆能同时容纳多少 **“在途中的数据”**

所以 带宽 × 时延也就等于网络管道的「容积」。

就好像水管总容量 = 水流速度（升/秒） × 水流循环时间（秒）

若水流速度是10升/秒，水流循环一周需5秒 → 水管总容量 = 50升

那这个有啥实际应用呢？

**传输窗口设计：**


在TCP协议中，发送窗口其实就是允许最大未确认（在途）的数据包，那么这个窗口也应该至少等于延迟带宽积。

否则，如果窗口小于延迟带宽积，会造成带宽浪费（在途数据包填不满链路，发送方就暂停发送了）

而如果窗口过大，则可能导致网络拥塞。



## 四、这些指标之间的关系

1. **带宽与吞吐量**  

* 高带宽为吞吐量提供了上限保证，提高带宽可能会提升吞吐量，但不一定成正比，因为实际吞吐量受时延和丢包制约。

2 **带宽与时延**

* 带宽的增加不一定会减少时延（比如深圳开车回四川，在都不堵车的情况下，你即便是把高速路扩容到 20 车道，也不会降低回家的耗时）
* 对于小数据包，时延主要受传播时延和处理时延影响，与带宽关系不大（毕竟你都填不满网络）
* 对于大文件传输，带宽越大，发送时延越小（可以想象高速路收费站，入口越多，把同样的车辆送上高速路也就越快）

3. **时延与吞吐量**

* 在某些协议下，较高的时延可能导致吞吐量下降
* 例如在 TCP 协议中，较高的 RTT 会降低传输效率


## 总结

用图片来总结下：

![](https://cdn.how2cs.cn/gzh/2025-04-26-network-metrics-diagram.svg)