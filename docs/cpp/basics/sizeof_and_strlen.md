# sizeof 和 strlen

**面试高频指数：★★★★☆**

很多 C++ 的同学对于一些基本的 C 库函数没有掌握，面试的时候我遇到不少同学说 不清楚如何获得一个 char* 类型的字符串长度，这是一定不可以的。

接下来讲一下 sizeof 和 strlen。

strlen 是头文件 <cstring> 中的函数，而 sizeof 是 C++ 中的运算符。


`strlen` 是一个 C 标准库中的函数，用于计算 C 风格字符串（以空字符 '\0' 结尾的字符数组）的长度，即不包括结尾的空字符的字符个数。

```cpp
#include <iostream>
#include <cstring>

int main() {
    char str[] = "Hello, world!";
    std::cout << "Length of str: " << strlen(str) << std::endl; // 输出字符串 str 的长度
}
```

strlen 源代码如下:

```cpp
size_t strlen(const char *str) {
    size_t length = 0;
    while (*str++)
        ++length;
    return length;
}
```

`sizeof` 是一个 C++ 编译期间计算的操作符，用于计算数据类型或对象所占用的字节数。

```cpp
#include <iostream>

int main() {
    int a = 42;
    std::cout << "Size of int: " << sizeof(int) << std::endl;    // 输出 int 类型的大小
    std::cout << "Size of a: " << sizeof(a) << std::endl;        // 输出变量 a 的大小
    std::cout << "Size of double: " << sizeof(double) << std::endl; // 输出 double 类型的大小
}
```

