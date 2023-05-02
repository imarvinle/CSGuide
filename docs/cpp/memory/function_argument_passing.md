# 指针传递、值传递、引用传递

**面试高频指数：★★★☆☆**

在 C++ 中，函数参数传递有三种常见的方式：值传递、引用传递和指针传递。以下分别给出这三种方式的示例：
#### 值传递（Value Passing）

值传递是将实参的值传递给形参。在这种情况下，函数内对形参的修改不会影响到实参。

示例:

```cpp
#include <iostream>

void swap_value(int a, int b) {
    int temp = a;
    a = b;
    b = temp;
}

int main() {
    int x = 10;
    int y = 20;
    swap_value(x, y);
    std::cout << "x: " << x << ", y: " << y << std::endl; // 输出：x: 10, y: 20
    return 0;
}
```

### 引用传递（Reference Passing）

引用传递是将实参的引用传递给形参。在这种情况下，函数内对形参的修改会影响到实参。

```cpp
#include <iostream>

void swap_reference(int &a, int &b) {
    int temp = a;
    a = b;
    b = temp;
}

int main() {
    int x = 10;
    int y = 20;
    swap_reference(x, y);
    std::cout << "x: " << x << ", y: " << y << std::endl; // 输出：x: 20, y: 10
    return 0;
}
```

### 指针传递（Pointer Passing）

指针传递是将实参的地址传递给形参。在这种情况下，函数内对形参的修改会影响到实参。

```cpp
#include <iostream>

void swap_pointer(int *a, int *b) {
    int temp = *a;
    *a = *b;
    *b = temp;
}

int main() {
    int x = 10;
    int y = 20;
    swap_pointer(&x, &y);
    std::cout << "x: " << x << ", y: " << y << std::endl; // 输出：x: 20, y: 10
    return 0;
}
```
其实，关于值传递、指针传递等有很多争论，比如指针传递这个，本质上也是值传递，只不过传递的值是一个指针而已，所以就看你从什么角度去看待这个问题。


在这也贴一篇我之前发表在公众号的文章[值传递与引用传递，传的到底是什么?](https://mp.weixin.qq.com/s/-PscWmBqTFFAq2jSVPpqNw)


### 值传递与引用传递，传的到底是什么？

在网上看到过很多讨论 Java、C++、Python 是值传递还是引用传递这类文章。
所以这一篇呢就是想从原理讲明白关于函数参数传递的几种形式。
参数传递无外乎就是传值（pass by value），传引用（pass by reference）或者说是传指针。
传值还是传引用可能在 Java、Python 这种语言中常常会困扰一些初学者，但是如果你有 C/C++背景的话，那这个理解起来就是 so easy。
今天我就从 C 语言出发，一次性把 Java、Python 这些都给大家讲明白，谁让我是指北呢哈哈
不过呀，要想彻底搞懂这个，需要了解两个背景知识：
* 堆、栈
* 函数调用栈
#### 一、堆、栈
要注意，这“堆”和“栈”并不是数据结构意义上的堆（Heap，一个可看成完全二叉树的数组对象）和 栈（Stack，先进后出的线性结构）。
这里说的堆栈是指内存的两种组织形式，堆是指动态分配内存的一块区域，一般由程序员手动分配，比如 Java 中的 ```new```、C/C++ 中的 ```malloc``` 等，都是将创建的对象或者内存块放置在堆区。
而栈是则是由编译器自动分配释放（大概就是你申明一个变量就分配一块相应大小的内存），用于存放函数的参数值，局部变量等。
就拿 Java 来说吧，基本类型（int、double、long这种）是直接将存储在栈上的，而引用类型（类）则是值存储在堆上，栈上只存储一个对对象的引用。
举个栗子：

```java
int age = 22;
String name = new String("shuaibei");
```
这两个变量存储图如下：
![](https://cdn.how2cs.cn/gzh/0081Kckwgy1glm2do4v2qj30x20oydgn.jpg)
如果，我们分别对```age```、```name```变量赋值，会发生什么呢？
```java
age = 18;
name = new String("xiaobei");
```
如下图：
![](https://cdn.how2cs.cn/gzh/0081Kckwgy1glm2oj4i4uj30w00okgmk.jpg)

```age``` 仅仅是将栈上的值修改为 18，而 ```name``` 由于是 String 引用类型，所以会重新创建一个 String 对象，并且修改 ```name```，让其指向新的堆对象。（细心的话，你会发现，图中 name 执行的地址我做了修改）
然后，之前那个对象如果没有其它变量引用的话，就会被垃圾回收器回收掉。
这里也要注意一点，我创建 String 的时候，使用的是 new，如果直接采用字符串赋值，比如：

```java
String name = "shuaibei"
```
那么是会放到 JVM 的常量池去，不会被回收掉，这是字符串两种创建对象的区别，不过这里我们不关注。
Java 中引用这东西，和 C/C++ 的指针就是一模一样的嘛，只不过 Java 做了语义层包装和一些限制，让你觉得这是一个引用，实际上就是指针。
好，让我继续了解下函数调用栈。
#### 二、函数调用栈
一个函数需要在内存上存储哪些信息呢？
参数、局部变量，理论上这两个就够了，但是当多个函数相互调用的时候，就还需要机制来保证它们顺利的返回和恢复主调函数的栈结构信息。
那这部分就包括返回地址、```ebp```寄存器（基址指针寄存器，指向当前堆栈底部） 以及其它需要保存的寄存器。
所以一个完整的函数调用栈大概长得像下面这个样子：
![](https://cdn.how2cs.cn/gzh/0081Kckwgy1glm11u5x52j30hk0m4mxt.jpg)

**那，多个函数调用的时候呢？**

简单来说就是叠罗汉，这是两个函数栈：
![](https://cdn.how2cs.cn/gzh/0081Kckwgy1glm11bk7x4j30p016s76a.jpg)
今天，我们不会去详细了解函数调用过程```ebp```、```ebp```如何变化，返回地址又是如何起作用的。
今天的任务就是搞明白参数传递，所以其它的都是非主线的知识，忽略即可。
顺便插点题外话：
> 学习新知识有时候需要刨根问底，有时候却需要及时回头，尤其是计算机，你要是一直刨根问题，我能给你整到硅的提纯去，这就是失去了学习的意义。
> 最好的方式是，在一个恰到好处的地方建立一个抽象层，并且认可这个抽象层提供的功能/接口，不去探究这一层下面是什么，怎么实现的。
> 比如，学习 HTTP，我就只需要认 TCP 提供稳定、可靠传输就够了，暂时就不需要去看 TCP 如何做到的。

好了，继续说回函数传参，举个例子，下面这段代码在```main```函数内调用了```func_a```函数

```c
int func_a(int a, int *b) {
	a = 5;
	*b = 5;
};

int main(void) {
	int a = 10;
  int b = 10;
  func_a(a, &b);
  printf("a=%d, b=%d\n", a, b);
  return 0;
}

// 输出
a=10, b=5
```

那么```func_a(a, &b)``` 这个过程，在函数调用栈上究竟是怎么样的呢？

![](https://cdn.how2cs.cn/gzh/0081Kckwgy1glm10jbwgjj30xc0u076r.jpg)

就像上图所示，编译器会生成一段函数调用代码。

将 ```main``` 函数内变量 ```a``` 的值拷贝到 ```func_a``` 函数参数 ```a``` 位置。

将变量 ```b```的地址，拷贝到 ```func_a``` 函数参数 ```b``` 的位置。

记住这张图，这是函数参数传递的本质，没有其它方式，just copy！

copy 意味着是副本，也就是在子函数的参数永远是主调函数内的副本。

决定是值传递还是所谓的引用传递，在于你 copy 的到底是一个值，还是一个引用（的值）。

其实引用也是值......不要觉得引用就是那种玄乎的东西。

所以会有一种声音说，是不存在所谓的引用传递的，一切传引用的本质还是传值。

也就是 pass pointer by value 或者 pass reference by value，哈哈哈有点意思。

今天，我们不讨论到底有没有传引用这个东西，这是一个个仁者见仁智者见智的问题。

我的目的呢，就是把参数传递这个过程给大家剖析下，至于到底是传值还是传引用，那就看大家怎么思考了。

#### 三、pass by value in java

举个最简单的例子来说明下：

```java

public class HelloWorld {
  
    public static void ChangeRef(String name) {
        name = new String("xiaobei");
    }

    public static void main(String[] args) {
        String name = new String("shuaibei");
      	ChangeRef(name);
        System.out.println(name.equals("shuaibei"));

    }
}
```

上面，```ChangeRef``` 函数实际上并没有改变到 ```main``` 函数内的 name 对象，看图就明白了：

![](https://cdn.how2cs.cn/gzh/0081Kckwgy1glm3vdwg03j31dq0u0tb5.jpg)

根据我们前面所讲，参数传递实际就是复制栈上的值本身，这里```name```的值就一串地址，所以```ChangeRef```接收到的也是这串地址，但是在```ChangeRef```函数内将```name```的指向改成了一个新的 String 对象，但是这里不会对```main```函数中的 name 对象产生任何的影响。
咦，不是说引用类型都是引用传递吗？为什么还不会对主调函数产生影响呢？
我们都把引用的指向改变了，还能影响个啥，如果想通过引用传递修改外部传进来的值，一般是采用成员方法。
假设 String 类有一个方法叫做```changeStr(String value)```，那么我们就可以在```ChangeRef```内调用这个方法，修改```name```的值，

并且会同步修改到```main```函数里的值。
（其实这里最好的说明方式是自己定义一个类，但是我懒了，就省掉了哈哈哈，相信聪明的你一定知道我在说什么~）

####  四、Python
其实和```Java``` 挺像的，但是 ```Python``` 有个特点就是所有变量本身只是一个引用，真正的类型信息都是和对象存储在一起的。

所以我打算后面单独聊聊 ```Python``` 对象这个话题，然后把参数传递也放在那里了，今天就到这吧~ 

