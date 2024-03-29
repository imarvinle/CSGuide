# 计算机网络自底向上


## 正文

### 什么是协议？

> 协议就是参与的各方行成的一个统一的标准，并且共同遵守的东西，就像人类世界的语言一样，	大家遵守一样的语法、语义和文字，那么就能够互相沟通。

在人类世界中，协议的是非常重要的，同样，在计算机的世界里更是依赖于各种协议，比如网络协议、函数调用约定、字节序、编程语言... 可以说学习计算机，就是学习**理论 + 一堆协议**。

### 网络协议

> 网络协议由通信计算机双方必须共同遵从的一组约定构成。协议有三要素：语法、语义、时序。

语法就像我们规定数据包的的格式、头部字段; 语义就是指需要发出哪种控制信息，接受到信息时，完成哪种动作以及做出什么响应；
时序就是事件实现顺序的详细说明。

在计算机世界里，机器之间如何沟通呢？没错，就是靠一个一个的协议来赋予 0、1  序列意义，协议之间存在层级关系，共同组成了协议栈。

沟通就是传输数据，比如将一台机器上的文件发送到另外一台计算机，中间需要经过无数的路由器、交换机等转发设备来进行转发，那么参与数据传递的各方，从发送者、中间转发、接受者都需要互相理解数据包的格式。

我们知道在计算机世界里，一切都是由0和1构成的，那么路由器从网线里接受到的也是一堆的0和1，路由器怎么知道该把某一个bit发送到哪里去呢？

这里的关键就是发送方将数据提前进行分组，并且包装上头部字段，最简单至少要包含这个分组有多长，需要发送到哪里。

那么路由器又是怎么从 0、1 序列里知道哪些 bit 表示有多长呢，对了，这里就是协议登场了，我们需要一个统一的组织，制定一个规范，比如规定以01010101开头的，第二个 byte 就表示接下来的数据包长度，那么这里又有一个问题，01010101这个序列在中间传输的数据中也可能会出现？这种情况怎么办？那当然是搬出经典的解决方法，转义。

接下来，我们会从最简单的点对点通信开始，逐步扩展到广域网里的主机之间通信。
我们需要网线、主机、交换机、路由器这些设备，顺便再借用两个密码学中常用的学术情侣 Alice 和 Bob。**为了关注核心问题: 数据如何在网络中传输的，以下内容均不讨论差错校验、超时重传等处理数据传输错误和丢包的情况，也就是说我们的网络是不会出问题的！**

故事正式开场！

时间倒回到上世纪五十年代，那时候还没有英特网，假如 Alice 和 Bob 想回家后也能像今天这样使用 IM 工具通过网络聊天，他们应该怎么做呢？

### 从最简单的问题开始，点对点通信


最初，Bob 用了一根很长的网线，直接将他和 Alice 的电脑连接起来。

![点对点直连](https://cdn.how2cs.cn/gzh/007S8ZIlgy1gi8xv13mcbj30bp01z744.jpg)

这样他们就能通过这条网线互相发送数据，我们只知道计算机中的数据都是用 0 和 1 表示的，计算机将 0、1 序列通过网卡发送到数据链路上去，网卡则充当计算机和网线之间的物理接口，负责将计算机中的数字信号转换成电或光信号在网线中传输。

**为了方便起见，后面我们还是将数据在网线上的呈现形式描述为 0、1，虽然这样的表述不够准确，因为实际上网线中是不可能传输 0、1 这样抽象的符号的，传输的是高低电平或者光信号(光纤)。**

Alice 发给 Bob 的信息用 ASCII 编码转化为 0、1 序列，经过网卡、网线到达 Bob 的主机，Bob 的网卡将 0、1 序列提取出来，传递给上层的 IM 工具。

![直接相连架构](https://cdn.how2cs.cn/gzh/007S8ZIlgy1gi8z2dh088j30h906fglr.jpg)

网线上传输的 0、1 序列是流式的，像水管中的水一样，没有分割和边界，这会导致前后传输的信息混合在一起，无法区分。单纯传送编码后的 0、1 序列没有任何意义，我们必须规定一种解读方式解决数据包粘连在一起无法区分的问题。

如何解决呢？

很简单，加上数字世界的**标点符号**，早期的文言文是没有标点符号的，比如**下雨天留客天留我不留**，同样一句话可以有多种不同含义的解读。在古文中，断句方式尚可用上下文来推断，但是要让机器从一堆的 0、1 中找到边界，显然不可能。

所以需要我们制定一个规则，多少位算一组？哪一位有什么含义？

怎样才算接受到一个完整的数据包？

这就是**链路层**做的事情，它在**物理层**之上，规定了如何解释 0、1 序列。

我们把在物理层上传输的一个完整数据包叫做**帧（ Frame）**。
接收方如何判断收到的一个完整的帧呢？这个问题实际上就是帧同步问题，即接收方能从接收到的 0、1 序列中区分出帧的起始与终止。

举个不太恰当的例子，比如 Alice 想给 Bob 说几句话:

“我喜欢你，你喜欢我吗”。

如果没有标点符号和语气停顿来分割句子的话，那该怎么做呢？ “【我开始说了】我喜欢你【我说完了】【我开始说了】你喜欢我吗【我说完了】”。

是不是很简单，只需要在数据本身的前后加上特定的字符就能够划分数据帧了。

解决帧同步的方法其实有很多，这里选两个特别典型的：

* 字符计数法

字符计数法是用一个特殊的字符来表示一帧的开始，然后用一个固定长度的计数字段来表示该帧的长度。当目的主机遇到特殊字符时就知道开始接受一个帧，然后根据后面固定长度的字段提供的字节数，便可知道该帧的结束位置。

![第一版链路层协议格式](https://cdn.how2cs.cn/gzh/007S8ZIlgy1gi90ky84hbj30ee01zweh.jpg)

有没有想过如果数据帧中包含该特殊字符怎么办？其实在字符计数法中是无所谓的，因为有长度字段标识了后面的数据部分的长度，那么在这个长度内的任意字符包括特殊字符我们都会当作帧数据本身。

* 字符填充的首尾界定法

这个方法也特别显然，就是选择两个特殊字符分别作为帧开始和帧结束的标识，比如 10101010 (我们简称为 SOH，Start Of Header )和 01010101 (简称为 EOH，End Of Transmission )。如果数据中存在 SOH 和 EOT 会让接收方错误的以为帧提前结束或者新帧开始了。那怎么办呢？解决方法也很简单，大家也一定用过，那就是转义。就像在 C 语言中, [\]作为控制符，有特定含义，如果我们想用 printf 输出[\]，那么该多加一个 [\]来转义:

```
printf("\\");
```

同理，在这里我们引入一个特殊转义字符 ESC， 对出现在数据中的 SOH 和 EOT 前面再插入一个特殊字符 ESC。举个例子，一下就明白转移字符怎么用的了：

![字符填充首尾界定法](https://cdn.how2cs.cn/gzh/007S8ZIlgy1gi91gnsc2kj30h907jgm0.jpg)

不要问为什么应用层发出的数据直接到链路层了，因为我们这不是在从 0 构建网络嘛，现在还没有网络层、传输层这些。

目前我们的协议成功将 0、1 比特流封成一个个的数据帧，并且有了方法让接收方去识别出来。

Alice 和 Bob 已经能够成功的通过我们这个网络聊天啦！并且不会导致消息错乱。

在上述两种帧同步方法中我们就暂且选择第一种**字符计数法**吧，并且把我们当前这个协议叫做链路层 0.1 版本，它实现了将数据通过直接连接的网线传输到相邻计算机。

注意⚠️ 在我们的链路层 0.1 版本中并没有使用 **MAC 地址**，因为我们当前网络的使用者只有 Alice 和 Bob，并不需要 MAC 地址来做寻址。

* MAC地址：

每个接入以太网的网络设备都需要网卡，而网卡的地址就是 MAC 地址，是由网卡生产的厂商烧录在网卡的闪存中，长度为 48 个 bit，一般用 12 个十六进制数表示。

![路由器的MAC地址](https://cdn.how2cs.cn/gzh/007S8ZIlgy1gi9m07ca22j30dy02kwfj.jpg)

MAC 地址是全球唯一的，这么多厂家，怎么做到不重复的呢？原来啊，MAC 地址的前 24 bit 标识生产商，是由 IEEE 统一分配给各个厂家，比如华为、小米、思科等，后 24 bit 由厂家内部自己分配。

### 第三者——Carol

Carol（一般用于指代通讯中的第三位参加者）是 Alice 和 Bob 的好朋友，当他得知 Alice 和 Bob 待在家也能随时聊天后，留下了羡慕的眼泪，强烈要求 Bob 把自己也加入进去。这可让 Bob 犯了难，由于链路层 0.1 版本没有使用 MAC 地址，所以当多台计算机在一个网络时，我们是无法相互区分的。

为了解决这个问题，Bob 在协议里用上了网卡上的 MAC 地址，在数据帧头部加上了发送方和接收方的 MAC 地址，这样就能区分数据帧是谁发给谁的了。

![第二版链路层协议](https://cdn.how2cs.cn/gzh/007S8ZIlgy1gi95eo2zs8j30h602974d.jpg)

协议制定好了，那么用什么把三台计算机连接起来呢？答案是集线器（Hub）。

![集线器拓扑图](https://cdn.how2cs.cn/gzh/007S8ZIlgy1gi95yqrsptj30dn062dg4.jpg)

要知道集线器只是一个无情的转发机，没有一丁点的自己的想法，只会把从一个端口收到数据帧原封不懂地转发到其它所有端口，简单粗暴！

当 Carol 向 Alice 发送一个消息时，IM 工具将消息编码后的数据包传递给网卡，网卡将 Alice 的 MAC 地址插入到数据包头部组成一个数据帧，并将该帧发送到局域网中。经过集线器的转发，Bob 和 Alice 的网卡都会收到来自局域网的这个数据帧，Bob 的网卡将数据帧头部取出，发现目的 MAC 地址不是自己，便默默的丢弃。而 Alice 的网卡发现目的 MAC 地址就是自己，就会把数据帧内的数据部分拿出来传给上层应用。

现在我们的协议可以支持多人同时在一个局域网里通信啦！

等下！似乎有哪里不对劲，上面说到发送方需要将接收方的 MAC 地址插入到帧头部，那么问题又来了：接收方的 MAC 地址从何得知呢？Carol 只是想给 Alice 发消息而已，肯定不至于要让他去找 Alice 要计算机的 MAC 地址吧。显然，这里需要链路层自己去解决，怎么解决？

1. 手动设置映射表：
   也就是计算机使用者需要手动的设置用户名和 MAC 地址的对应关系

2. 主动查询：
   Carol 的网卡想知道 Alice 的 MAC 地址时，它可以向局域网中发送一个**查询数据帧**，这个数据帧数据部分包含了它要查询的用户名，并且把接收方的 MAC 地址设置为一个**特殊值**（我们就把这个特殊值设为 ff.ff.ff.ff.ff.ff），因为暂时不知道接收方的 MAC 地址。局域网内的每台计算机都能收到这个数据帧，如果某台计算机的用户名和待查询的匹配，那么这个计算机就返回一条数据帧给发送方，数据帧的数据部分就把自己的 MAC 地址填上。
   这个过程其实很类似我们在群里问 “谁是 Alice，把你的 MAC 地址告诉我，我的 MAC 地址是 xxx ”，当这个广播信息被 Alice 收到后，发现有人找自己，就会**定向回复**：“xxx, 我是 Alice，我的 MAC 地址是 yyy”。这样链路层就成功将用户名转换为 MAC 地址，然后就可以开心滴发送数据。并且计算机会把  Alice 对应 yyy 这样的**映射关系缓存到本地**，下一次向 Alice 发送数据的时候，可以直接从本地记录获取 MAC 地址，不需要在群里吼了。

![查询MAC地址的广播](https://cdn.how2cs.cn/gzh/007S8ZIlgy1gi97l7t2mcj30ht072gmn.jpg)

这里又产生了一个问题，像 Alice、Bob 这样的名字很容易重复，如果在一个局域网内有两个Alice，那么我们就会收到两条回复，计算机也没法分辨这两个Alice到底哪个才是我们要找的。所以我们需要将用户名用一个唯一的标识来替代，类似身份证号，最好有统一的机构分配，这样可以保证唯一性。发送查询信息的时候就可以使用这个唯一标识号，永远不会存在冲突。

方法二实际上又规定了一种局域网内主机的交互方式，这就是协议呀！我们在解决问题的过程中其实就是不断引入新的设备、新的协议、划分新的层次。

其实到这里大家肯定看出来了，这个所谓的新协议就是 **ARP 协议**做的事情，那么我们说的唯一标识其实就相当于 IP 地址了，不过在当前的场景下，IP 地址的作用仅仅是做一个唯一标识，彷佛是大材小用了，所以理论上身份证号就非常够用啦！

到这里我们才可以算是解决了局域网内任意主机的通信问题，下一步该干啥了，当然是向着更宽阔的广域网前进。

在前进之前，我们先介绍一位新朋友--交换机，他是来代替集线器的，交换机和集线器一样，负责把从一个端口收集到的数据转发到另外的端口，但是交换机有个特别的地方在于他会学习，怎么学习的呢？

![交换机拓扑](https://cdn.how2cs.cn/gzh/007S8ZIlgy1gi98q93j7pj30dn0bz0t7.jpg)

在上面的网络拓扑中，假设 A 第一次给 B 发消息，A 会构造一个源地址是自己，目的地址是 B 的数据帧，当数据帧到达交换机的时候，交换机并不知道 B 对应哪个端口，所以只能向所有端口转发，但是注意，交换机会在这个时候悄悄在小本本上记下 A 是来自于 H1 端口的。所以当下次其它计算机给 A 发数据帧的时候，交换机就能直接从小本本上找出 A 对应 H1 端口，那就可以直接将数据转发到 H1 端口，从而降低对网络中其它节点的干扰。

看！学习多么滴重要，不学习的集线器已经被爱主动学习的交换机替换掉啦！


### 逐渐扩大的网络

Alice、Bob 和 Carol 搭建的局域网在校园里越来越火，很多小伙伴都要求加入网络，一起冲浪！
这又让 Bob 犯难了，因为大家分布在城市的各个街道、社区，很难直接通过交换机连接起来，即使技术上可以实现，也存在很严重的缺陷。那就是会存在大量的数据发送冲突，导致整个网络可用性非常低，甚至完全不可用，为啥？上面说过了，在局域网内，数据的传递实际上是非常傻瓜式的广播，在同一个局域网中的主机是能够收到其它任何一台主机发送的数据帧的，然后判断目的 MAC  地址是不是自己，不是自己的就丢弃。

这样的方式如果只有几台或者几十台的局域网尚能应付，想象一下整个城市所有的计算机都在一个局域网那会是一种什么场景？很简单，网络会瘫痪，就和人们聊天一样，当几百个人同时大声说话的时候，谁也别想听清对方在说什么。

怎么办呢？那么就只有**分而治之**，按照地理位置距离把小伙伴们分组，同组内通过交换机组成一个局域网，然后，然后，然后。。。然后问题来了，局域网之间通过什么网络设备连接？当然是路由器呀！等等，你这是有上帝视角哈哈哈，我们还是先看看用交换机行不行，存在什么问题？

![交换机连接局域网](https://cdn.how2cs.cn/gzh/007S8ZIlgy1gi9mjn1lnfj30jr0e0dgv.jpg)

现在我们假设，Alice 和 Bob 分别在两个局域网内，那么按照我们之前的设计的协议，当 Alice 想给 Bob 发送消息时，Alice 的主机需要在局域网发送广播询问 Bob 对应的 MAC 地址，但局域网内并不存在 Bob，所以在该局域网内部不会有主机回应。当交换机 Switch-1 收到目的 MAC 地址 **ff.ff.ff.ff.ff.ff** 的数据帧时会直接向所有端口转发，也就是这个查询 MAC 地址的数据帧会广播到所有的局域网，当 Bob 所在的局域网交换机 Swtich-2 收到，会向 Bob 所在局域网内部广播，最终 Bob 会生成一个响应的数据帧发送回去，这次发送回去就不会是广播了，因为我们说过交换机具有自学习能力，它们已经知道想要发给 Alice 的 MAC 地址需要转发给哪个端口了。

大家看出来没有，虽然理论上能正常工作，但是这和用一个交换机(假设交换机端口无限多)直接连接所有计算机并没有区别，所有的计算机还是在一个**广播域**。

如果参与的主机数量太多，网络中广播的数据帧会消耗大量的带宽，降低网络的效率。

所以我们需要怎么做？出现上述现象就是因为一个广播域存在太多主机，光广播泛洪就占掉了大部分带宽，所以我们需要划分广播域。划分的意思就是依据某种特征，把主机分组，在一个组内的所有主机构成一个**子网**，给相同特征的主机发送数据直接使用广播查询 MAC 地址，给特征不一同的主机发松数据，应该有个**中间转发设备**直接向子网外部转发。并且这个设备可以根据目的主机的特征**寻址**。
所以问题变成如何设计这样一个特征？并且需要满足两个条件:

1. 计算机可以根据这个特征判断是不是和自己在一个子网内。

2. 这个特征是可寻址的，什么是可寻址呢？也就是给我一个特证我能找到它在哪里。

   举个例子，身份证号唯一标识了一个人，但它是可寻址的吗？显然不是，因为大家可以跑到任意地方去，我光拿一个身份证号是两眼一抹黑的。那怎么才能找到一个人呢？当然是给出他的定位信息呀，比如**北京市海淀区 xxx 路 yyy**号，拿着这个信息我们才能先到北京，再到海淀区，这样一层层缩小范围，最终找到目标。
   看出来了吗？可寻址这个条件实际上是要求特征要具有**层次结构**，就像地理位置信息一样，这样我们才从大到小，逐渐缩小范围，定位到目标。

如何设计这样一个特征呢？MAC 地址可以吗？显然是不符合，MAC 地址本质上就和人的身份证号一样，是一个扁平的结构。
那怎么办？有请 IP 地址登场！IP 地址恰好完全满足这两个条件，IP 地址最初设计时，就是为了便于寻址以及层次化构造网络，每个 IP 地址包括两个部分，**网络号**和**主机号**，网络号标识子网，主机号标识主机。根据网络号可以找到具体的子网，在子网内部根据主机号就能唯一定位到主机。
在这里也补充一下IP地址的一些信息

> 待补充

引入 IP 地址作为这个“特征”简直完美，那么由什么设备转发呢？交换机显然不合适，它不能识别 IP 地址这个新东西，这个设备必然要能够识别 IP 地址，并且能够根据不同的 IP 地址进行寻址(路由)和转发，那我们姑且把它叫做路由器吧！

现在我们看看加入IP地址和路由器之后，整个网络能够正常且高效的工作完成数据传输任务吗？

【画出网络拓扑，然后举个发送数据的例子，分为局域网内和局域网外】


到这里，学校里的小伙伴们都能够成功的在网上冲浪啦！Bob也是吸引了一大波迷妹。不知道我能不能吸引到一波读者😭
等等这里还有个问题，我们的网络才做到

理论上我们目前这个网络已经能够无限的扩展了，但是还有一些问题在这这篇文章里我没有谈到，因为我们的主线是**自底向上剖析网络是怎么一步一步搭建的**

