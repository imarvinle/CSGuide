---
title: C++推荐书籍从入门到进阶
description: 推荐C++领域经典的书籍，包括C++入门、进阶、提高等各个阶段的书，还包含C++电子书下载
head:
  - - meta
    - name: keywords
      content: 计算机书籍, C++, C++，书单，书籍
---


之前写了一篇《[如何系统学习C++？](https://mp.weixin.qq.com/s/WW_X12bTm94iaCgWBgYtJw)》，里面没有推荐太多书籍，所以这次单独搞了一个 C++ 书籍大全。

正如侯捷老师所说 C++ 相关的书籍非常多，如天上繁星: 

> 广博如四库全书者有 《The C++Programming Language》 《C++ Primer》
>
> 深奥如山重水复者有《The Annotated C++  Reference Manual》、《Inside The C++ Object Model》
>
> 细说历史者有《The Design And Evolution Of C++》、《Rumiations on C++》
>
> 独沽一味者有《Polymorphism in C++》、《Genericity in C++》
>
> 独树一帜者有《Design Patterns》、《C++ FAQs》
>
> 程序库大全有之《The C++ Standard Library》.....

为了避免大家不知道怎么选择书籍，小北参考 ```Stackoverflow```上的问题```《The Definitive C++ Book Guide and List》```给大家整理了一份 C++ “最全”书单，并且标注了适合的阶段:

首先是手册型的，这类书籍适用于所有阶段的同学，主要用途是查询语法。

当然了，现在一般都是在```cppreference``` 这类在线网站上查。

## 手册类 – 所有级别皆适用：

- **C++程序设计语言(The C++ Programming Language) 作者：Bjarne Stroustrup(更新到C++11)**

> C++ 之父写的经典 C++ 书籍。
>
> 内容覆盖C++的所有东西，从语言内核到标准库、编程范式和语言哲学。
>
> 2013年5月出版的第四版涵盖了 C++11 的内容。



* **C++标准程序库(C++ Standard Library Tutorial and Reference) 作者：Nicolai Josuttis (更新到C++11)** 

> 这本书是C++标准库（STL）的引导和手册。 2012年4月发行的第二版涵盖了C++11。



* **The C++ IO Streams and Locales 作者：Angelika Langer and Klaus Kreft** 

>  除了这本书，市面上基本没有讲解 streams and locales 的书。



* **The C++ Standard (INCITS/ISO/IEC 14882-2011) 作者：C++标准委员会** 

> 这当然是 C++ 最权威的标准。
>
> C++ 标准文档一般是那些“语言律师”比较爱看的，一般使用者应该很少看这种语法大全。



- **Overview of the New C++ (C++11/14) 作者：Scott Meyers(更新到C++11/C++14)** 

> 这是Scott Meyers开设的一个为期 3 天的 C++ 课程的教材。（3天？我想起了某船长。。。
>
> Scott Meyers是 C++ 社区最受尊敬的作者之一。
>
> 虽然内容比较简短，但质量却极高。

接下来是分初级、中级和高级给大家推荐，当然，这些划分也不是特别死，大家仅供参考就好了：

## 初级

### 入门

如果你是一个无编程经验的 C++ 初学者，或者有其它语言经验的 C++ 初学者，那强烈推荐下面的书籍:

- **C++ Primer 作者：Stanley Lippman, Josée Lajoie, and Barbara E. Moo (更新到C++11)**

> 全书近一千页，透彻的介绍了 C++，以浅显和详细的方式讲到 C++ 语言差不多所有内容。
>
> 2012年 8 月发行的第五版包含 C++11 的内容。
>
> 这也是我认真看过的一本书，不过对于没有 C 基础的同学可能不那么友好。



- **Accelerated C++  作者：Andrew Koenig and Barbara Moo** 

> 这本书覆盖了和C++ Primer一样的内容，但厚度只有 C++ Primer 的四分之一。
>
> 这主要是因为本书面向的不是编程的初学者，而是有其它语言经验的C++初学者。
>
> 对于初学者，本书学习曲线稍显陡峭，但对于能克服这一点的学习者而言，它确实非常紧凑的介绍了C++这门语言。



- **C++编程思想（Thinking in C++) 作者：Bruce Eckel** 

>  共两卷，第二卷主要将标准库，但还是不错的。



- **C++程序设计原理与实践 （Programming: Principles and Practice Using C++ ）作者：Bjarne Stroustrup** 

> C++之父写的C++入门书籍。本书面向没有编程经验的初学者，但相信有编程经验的人也能从本书中学到不少东西。



### 最实用

- **Effective C++ 作者：Scott Meyers** 

> 一言概括，就是帮助你快速习得一些 C++ 里正确的姿势。
>
> 否则你可能需要用几万行代码的经验才能慢慢悟出来。
>
> 本书以瞄准成为 C++ 程序员必读的第二本书籍而写，早期的版本面向从 C 语言转过来的程序员。
>
> 第三版修改为面向从类似 Jave 等语言转来的程序员。
>
> 可以让你的代码写得更加“C++”。



- **Effective STL 作者：Scott Meyers** 

> 讲解方式和Effective类似，但内容主要面向于STL。
>
> 有人说 C++ 是 C With STL，足以可见 STL 的重要性，这本书也强烈推荐看下。



### 中级

- **More Effective C++ 作者：Scott Meyers** 

> 更多（深入）关于C++的规则。
>
> 没有前一本 Effective C++ 重要，但同样值得一读。
>
> 有时间的话可以看下，反正也挺薄的。



* **Exceptional C++ 作者：Herb Sutter** 

> 讲解方式为以解决一系列的 C++ 难题为线索。
>
> 极其透彻的讲解了 C++ 资源管理、异常安全和RAII。
>
> 同时覆盖了一些较为深入的技术，比如：编译防火墙（pimpl idiom)、名字查找规则、好的类设计和 C++ 内存模型。
>
> 推荐指数：五✨



- **More Exceptional C++ 作者：Herb Sutter** 

>  讲到了Exceptional C++没有涉及到的更高级的异常安全技术, 同时讨论了高效的 C++ OOP 方式和如何正确的使用 STL。



- **Exceptional C++ Style 作者：Herb Sutter** 

> 讨论了泛型编程、最优化和资源管理。本书出彩之处在于谈到了如何用非成员函数和单职责原则编写模块化的 C++ 代码。



- **C++编程规范(C++ Coding Standards) 作者：Herb Sutter and Andrei Alexandrescu**

> “编程规范”这里并不是”代码缩进要用几个空格”。这本书包含了101个例子、惯用法、缺陷，通过这些可以帮助你编写正确、清晰高效的C++代码。



- **C++ 模板完全指南(C++ Templates: The Complete Guide)作者：David Vandevoorde and Nicolai M. Josuttis** 

>  这本书是关于C++11之前的模板的。
>
>  它覆盖了从非常基础到最高级的元编程知识，解释了模板工作原理的细节(概念和实现方式）。
>
>  并且讨论了大量的缺陷，想学习模板元编程的同学不要错过啰。



### 高级

### 高级

- **C++设计新思维-泛型编程与设计模式之应用(Modern C++ Design ) 作者：Andrei Alexandrescu**

>  泛型编程鼻祖级书籍。本书先介绍了基于策略（policy-based)的设计、type lists 和泛型编程基础。
>
>  然后讲到了许多有用的设计模式(包括small object allocators, functors, factories, visitors, and multimethods) 如何被高效、模块化、清晰的泛型代码实现。



- **C++模板元编程(C++ Template Metaprogramming)作者：David Abrahams and Aleksey Gurtovoy** 

> 更多的是讲解boost::mpl，想要深入理解mpl的可以看一下。



- **C++ Concurrency In Action 作者：Anthony Williams** 

> 这本书主要内容是C++11的并发支持，包括线程库、原子(atomics)库、内存模型、锁和互斥量。同时也讲解了开发和调试多线程程序的一些难题。



- **Advanced C++ Metaprogramming 作者：Davide Di Gennaro**

> 前C++11时代TMP技术的手册级书籍。本书更侧重于工程实践。里面有大量的可能几乎无人知道但很实用的技术写成的代码。本书可能比Alexandrescu的书更值得读。对于资深的开发者来说，这是一个学习C++暗角技术的绝佳机会，通常这些技术要通过资深的编程经历才能获取。



### 经典 / 古老

注意: 下列书中的部分内容可能有些过时，有时间的话可以看下：

- **C++的设计与演化(The Design and Evolution of C++ )作者：Bjarne Stroustrup** 

> C++ 之父所著，如果你想知道为什么 C++ 是今天这个样子，那么这本书将给你答案。
>
> 本书覆盖C++标准化之前的一切东西，有点像 C++ 编年史。



- **C++沉思录(Ruminations on C++) 作者：Andrew Koenig and Barbara Moo** 

> 这本书不是为了讲解具体的C++技术细节，而是如何通过C++编写出色的OO代码。



- **Advanced C++ Programming Styles and Idioms 作者：James Coplien**

>  讲解了一些C++特有的惯用法. 它确实是一本不错的书籍，如果时间闲暇也可一读。
>
>  不过它确实很老了，可能有些不符合现代的 C++。



- **大规模C++程序设计（Large Scale C++ Software Design） 作者：John Lakos** 

> 本书介绍了如何管理大规模C++软件项目的技术。
>
> 很值得一读，除了有些过时以外。它是在 C++98 以前写的，缺少了好多对大规模项目重要的特性（比如namespace）。



- **深度探索C++对象模型 (Inside the C++ Object Model ) 作者：Stanley Lippman** 



> 如果你想知道虚函数是如何实现、多继承时基类是如何在内存中排布的和所有影响性能的东西，那么这本书会给你答案。
>
> 这里面很多都是国内 C++ 面试高频问题的答案，所以准备面试的话，不要错过。
>
> 不过这本书有好多低级的拼写排版错误，英文原版错误更多，侯捷翻译的版本中注明和纠正了很多，但本书绝对值得一读，你将明白编译器如何实现C++的对象模型。

好了，这么多书，大家按需选择就好了，学习编程最重要的还是多写，毕竟纸上得来终觉浅。



最后送大家一份我整理的 C++ 电子书合集：

![](https://cdn.how2cs.cn/gzh/008eGmZEgy1gmsxf1nt6dj30zy0n6124.jpg)

在我公众号「编程指北」后台回复「**cpp**」即可获取~


<p align="center">
  <img src="https://cdn.how2cs.cn/csguide/095140.jpg" alt="计算机书单" width="auto" height="auto">
</p>

<center>👆也可以截图微信扫描上方二维码， 回复「<font face="黑体" size=4 color="red">cpp</font>
」即可获取</center>

好了，今天的分享就到这了，

大家周末愉快，再坚持两天就过年啦哈哈哈。

记得帮我点个赞哟~




