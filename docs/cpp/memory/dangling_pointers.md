---
title: C/C++ 野指针和空悬指针(Dangling pointer and wild pointer)
shortTitle: C/C++ 野指针和空悬指针
description: 本文详细介绍了C/C++中野指针和空悬指针的概念、成因和表现，包括野指针和空悬指针的区别和使用场景。
head:
  - - meta
    - name: keywords
      content: C/C++, 野指针, 空悬指针, 内存管理
---

野指针（Wild Pointer）和空悬指针（Dangling Pointer）都是指向无效内存的指针，但它们的成因和表现有所不同，区别如下：

## 野指针（Wild Pointer）
野指针是一个未被初始化或已被释放的指针。

所以它的值是不确定的，可能指向任意内存地址。

访问野指针可能导致未定义行为，如程序崩溃、数据损坏等。

以下是一个野指针的例子：

```cpp
#include <iostream>

int main() {
    int *wild_ptr; // 未初始化的指针，值不确定
    std::cout << *wild_ptr << std::endl; // 访问野指针，可能导致未定义行为
    return 0;
}
```
## 空悬指针（Dangling Pointer）
空悬指针是指向已经被释放（如删除、回收）的内存的指针。

这种指针仍然具有以前分配的内存地址，但是这块内存可能已经被其他对象或数据占用。

访问空悬指针同样会导致未定义行为。

以下是一个空悬指针的例子：

```cpp
#include <iostream>

int main() {
    int *ptr = new int(42);
    delete ptr; // 释放内存

    // 此时，ptr成为一个空悬指针，因为它指向的内存已经被释放
    std::cout << *ptr << std::endl; // 访问空悬指针，可能导致未定义行为
    return 0;
}
```

为了避免野指针和空悬指针引发的问题，我们应该：

1. 在使用指针前对其进行初始化，如将其初始化为`nullptr`。
2. 在释放指针指向的内存后，将指针设为`nullptr`，避免误访问已释放的内存。
3. 在使用指针前检查其有效性，确保指针指向合法内存。

## delete nullptr 安全性

在[知识星球](https://www.yuque.com/csguide/go/fw8rzr12f0vn5bfb)有同学提问:

[问题链接](https://t.zsxq.com/0dYauoOdq)

> 对一个动态存储的指针赋值nullptr，然后使用delete释放内存是否安全。我上网查阅资料有些说安全有些说不安全。
还有一个问题是，使用delete后是否要将其设置为nullptr，网上说设置为nullptr可以避免悬空指针的问题，也有的说将指针删除后设置为nullptr会伪装内存分配错误，说设置为nullptr没什么意义。

回答:

1. C++ 标准里 delete 一个 nullptr 实际上没任何效果，可以理解为是安全的

2. delete 指针后是否要置空其实也有一些争论，不过总体上来说是建议 置空的，这是为了防止后续再使用这个指针去访问到了这块已经释放的内存，但是我觉得写C++的程序员应该要对 指针的生命周期掌握比较准确

至于你说的 删除后 置为 nullptr会掩盖一些错误，这个的确，如果在 delete 后立即将指针设置为 nullptr， 如果后续代码中存在对这块内存的错误使用（比如，再次 delete），这个错误就可能被掩盖，如同第一点说的， delete nullptr 是安全的。



