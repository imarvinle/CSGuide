---
title: 腾讯暑期实习经历分享
shortTitle: 鹅厂实习经历
description: 分享在腾讯实习经历，包括面试准备、日常工作、技术成长和职场体验，为计算机专业学生提供实用的求职和实习参考。
head:
  - - meta
    - name: keywords
      content: 互联网实习,实习经验,大厂实习,程序员实习,技术面试,职场经验,实习求职
---


> 原文发表于公众号 [在微信转正了](https://mp.weixin.qq.com/s/VDlkZfVksiYw9vJg36JnVA)

## 前言

现在已经快七月了，想必已经有很多同学正在各大公司实习，大家在实习的同时也别忘了备战秋招呀！

现在 BAT 这些大公司，秋招 60%(根据身边同学的反馈大致估算，无官方数据) 以上的校招 HC是发给了转正的实习生。

只要实习期间能尽快融入团队、积极学习并有所产出，我觉得转正应该都不难。

并且就算没有转正，有大厂实习经历背书，秋招在面其它公司的时候简历也更加容易通过。

面试官一般看到有其它大厂实习经历的同学，也会特别感兴趣一些。

所以我现在把实习叫做大厂校招「提-提前批」。

当然了，对于有些导师不放、或者其它原因没有办法去暑期实习的同学，也不必着急。

**把时间用在刷算法题、打牢基础上，秋招也是可以成为 offer 收割机的。**

并且这样的例子还不少，和我同届的一个朋友，当时三四月份准备实习的时候并不充分，然后阿里、百度这些面试都挂了。

但是一直在学校复习刷题、补基础，到了七月份就开始投提前批了，顺利拿到了 oppo 提前批 sp，再到八九月的时候，他已经手上拿了阿里、美团、华为、字节这些一堆的 offer 了。

总之呢，不管你是有去实习还是没有，都要有所规划。

**实习的做好转正和秋招两手准备，没实习的 All IN 秋招。**

之前很多同学问我实习相关的问题，所以今天在这聊聊我的第一份实习经历吧。

## 一、实习offer

当时春招是  3 月 12 号投递的鹅厂简历，3 月 13 号开始面试，3 月 25 号收到实习 Offer，鹅厂效率还挺高的：
![](https://cdn.how2cs.cn/gzh/008i3skNgy1grwvp41symj309x02rjsl.jpg)
在这篇文章中也有记录[春招总结](https://mp.weixin.qq.com/s/SIbk3sz-hig6HhsRNzb0XA)，感兴趣的话可以看看~


## 二、实习经历

六月下旬期末考试全部结束后，我就飞到深圳去了。

深圳天气真的非常好，经常是晴空万里，晚上也能看到云彩在天空飘着（是不是光污染太严重了....）

![](https://cdn.how2cs.cn/gzh/008i3skNgy1grwvujbz20j31400u0q5s.jpg)

![](https://cdn.how2cs.cn/gzh/008i3skNgy1grwvwssixcj31400u07wh.jpg)

关于实习时间，我建议如果学校没什么课，可以四五月就去实习，实习时间长一点，团队融入和产出都会更好一些。

并且早点去，可以在七八月就有时间参与秋招了，两手准备~


## 三、需要做什么提前准备吗？

一般来说不用特别刻意去准备，但是如果涉及到转语言之类，比如本来做 Java 入职要写 C++，最好还是提前跟导师联系一下，问下技术栈、需要提前学习的一些东西。

这样的话，会减少入职后学习时间，尽快上手做项目。

比如我当时大概问了下导师，基本上只需要熟悉 C++、Linux、shell，还有就是 Web 安全方面的东西。


## 四、实习入职

到深圳的第二天，早早的就去公司报到了，第一次坐滨海电梯还有点不太会使用，有种进大观园的感觉：

![](https://cdn.how2cs.cn/gzh/008i3skNgy1grww1tkp7vj30u0140tfv.jpg)

领工卡、电脑，找导师定下工位之后，就是一系列的开机、入域（办公防护软件...你懂的）、装开发环境。


当时实习的时候在深圳微信，主要是微信支付相关的，这边基本上线上业务都是用 C++ 完成的，整个 C++ 服务体系和生态非常完善。

主要的框架是 Svrkit，关于这个的介绍我就直接贴腾讯开源项目 phxrpc (svrkit简化版) 里 一个 Issue 的描述了：

> 微信后台主要使用 C++，后台服务使用 Svrkit 框架搭建，服务之间通过同步 RPC 进行通讯。

>  Svrkit 是另一个广硏后台就已经存在的高性能 RPC 框架，当时尚未广泛使用，但在微信后台却大放异彩。
>
>  作为微信后台基础设施中最重要的一部分，Svrkit 这几年一直不断在进化。我们使用Svrkit构建了数以千计的服务模块，提供数万个服务接口，每天RPC调用次数达几十万亿次。

> 于是我们开始尝试使用一种新的CGI架构——Logicsvr。
> Logicsvr基于Svrkit框架。将 Svrkit 框架和 CGI 逻辑通过静态编译生成可直接使用 HTTP 访问的 Logicsvr。

> 每个 Logicsvr 都是一个独立的二进制程序，可以分开部署、独立上线。时至今日，微信后台有数十个 Logicsvr，提供了数百个 CGI 服务，部署在数千台服务器上，每日客户端访问量几千亿次。

> Svrkit——Client/Server自动代码生成框架:10分钟搭建内部服务器

来源：```https://github.com/Tencent/phxrpc/issues/25```



个人感觉开发体验比较接近 Java，除了 C++ 本身没有包管理，编译构建要麻烦一点。


## 五、实习初体验

把 VSCode、SVN 这些环境装好后，导师就发了一些资料、文档给我，有业务的有技术文档的，让先熟悉这些微服务框架，写个 Demo 出来。

（PS：当时整个微信代码还用 SVN 管理的，不过去年就已经切换到 Git 了，其实 SVN 还是 Git 对于开发模式影响不大，这边基本上是每个人负责自己的服务，开个分支出去开发，然后 CR 后合并到 Master，基本不会有大规模改相同的文件。

刚开始第一周，我就拿着导师给的文档，一个劲的看PDF、PPT，还有 Svrkit 入门教程。

老老实实按着文档的指引去定义协议、生成代码、实现函数，但是一编译就各种失败，编译成功后又访问不通，总之遇到现在看来各种傻逼的问题。

但是呢，当时又不好意思去问导师，就自己一个人在那到处搜文档，学习进度也被各种问题卡着。

明明一个很简单的 Hello World，愣是浪费了好久时间，后面请教旁边同事才发现是一个很小的编译依赖问题。

（阿北实习建议：一定不要自己闷着头看文档，最好的方式是让导师先给你宏观的讲一遍整个开发环境，有一个整体的认知，遇到问题，确实要先自己思考解决，但是实在解决不了(比如卡住半小时以上)就及时向导师求助，也可以把问题描述清楚发到组群里，这样不仅能快速解决问题，而且同事们可能会给出一些很好的建议，还能学到其它东西。

另外就是刚入职不要去死看那些业务知识文档，大概浏览一下就可以了，后面遇到具体的需求的时候，再去针对性的补齐。

因为我发现，我把导师发给我的 xxx.pdf、yyy.pdf 都挨着挨着一字不落的看完了，最后的结果就是看完了，也忘完了。仿佛鸟儿飞过天空）

看资料和文档最好是做需求的时候，带着目的去查找。

## 六、实习工作

当时做的主要是识别一种叫 Emvco 的二维码，这是一种国际通用的编码方案，当时的现状是不同地区的大商户接入都要单独写一个文件去适配解析也就是 handle。

我的任务就是实现一个通用的解析方案，然后新接入的商户规则只需要在运营界面配置一下规则即可，不用单独去做开发。

Emvco 本身是一种典型的 TLV(Tag、length、value) 结构编码方案，当时设计一个简单的规则结构和解析方式，这是其中一种最后没采用的方案：

![](https://cdn.how2cs.cn/gzh/008i3skNgy1grwxm5ungxj30u00wugtl.jpg)

把方案和 leader（恰好是导师）、其它同事过了后，就开始编码实现了。

整个项目我需要完成规则识别解析，还有就是一个配置管理系统，也就是用来录入各种机构商户对应的二维码编码规则。

然后我又从零开始学习 Vue 前端开发、PHP 等，我在这之前没做过前端开发，所以当时就是从零边学边做，但是呢，我不会真正的从"零" 开始。

因为我要做的是一个典型的配置管理系统，进行 CRUD，我直接在 Github 找了一个雏形，然后基于这个我再去修改，我觉得没必要重复造轮子，像这种东西就是要在别人的基础上快速吸收学习，然后二次开发。

这里面也是踩了一堆的坑，因为对于前端开发部署，还有整个前后端、测试环境、权限认证等等。

不过好在最后还是成功完成了改造，并且接入新商户的只需要花几分钟配置一下即可，以前是至少需要两三天开发、测试、CR、发布上线的。

除了做这个以外，还做了一些所谓的"打杂"的工作，但是我觉得把这些工作做好也是一样可以学到东西的。

千万不要觉得是"边缘"工作就不好好去做，先把边缘的做好，后面可以积极主动去争取参与一些

尤其是是我们作为实习生，因为本身不稳定，可能实习一两个月就走了，这种情况下 leader 肯定不会安排一些核心的模块的。

当然这个是看每个公司、团队的风格，有些公司对于实习生也是当正式员工一样用。

我前后总共实习了两三个月左右，后面九月份顺利拿到了实习转正的offer。

## 七、血泪教训

最后，说几点对实习同学的建议吧（尤其是刚入职的同学），不是想好为人师，而是自己从新人走过来的一些体会：

* 积极主动找导师沟通，包括进度、困难等
* 多和大家一起去吃饭，混个脸熟，融入团队
* 充分利用公司资源，尤其是BAT这种级别公司，内网非常多的学习资料，简直是个宝库，可以工作之余多学习一点
* 认真对待边缘工作，工作之外主动了解上下游、系统核心模块
* 每天发日报给导师，同步给leader和同事（不要走形式，真正把自己的问题、进度展现出来，日报也是一种表达方式）
* 以正式员工的心态要求自己、投入工作，不要想着自己随时都会跑路....
* 接到需求找产品和导师了解清楚需求背景，先出设计文档给导师看下
* 写代码之前先画图，我之前也喜欢直接撸代码，但是后来发现先把接口的设计、时序图弄好，后面编码反而会更快
* 多做总结，包括项目的难点、自己的方案设计、对比等，答辩的时候用得上~

关于在鹅厂的生活，大家感兴趣的话可以留言~ 

下次再更新~

