---
title: 什么是大端序、小端序和网络字节序，为什么要有字节序？
shortTitle: 字节序：大端序和小端序
description: 本文详细介绍了计算机系统中的字节序概念，包括大端序和小端序的区别、判断系统字节序的方法，以及在网络编程中字节序的重要性。
head:
  - - meta
    - name: keywords
      content: 字节序, 大端序, 小端序, Big-endian, Little-endian, 网络字节序, C++字节序

---


**面试高频指数：★★★★☆**

字节序是指在多字节数据类型（如整数、浮点数等）中，字节在内存中的存储顺序。

主要有两种字节序：大端字节序（Big-endian）和小端字节序（Little-endian）。

## 大端字节序（Big-endian）

高位字节存储在低地址处，低位字节存储在高地址处。例如，一个4字节的整数0x12345678，在大端字节序的系统中，内存布局如下（从左侧的低地址到右侧的高地址）：

```cpp
0x12 | 0x34 | 0x56 | 0x78
```

大端字节序是符合人类阅读习惯的顺序。

## 小端字节序（Little-endian）

低位字节存储在低地址处，高位字节存储在高地址处。

例如，一个4字节的整数0x12345678，在小端字节序的系统中，内存布局如下（从左侧的低地址到右侧的高地址）：

```cpp
0x78 | 0x56 | 0x34 | 0x12
```

判断系统的字节序的方法有多种，下面是一个简单的 C++ 代码示例：

```cpp
#include <iostream>

int main() {
    int num = 1;
    // 将int类型指针转换为char类型指针，取第一个字节
    char* ptr = reinterpret_cast<char*>(&num);

    if (*ptr == 1) {
        std::cout << "Little-endian" << std::endl;
    } else {
        std::cout << "Big-endian" << std::endl;
    }
    return 0;
}
```

这段代码的原理就是，整数`num`值初始化为1（0x00000001）。然后将其指针类型从`int*`转换为`char*`，这样我们就可以访问该整数的第一个字节。

如果系统是小端字节序，那么第一个字节是1；如果系统是大端字节序，那么第一个字节是0。

通过判断第一个字节的值，我们就可以得知系统的字节序。

## 常见的大小端字节序

在计算机领域中，不同的系统、平台和协议使用不同的字节序。下面是一些常见情况的字节序：

### **1. 网络传输**

在网络传输过程中，通常使用大端字节序（Big-endian），也称为网络字节序，这是 TCP/IP 协议的规定，多字节数据在网络上传输时使用大端字节序。

因此，如果本地系统使用的是小端字节序，那么就需要在传输之前将其转换为大端字节序。

一般通过使用`htonl()`、`htons()`、`ntohl()`和`ntohs()`等函数来完成。

### **2. Linux**

Linux 操作系统在不同的硬件平台上可能使用不同的字节序。例如，x86 和 x86_64（Intel 和 AMD 处理器）是小端字节序（Little-endian），而 PowerPC 和 SPARC 等其他架构可能使用大端字节序（Big-endian）。

所以具体的字节序取决于运行 Linux 的硬件平台。

### **3. Windows**

Windows 操作系统主要运行在 x86 和 x86_64（Intel 和 AMD处理器）架构上，这些处理器使用小端字节序（Little-endian）。

### **4. Mac**

 一般使用 Intel 处理器或 Apple 自家的 M1 芯片（基于ARM架构），这些处理器都采用小端字节序（Little-endian）。

### **5. 总结**

在网络传输中，通常使用大端字节序（网络字节序）。

在具体的操作系统中，字节序取决于底层硬件架构。例如，Linux和Windows操作系统主要运行在x86和x86_64（Intel和AMD处理器）架构上，这些处理器使用小端字节序。

而其他硬件平台，如PowerPC和SPARC等，可能使用大端字节序。