---
title: 网络指北介绍(图解版， 正在陆续发布中)
description: 系统、深入浅出、全面地梳理计算机网络核心知识，帮助程序员快速备战面试。涵盖网络基础概念、物理层、数据链路层、网络层、传输层等重要内容。
head:
  - - meta
    - name: keywords
      content: 计算机网络, 面试, TCP/IP, OSI模型, 网络协议, 三次握手, 四次挥手, HTTP, HTTPS, 网络安全
---


## 为什么要学计网，计网如何快速准备面试？

大家好，我是小北。

《网络指北-图解版》是一份专门为程序员准备的计算机网络教程，它有这些特点：

1. **面试导向**：每个知识点都标记了面试频率，高频考点单独成章。
2. **图解为主**：大量手绘图结合文字描述，帮大家更好理解。
3. **兼顾系统**：面试导向，并不意味着不注重系统性。相反，这份教程会带你从整体到细节，全面掌握计网最核心的知识。
4. **通俗易懂**：用通俗易懂的语言，把复杂难懂的网络知识讲清楚。

说实话，我大一第一次学计网时完全是懵逼的：七层模型、四层协议栈、各种神秘缩写（TCP/UDP/HTTP/IP...）简直像看天书。

教材上密密麻麻的文字和复杂图表让我直接劝退，还有那些看起来永远学不完的协议细节...

然而，残酷的现实是：**不会计网，你连面试都很难过**。无论前端、后端还是运维，计网都是绕不开的知识点。

所以我花了几个月时间，把计网的知识重新梳理了一遍，**并结合自己的面试经验，以及最近两年在鹅厂当面试官的考察经验**，整理出这套《网络指北：图解版》。

(PS： 当前正在陆续发布中， 预计 2025.3 月底前完成)

## 《网络指北-图解版》适合谁？

- **计算机专业学生**：课程学过计网，通过本教程，快速掌握计网核心
- **转行自学者**：快速掌握计网核心，避开冗长的教科书
- **面试备战者**：直击高频考点，掌握答题技巧

## 如何学习这份教程？

这份教程共分四大模块：基础概念、核心协议、高频考点和进阶实战。

建议这样学：

1. **基础薄弱的同学**：按顺序学习，打好基础再深入
2. **赶时间面试的**：直接跳到"高频考点模块"，抓紧背八股文
3. **已有基础想突破的**：重点学习"核心协议深度模块"中的 TCP 和 HTTP 部分
4. **实战能力要提升的**：结合"进阶实战模块"的代码案例动手实践

每个知识点我都标注了重要程度（⭐⭐⭐表示面试必考），建议集中精力先掌握这些。


## 学完《网络指北》你能得到什么？

你也许能够：
- **自信回答 90% 的计网面试题**
- **真的搞懂网络是如何连接的**，而不是背了一堆的协议
- 看懂 Wireshark 抓包内容，分析网络流量
- 具备一定网络编程能力

最重要的是，你会发现计算机网络其实挺有意思的！


## 《网络指北：图解版》目录：

### 一、网络基础概念

* [什么是计算机网络？](https://csguide.cn/network/overview/10_what_is_network.html)
* [网络是如何连接的？](https://csguide.cn/network/overview/30_how_networks_connect.html)

#### 1. 网络分层模型
  ○ [TCP/IP分层模型](https://csguide.cn/network/overview/20_protocol.html)

  ○ [分层思想的工程哲学：为什么需要分层？](https://csguide.cn/network/overview/21-why-layered.html)

  ○ [各层典型协议和设备对应表](https://csguide.cn/network/overview/23-network-layers.html)
#### 2. 网络协议基础
  ○ [报文/帧/包的区别](https://csguide.cn/network/overview/40-data-package.html)

  ○ [MAC 地址和 IP 地址](https://csguide.cn/network/overview/45-ip-mac-port.html)

  ○ [带宽、吞吐量、时延](https://csguide.cn/network/overview/48-bandwidth-throughput-latency.html)


### 二、传输层

  ○ [传输层基础概念：TCP & UDP](https://csguide.cn/network/transport/0_overview.html)

  ○ [UDP 详解](https://csguide.cn/network/transport/5_udp.html)

  ○ [TCP 协议概述](https://csguide.cn/network/transport/10_tcp_overview.html)

  ○ [TCP重传机制：滑动窗口、流量控制、拥塞控制 （上）](https://csguide.cn/network/transport/30_tcp_sliding_window.html)

  ○ [TCP 流量控制：滑动窗口、流量控制、拥塞控制 （下）](https://csguide.cn/network/transport/40_tcp_flow_control.html)

### 三、核心协议：IP 层
  ○ IP协议与路由选择
  ○ ICMP协议：ping/traceroute原理
  ○ NAT技术：IP 地址不够用咋办？

### 四、核心协议：应用层
  ○ HTTP协议：
  ○ 报文结构/状态码/缓存机制
  ○ HTTPS加密流程（TLS握手动画演示）
  ○ HTTP/2/3核心改进
  ○ DNS协议：
  ○ 域名解析全流程（递归/迭代查询）
  ○ 常见攻击手段（DNS劫持/污染）

### 五、高频考点模块

#### 1. 场景化问题解析
  ○ 浏览器输入URL后发生了什么？
  ○ 为什么TCP连接需要三次握手？
  ○ TCP如何保证可靠传输？
  ○ 直播软件为什么用UDP不用TCP？

#### 2. 故障排查工具箱
  ○ 网络诊断命令集（ping/tracert/netstat）
  ○ 端口占用排查实战
  ○ 跨域问题解决方案全解析（CORS/JSONP等）

### 六、进阶实战模块

#### 1. 协议实现原理
  ○ 手撕简易TCP粘包处理
  ○ 实现一个HTTP服务器核心逻辑
  ○ Socket编程入门示例（附Python代码）

### 七、安全攻防基础
  ○ XSS/CSRF攻击原理与防御

  ○ 中间人攻击演示（ARP欺骗实验）

  ○ [数字证书 & 数字签名](https://csguide.cn/network/security/6_digital_certificate.html)


（对了，有问题随时在评论区提出，我会及时回复，也欢迎加入学习交流群一起讨论~）