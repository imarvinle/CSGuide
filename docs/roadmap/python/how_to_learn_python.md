---
title: Python超详细一条龙版学习路线（2023）
meta:
  - name: description
    content: Python语言学习，Python应用场景，Python简介，Python特点
  - name: keywords
    content: Python、爬虫、Python后台、python自动化运维、Python就业
---

#  Python超详细一条龙版学习路线（2023）

## 一、前言

Python 最近几年非常热门，甚至出圈了，很多非计算机专业的同学也会去学习 Python，辅助自己日常工作、科研、处理数据等等，我也习惯用 Python 来快速开发一些脚本、爬虫。

在 TIOBE 编程语言排行榜上，也是连续多次排名第一。
![](https://cdn.how2cs.cn/csguide/052448.png)

有趣的是，Python其实 早在 1991 年就发布了，是著名的“龟叔”Guido van Rossum在1989年圣诞节期间，为了打发无聊的圣诞节而编写的一个编程语言。

这些年云计算、机器学习、深度学习等 AI 的快速发展促进了 Python 的火热。

当然了，打铁还得自身硬，这一切都离不开 Python 本身的特点：

**简单、易用、丰富易用的库**

下面简单介绍一下 Python 语言：

## 二、 Python 简介
Python 是一门现代高级编程语言，它的设计哲学就是强调代码的可读性和简洁性，同时拥有丰富的标准库和强大的生态系统，广泛应用于各个领域。

### 2.1  Python 的优点
* **简洁易懂：**
Python 的语法简单明了，可读性极高，相对 C 语言等更加接近人类自然语言，这让很多非专业程序员也能快速上手。

* **跨平台**
Python 也是一种虚拟机执行的语言，因此也可以像 Java 一样跨平台，具有很好的可移植性，即 “Write once, run anywhere”

* **丰富的标准库：**
不管你是写爬虫、处理数据还是机器学习，Python 都有非常丰富的类库，有时候一行代码能干 C/C++ 几十行的功能。
### 2.2 Python的缺点
任何编程语言都有独特的优缺点，这也是语言设计取舍不同而导致的，比如 C++ 设计原则就是 zero overhead abstraction（零成本抽象），而 Python 就是简单优雅。

* **运行速度慢**
由于 Python是解释型语言，代码会在执行时一行行被翻译成 CPU 能执行的机器码，相比 C/C++ 这类提前把代码编译成 CPU 可以执行的机器码来说，会更加耗时一些。

* **源码没法保密**
Python 这种解释型语言，对外发布的就是源码，没法对代码做保密。

### 2.3 Python 主要应用领域
* AI、人工智能
* 数据分析
* Web后台开发
* 网络爬虫
* 自动化运维、脚本开发

后面会详细介绍这些方向的具体学习方向。

### 2.4 就业如何

如果是科班的同学或者想转码的同学，不建议用 Python 作为主语言，还是建议以传统的 Java、C++、Go 这类为主，目前国内还是很少有公司以  Python 作为主力开发语言。

并且机器学习、人工智能、数据分析这一类，Python 其实只是工具，更重要的是对应领域的专业能力，比如AI 就是数学和算法思想才是重点，而不是 Python 语言掌握如何。

## 三、Python学习路线

**目前 Python 官方已经停止支持 Python2 了，所以建议大家直接上手学习 Python 3。**

正如这篇文章[如何快速掌握编程语言的一半](https://www.yuque.com/csguide/index/npcw70z4s01sigqw) 所说，学习一门编程语言，首先需要掌握语言的核心语法和关键词。

对于有其它编程语言基础的同学，这部分几乎可以在 2、3天内完成，如果第一次接触编程，那么可能需要 2  周左右的时间慢慢熟悉变量、循环、条件语句、函数这些语法特性。

下面是 python 中的一些核心语法：

### 3.1 Python 核心语法

#### 数据类型
- 数值型、布尔型、字符串、列表、元组、集合、字典

#### 控制结构
- 条件语句、循环语句、循环控制

####  函数
- 函数的定义、调用、参数、递归函数

#### 运算符、操作
- 切片、迭代、列表生成式、生成器、迭代器

#### 模块与包
- 模块导入、常用内置模块、包的创建与使用

####  IO文件操作
- 文件打开与关闭、读取、写入

#### 异常处理
- 异常捕获、异常抛出

#### 面向对象编程
- 类与对象、属性与方法、继承与多态、封装

####  函数式编程
- 高阶函数：map、reduce、filter、sorted
- 匿名函数
- 返回函数
- 偏函数
#### 装饰器
- 函数装饰器、类装饰器

### 3.2 入门学习材料

#### 开发环境
* 安装Python: 
建议直接安装 Python3，参考：runoob.com/python3/python3-install.html

* 开发工具
强烈建议 PyCharm，应该算是最强大的集成开发工具，简单一点的话可以用  VSCode

* IPython：
功能丰富的工具，非常有效的使用交互式 Python。

#### Python教程
* 廖雪峰 Python 教程

推荐廖雪峰 Python 教程：[https://www.liaoxuefeng.com/wiki/1016959663602400](https://www.liaoxuefeng.com/wiki/1016959663602400)， 可能是中文写得最好的 Python 入门教程了，有编程基础的，大概 一周可以把这个文档刷一遍，就可以愉快的写各种 Web、爬虫了。
*  Python谷歌编码风格中译版: 
 [https://www.elias.cn/Python/PythonStyleGuide#Global_variables](https://www.elias.cn/Python/PythonStyleGuide#Global_variables)

#### 视频教程：
* 北理工 Python语言程序设计
可以看北京理工大学 嵩天 老师的 Python 程序设计语言课：[Python语言程序设计](https://www.bilibili.com/video/BV1Z64y1h7Rk?p=8&vd_source=dbac6fa7bb0e0e4d77d2421a5d3792cd)
* 黑马Python
如果不喜欢 MOOC 这种风格的课程，可以考虑看看黑马的：[黑马Python课程](https://www.bilibili.com/video/BV1qW4y1a7fU/?spm_id_from=333.337.search-card.all.click)
* MIT 计算机科学导论与Python编程
喜欢看国外公开课的话，可以看下MIT的  ：[计算机科学导论及Python编程](https://www.bilibili.com/video/BV1aJ411F7zE/?spm_id_from=333.788.recommend_more_video.4&vd_source=dbac6fa7bb0e0e4d77d2421a5d3792cd)

#### 学习书籍

* 《Python编程：从入门到实践》 

这本书是《Python Crash Course》的中文版，适合 Python 初学者掌握 Python 的基础知识。书中还包括实践项目，如游戏开发、数据可视化和 Web 应用开发等，

* 《Python核心编程（第3版）》
本书深入浅出地介绍了 Python 的核心概念和技巧。
适合具备一定编程基础的读者，可以学到 Python 语言的核心知识、标准库、高级特性以及最佳实践。

**学完基本的 Python 语法特性后，就可以根据自己的实际需求，学习特定方向的技术栈了。**






