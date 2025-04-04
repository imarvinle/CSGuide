---
title: C++中类（class）和结构（struct）的区别
shortTitle: C++ class 和 struct 区别
description: 本文详细介绍了 C++ 中 class 和 struct 的区别，包括成员默认访问权限、继承默认访问权限、模板参数使用等方面的差异。
head:
  - - meta
    - name: keywords
      content: C++, class, struct, 区别, 访问权限, 继承, 模板参数
---

**面试高频指数：★★★☆☆**

C++ 中为了兼容 C 语言而保留了 C 语言的 struct 关键字，并且加以扩充了含义。

在 C 语言中，struct 只能包含成员变量，不能包含成员函数。

而在 C++ 中，struct 类似于 class，既可以包含成员变量，又可以包含成员函数。

## 不同点

C++ 中的 struct 和 class 基本是通用的，唯有几个细节不同：

* **class 中类中的成员默认都是 private 属性的。**

* **而在 struct 中结构体中的成员默认都是 public 属性的。**

* **class 继承默认是 private 继承，而 struct 继承默认是 public 继承。**

* **class 可以用于定义模板参数，struct 不能用于定义模板参数。**

意思是这样可以：

```cpp
template <class T>
struct  Person {
public:
    T age;
};
```

但是这样就会编译报错:

```cpp
template <struct T>
struct  Person {
public:
    T age;
};
```

## 使用习惯

实际使用中，struct 我们通常用来定义一些 POD(plain old data)

在 C++11 及之后的标准中，POD 类型需要同时满足两个独立条件：

**​​平凡（Trivial）**​​：类型具有默认的构造/拷贝/移动/析构函数（可自动生成且非虚）
​​**标准布局（Standard Layout）​​**：内存布局与 C 兼容，成员排列顺序符合特定规则

同时满足平凡性和标准布局的类型称为 POD 类型，这类数据可以安全使用 memcpy 等底层内存操作，因为它们的内存布局与 C 完全兼容且没有特殊处理需求。


