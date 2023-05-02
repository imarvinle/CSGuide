# 数组做参数退化为指针

**面试高频指数：★★★★☆**

接着[sizeof和strlen函数](https://www.yuque.com/csguide/cf15wf/ypxcuscilke4gw9f)这个话题

还有一个比较常见的考题，就是会将一个数组做参数，然后在函数内部用 sizeof 去判断这个数组参数的大小，如下:

```cpp
int func(char array[]) {
    printf("sizeof=%d\n", sizeof(array));
    printf("strlen=%d\n", strlen(array));
}

int main() {
    char array[] = "Hello World";
    printf("sizeof=%d\n", sizeof(array));
    printf("strlen=%d\n", strlen(array));
    func(array);
}
```

比如上述的输出结果分别是多少，对于 64 位机器来说，输出结果为：

```cpp
sizeof=12
strlen=11
sizeof=8
strlen=11
```

前面两个 12、11 大小应该很好理解，但是 func 函数内部 sizeof(array) 为什么会是 8 呢？

#### 数组做参数为什么会退化为指针

这里涉及到一个概念：**数组退化为指针**。
数组退化：在 C++ 中，数组在作为函数参数时会退化为指向其首元素的指针。退化的原因是因为数组作为函数参数时，**实际传递的是指向数组首元素的指针**，不可能逐个拷贝整个数组然后在栈上传递，所以编译器只知道参数是一个指针，而不知道它的长度信息。

但是，当数组直接作为 `sizeof` 的参数时，它不会退化，因为 sizeof 是编译器在编译期间计算的结果，这个时候编译器是有信息知道数组的大小。
为了在函数中获取数组的长度，我们需要将数组的长度作为另一个参数传递给函数，或者使用模板实现。
#### 数组的引用做参数
举例：
```cpp
#include <iostream>
#include <cstring>
template <typename T, std::size_t N>
void printSizeAndLength(const T (&arr)[N]) {
    std::cout << "Size of arr in function: " << sizeof(arr) << std::endl; // 计算数组的大小
    std::cout << "Length of arr: " << strlen(arr) << std::endl; // 计算字符串的长度
}
int main() {
    char str[] = "Hello, world!";
    std::cout << "Size of str in main: " << sizeof(str) << std::endl; // 计算整个字符数组的大小
    printSizeAndLength(str);
}

```

```cpp
Size of str in main: 14
Size of arr in function: 14
Length of arr: 13
```

在这个例子中使用了模板函数 `printSizeAndLength`，它接受一个数组引用作为参数。
在函数内部，使用 `sizeof` 计算数组的大小时，数组不会退化为指针。

引用的作用就在于阻止拷贝的发生，通过传递引用，我们让形参得到和数组名同样的地址。

