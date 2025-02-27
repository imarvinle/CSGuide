---
title: 计算机各种访问操作的耗时对比数据
description: 计算机各种访问操作的耗时对比数据，包括CPU缓存、主内存、硬盘、网络等，帮助理解系统各种操作的基本成本，优化程序性能。
head:
  - - meta
    - name: keywords
      content: 计算机访问操作、耗时对比、CPU缓存、主内存、硬盘、网络
---

## 为什么我们需要了解不同操作的访问耗时呢？

因为通过这些数据，我们可以了解系统各种操作的基本成本，理解这些底层细节可以更好地预测和优化程序的性能，甚至直接影响到我们系统设计。

比如，访问主内存的成本要远高于从CPU缓存中读取数据，所以在极度追求性能的场景下，利用空间局部性来优化缓存使用，可以大幅提高程序性能。

对于平常分布式系统的设计，了解网络延迟和数据中心间的通信成本也有助于我们并权衡数据分布、复制和分区策略的取舍。

这些延迟数据有助于理解存储系统的性能差异，了解SSD和传统硬盘的访问速度差别，可以在设计存储系统或选择存储硬件时做出更好的决策。


本文主要数据来自于 Google [Jeff Dean](http://research.google.com/people/jeff/) 的一次演讲:
[Building Scalable Web Applications with Google App Engine
](https://sites.google.com/site/io/building-scalable-web-applications-with-google-app-engine)

Github 有 https://gist.github.com/jboner/2841832 人整理了具体的数值。

**我把它翻译为了中文，并用表格形式展现：**

## 中文版本

### 延迟比较数值 (~2012)

| 英文名称 | 中文名称 | 延迟时间 | 对比 |
|---|---|---|---|
| L1 cache reference | L1缓存引用 | 0.5 ns |  |
| Branch mispredict | 分支预测错误 | 5 ns |  |
| L2 cache reference | L2缓存引用 | 7 ns | 14x L1缓存 |
| Mutex lock/unlock | 互斥锁定/解锁 | 25 ns |  |
| Main memory reference | 主内存引用 | 100 ns | 20x L2缓存, 200x L1缓存 |
| Compress 1K bytes with Zippy | 使用Zippy压缩1K字节 | 3,000 ns (3 us) |  |
| Send 1K bytes over 1 Gbps network | 通过1 Gbps网络发送1K字节 | 10,000 ns (10 us) |  |
| Read 4K randomly from SSD | 随机从SSD读取4K | 150,000 ns (150 us) | ~1GB/sec SSD |
| Read 1 MB sequentially from memory | 从内存连续读取1MB | 250,000 ns (250 us) |  |
| Round trip within same datacenter | 数据中心内的往返 | 500,000 ns (500 us) |  |
| Read 1 MB sequentially from SSD | 从SSD连续读取1MB | 1,000,000 ns (1 ms) | ~1GB/sec SSD, 4X memory |
| Disk seek | 磁盘寻道 | 10,000,000 ns (10 ms) | 20x 数据中心往返 |
| Read 1 MB sequentially from disk | 从磁盘连续读取1MB | 20,000,000 ns (20 ms) | 80x 内存, 20X SSD |
| Send packet CA->Netherlands->CA | 发送数据包 CA->荷兰->CA | 150,000,000 ns (150 ms) |  |

### 注意
-----
1 ns = 10^-9 seconds

1 us = 10^-6 seconds = 1,000 ns

1 ms = 10^-3 seconds = 1,000 us = 1,000,000 ns

### 延迟对比图

![延迟对比图](https://cdn.how2cs.cn/csguide/005620.jpg)

## 英文版本

### Latency Comparison Numbers (~2012)
----------------------------------
L1 cache reference                           0.5 ns

Branch mispredict                            5   ns

L2 cache reference                           7   ns                      14x L1 cache

Mutex lock/unlock                           25   ns

Main memory reference                      100   ns                      20x L2 cache, 200x L1 cache

Compress 1K bytes with Zippy             3,000   ns        3 us

Send 1K bytes over 1 Gbps network       10,000   ns       10 us

Read 4K randomly from SSD*             150,000   ns      150 us          ~1GB/sec SSD

Read 1 MB sequentially from memory     250,000   ns      250 us

Round trip within same datacenter      500,000   ns      500 us

Read 1 MB sequentially from SSD*     1,000,000   ns    1,000 us    1 ms  ~1GB/sec SSD, 4X memory

Disk seek                           10,000,000   ns   10,000 us   10 ms  20x datacenter 
roundtrip

Read 1 MB sequentially from disk    20,000,000   ns   20,000 us   20 ms  80x memory, 20X SSD

Send packet CA->Netherlands->CA    150,000,000   ns  150,000 us  150 


### Notes
-----
1 ns = 10^-9 seconds

1 us = 10^-6 seconds = 1,000 ns

1 ms = 10^-3 seconds = 1,000 us = 1,000,000 ns

### Credit
------
By Jeff Dean:               
Originally by Peter Norvig: http://norvig.com/21-days.html#answers

Contributions
-------------
'Humanized' comparison:  https://gist.github.com/hellerbarde/2843375

Visual comparison chart: http://i.imgur.com/k0t1e.png


## 参考

* [https://gist.github.com/jboner/2841832](https://gist.github.com/jboner/2841832)
