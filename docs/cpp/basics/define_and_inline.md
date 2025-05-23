---
title: C++ 宏定义（define）和内联函数（inline）的区别与使用场景
shortTitle: C++ define 和 inline 区别
description: 本文详细介绍了C++中宏定义（define）和内联函数（inline）的区别，包括语法特点、执行时机、类型安全性、调试难度和使用场景等方面的对比分析。
head:
  - - meta
    - name: keywords
      content: C++, define, inline, 宏定义, 内联函数, 预处理, 函数调用开销
---


**面试高频指数：★★★☆☆**

宏定义（`#define`）和内联函数（`inline`）都是为了减少函数调用开销和提高代码运行效率而引入的机制，但是它们的实现方式和作用机制略有不同。

## define

在 C/C++ 中，`#define` 是预处理指令的一种。

一般用于定义宏（macro），主要有两种用途：

* 定义常量
* 创建宏函数

无论哪种都是用于在编译时替换文本，也就是 define 实际上只是做文本的替换，可以使用 ```gcc -E``` 选项查看宏替换后的结果。

本篇我们主要讲宏函数的场景，举例:

```cpp
#define SQUARE_SUM(x, y) ((x) * (x) + (y) * (y))
```

写宏函数一定要注意：参数和函数体应当用括号包围，避免因运算优先级导致的错误。

## inline 

内联函数的定义和普通函数类似，只需在函数声明前加上 `inline` 关键字即可。

但是编译器并不一定会将所有声明为内联函数的函数都进行内联，是否内联取决于编译器的实现和优化策略。

内联函数的优点是类型安全、可调试、可优化，但是也存在一些问题。

由于函数体会被复制多次，会占用更多的代码段空间，而且在某些情况下可能会导致代码膨胀。

我们再从以下角度对比下宏定义和内联函数:

## 差别

### 1. 语义
宏定义使用预处理器指令 `#define` 定义。

它在编译期间将宏展开，并替换宏定义中的代码。

预处理器只进行简单的文本替换，不涉及类型检查。

内联函数使用 `inline` 关键字定义，它是一个真正的函数。

编译器会尝试将内联函数的调用处用函数体进行替换，从而避免函数调用的开销。

### 2. 类型检查：
宏定义就是单纯的字符替换，不涉及类型检查，容易导致错误。
```cpp
#define SQUARE(x) ((x) * (x))

int main() {
    int a = 5;
    double b = 5.5;

    // 这里没有类型检查，但在运行时可能导致问题
    double result = SQUARE(b);
}
```
内联函数会进行类型检查，更加安全。

```cpp
inline int square(int x) {
    return x * x;
}
int main() {
    int a = 5;
    double b = 5.5;

    // 下面这行代码将导致编译错误，因为类型不匹配
    double result = square(b);
}
```
### 3. 内联函数可以进行调试，宏定义的“函数”无法调试。

### 4. 宏可能导致不合理计算
在 inline 函数传递参数只计算一次，而在使用宏定义的情况下，每次在程序中使用宏时都会计算表达式参数，因此宏会对表达式参数计算多次。
因为宏只是做替换，可能会把同样的表达式替换到多个地方。
```cpp
#define MAX(a, b) ((a) > (b) ? (a) : (c))

int x = 1;
int y = MAX(x++, 10);
// 宏定义 x++ 会被执行两次, 因为 x++ 会被替换到两个地方，使用内联函数则不会出现这个问题
```