---
title: 各层网络协议和网络设备对应表（对比版）
shortTitle: 各层网络协议和网络设备对应表（对比版）
description: 本文详细对比了OSI七层模型与TCP/IP四层模型，列出各层典型协议和网络设备，帮助你快速建立网络分层的结构化认知，掌握网络协议栈的核心逻辑。
head:
  - - meta
    - name: keywords
      content: 计算机网络分层模型, OSI七层模型, TCP/IP四层模型, 网络协议, 网络设备, 路由器, 交换机, HTTP, TCP, IP, 数据链路层
---

## 各层网络协议和网络设备对应表（对比版）

大家好，我是小北。

在前面两篇文章文章我们分别介绍了 [网络模型](/network/overview/20_protocol.html) 以及为什么需要[分层](/network/overview/21-why-layered.html)。

根据我的经验，这个分层，日常中用的最多的场景是在于七层负载均衡、四层负载均衡、四层交换机这样的表达中。

这里简单说明下负载均衡:

> 四层负载均衡（L4 Switch）​​ 工作于传输层（TCP/UDP），就像快递分拣员仅查看包裹的收件地址。它基于IP地址和端口号进行流量转发，不解析具体应用内容。例如：客户端访问1.1.1.1:80时，负载均衡器直接将请求转发至后端服务器的2.2.2.2:8080，全程不关心传输的是HTTP请求还是MySQL数据。典型方案如LVS（Linux Virtual Server）和F5 BIG-IP，优势是性能极高（硬件方案可达百万级并发），但无法实现基于URL、Cookie的精细化路由。

> 七层负载均衡（L7）​​ 则深入应用层协议（如HTTP头、SSL证书），如同智能配送员拆开包裹检查内容。它能根据URI路径（如 /api/ 转发到Java集群，/static/ 转发到CDN）、HTTP头部（如浏览器类型、语言）甚至请求内容（如JSON字段）做决策。
> 典型工具如Nginx、HAProxy，支持灰度发布、SSL卸载、防爬虫等高级功能，但性能损耗较大（软件方案通常支持万级并发）。



在这还是主要分析一下网络分层和实际的网络设备关系：

| **工作层级**       | **典型设备**       | **核心功能**                     | **工作特点**                              |
|--------------------|--------------------|----------------------------------|------------------------------------------|
| 应用层/表示层/会话层 | 网关（应用层网关） | 协议转换、内容过滤、防火墙        | 处理高层协议（如HTTP代理）                |
| 传输层             | 四层交换机         | 基于端口号进行流量转发            | 识别TCP/UDP端口（如负载均衡场景）          |
| 网络层             | 路由器、三层交换机 | IP寻址、路由选择、跨网络通信       | 基于IP地址进行转发，支持NAT                |
| 数据链路层         | 交换机（二层）、网桥 | MAC地址学习、局域网数据帧转发      | 通过MAC地址表实现快速转发                  |
| 物理层             | 集线器、中继器、网卡 | 信号放大、比特流传输              | 无寻址能力，广播式传输                     |

---


### 应用层网关

这种是工作在应用层的，比如 Nginx 网关，将不同协议的数据转换为统一格式（如将HTTP请求转换为内部API协议）

也可以拦截恶意流量或敏感信息（如企业防火墙过滤非法网页）。

### 四层交换机

四层就是传输层，传输层的特点就是端口号，所以四层交换机是基于端口号转发流量：例如将80端口的HTTP请求分发到多台Web服务器（负载均衡）。

特点就是能识别TCP/UDP头部：不解析应用层数据，仅根据端口号决策。


### 三层交换机

三层就是网络层，三层交换机实际就是路由器，是利用 IP 地址进行寻址和转发的设备，当然了，有些路由器也带有交换机的能力。

另外就是诸如NAT（网络地址转换）这种协议也是工作在三层交换机，将私有IP映射为公网IP（如家庭宽带共享上网）。


### 二层交换机

二层交换机实际就是我们日常所说的“交换机”。

交换机主要就是数据帧转发：仅在同一局域网内转发数据帧（如办公室内PC互访）。 

当然了，还需要具备 MAC 地址学习等能力：记录端口与MAC地址的映射关系，构建转发表。

通过MAC地址表实现精准转发（非广播），减少冲突域。


### 物理层网络设备

这种一般是集线器 & 中继器这种，它们完全不理解任何地址信息，纯粹无脑向所有端口转发，所以冲突会很大，一般是用来做信号中继。

    
## 总结


层级越高，智能度越高：

**应用层网关可解析HTTP内容，而集线器仅处理电信号。**
 
转发依据不同：

**二层交换机看MAC地址，路由器看IP地址，四层交换机看端口号。**

一张图总结：

![网络层级和设备关系](https://cdn.how2cs.cn/gzh/2025-03-04-network-layer-devices.svg)

## 常见问题

1. **路由器工作在OSI哪一层？交换机呢？**  
   - 路由器工作在网络层（第三层），交换机传统工作在数据链路层（第二层）。但注意，三层交换机具备部分路由功能（现代网络设备多为多层混合工作）

2. **ARP协议属于哪一层？为什么？**  
   - ARP属于网络层（第三层）。虽然它基于MAC地址，但本质是解决IP地址到MAC地址的映射问题，属于网络层逻辑。  
   不要因涉及MAC地址误判为数据链路层协议。


4. **为什么交换机不能替代路由器？**  
   - 二层交换机基于MAC地址通信，无法处理跨网络的IP路由；路由器通过路由表实现不同网络间的数据转发。

