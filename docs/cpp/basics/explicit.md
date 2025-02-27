---
title: C++ 中 explicit 的作用
shortTitle: C++ explicit 关键字
description: 本文详细介绍了C++中explicit关键字的用法，包括防止隐式转换、显式转换和使用场景等方面的分析。
head:
  - - meta
    - name: keywords
      content: C++, explicit, 关键字, 隐式转换, 显式转换
---
**面试高频指数：★★★☆☆**
在 C++ 中，`explicit` 通常用于构造函数的声明中，用于防止隐式转换。
当将一个参数传递给构造函数时，如果构造函数声明中使用了 `explicit` 关键字，则只能使用显式转换进行转换，而不能进行隐式转换。

这种机制可以防止编译器自动执行预期外的类型转换，提高代码的安全性。

## 什么是隐式类型转化

就是当你只有一个类型T1，但是当前表达式需要类型为T2的值，如果这时候T1自动转换为了T2，那么这就是隐式类型转换。

举个例子:

```cpp
int a = 0;
long b = a + 1; // int 转换为 long
 
if (a == b) {
    // 默认的operator==需要a的类型和b相同，因此也发生转换
}
```

## explicit 的作用

接下来通过一个例子来详细解释 `explicit` 的作用。

有一个类 `MyInt`，表示一个整数，并且有一个构造函数可以将 `int` 类型的参数转换为 `MyInt` 类型：

```cpp
class MyInt {
public:
    MyInt(int n) : num(n) {}
private:
    int num;
};
```
我们可以使用下面的代码来创建一个 `MyInt` 对象：
```cpp
MyInt a = 10; // 注意，这段代码有两个步骤： 1. int 类型的 10 先隐式类型转换为 MyInt 的一个临时对象
              //   2. 隐式类型转换后的临时对象再通过复制构造函数生成 a
```
（PS：可能有些同学会说编译器会用复制省略（copy elision）优化这段代码，最终不会有中间这个临时对象产生，但是这是编译器的优化行为，我们暂时不考虑

在一些情况下，上面这种隐式转换可能会导致问题。
例如，考虑下面的函数：

```cpp
void f(MyInt n) {
    // do something
}
```
如果我们调用这个函数，并传递一个 `int` 类型的值作为参数，如下所示：
```cpp
f(10);
```
这也会编译通过，因为编译器会将 `int` 类型的值隐式转换为 `MyInt` 类型的对象。

但或许，有些情况下，我们并不期望 f 函数可以接受一个 int 类型的参数，这是预期外的，可能会导致错误的结果。

那么如果希望只接受 `MyInt` 类型的参数，就可以将构造函数声明加上 `explicit`：

```cpp
class MyInt {
public:
    explicit MyInt(int n) : num(n) {}
private:
    int num;
};
```
这样，上面的调用语句将会导致编译错误，因为不能使用隐式转换将 `int` 类型的值转换为 `MyInt` 类型。

必须使用显式转换，如下所示：

```cpp
f(MyInt(10));
```

所以大家日常可以使用 `explicit` 关键字可以防止不必要的隐式转换，提高代码的可读性和安全性。

尤其是构造函数参数只有一种类型的，强烈建议加上 `explicit`，目前我们部门的代码合并流水线就会按这个规则扫描。

以下是 Google C++ 代码规范中的说明：

![](https://cdn.how2cs.cn/csguide/142207.png)

[Google C++ 风格指南](https://zh-google-styleguide.readthedocs.io/en/latest/google-cpp-styleguide/classes/#section-3)
 