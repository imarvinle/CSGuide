# extern C 的作用

**面试高频指数：★★★★☆**

正如这篇文章[extern 的作用](https://www.yuque.com/csguide/cf15wf/dzsd2pviwodzb6v5)所说， extern 是指示链接可见性和符号规则，而 `extern "C"` 则是 C++ 语言提供的一种机制，用于在 C++ 代码中调用 C 语言编写的函数和变量。

如果不用 extern C，由于 C++ 和 C 语言在编译和链接时使用的命名规则不同，这会导致 C++ 代码无法调用 C 语言编写的函数或变量（链接时找不到符号）。
#### 函数的命名规则
简单解释一下什么是函数的命名规则，对于 C++ 语言，**由于需要支持重载**，所以一个函数的链接名（Linkage Name）是由函数的名称、参数类型和返回值类型等信息组成的，**用于在编译和链接时唯一标识该函数**。
函数的链接名的生成规则在不同的编译器和操作系统上可能有所不同，一般是由编译器自动处理，不需要手动指定。
下面介绍一些常见的规则：

- Microsoft Visual C++ 编译器（Windows）：函数的名称会被编译器修改为一个以 "_" 开头的名称，并加上参数类型和返回值类型等信息，以避免链接冲突。例如，函数 `int add(int a, int b)` 的链接名可能是 `_add_int_int`。
- GCC 编译器（Linux）：也会加上参数类型和返回值类型等信息。例如，函数 `int add(int a, int b)` 的链接名可能是 `_Z3addii`。
- Clang 编译器（MacOS）：函数的链接名的生成规则与 GCC 编译器类似，但稍有不同。例如，函数 `int add(int a, int b)` 的链接名可能是 `_Z3addii`。

而 C 语言的链接函数名规则又和 上面三个 C++ 不一样，通过在 C++ 代码中使用 `extern "C"` 关键字，可以将 C++ 编译器的命名规则转换为 C 语言的命名规则，从而使得 C++ 代码可以调用 C 语言的函数或变量。

#### extern c 语法

extern "C" 的语法格式如下：

```cpp
extern "C" {
    // C 语言函数或变量的声明
}

```

使用 `extern "C"` 声明的函数或变量会采用 C 语言的链接规则，即符号的名称和调用约定与 C 语言相同。

下面是一个代码示例：

如何使用 `extern "C"` 在 C++ 代码中调用 C 语言编写的函数：

```c
// C 语言代码
#include <stdio.h>

void print_message(const char* message) {
    printf("%s\n", message);
}

```

```cpp
// C++ 代码
extern "C" {
    // 声明 C 语言函数
    void print_message(const char* message);
}

int main() {
    // 调用 C 语言函数
    print_message("Hello, world!");
    return 0;
}
```

在上面的代码中，使用 `extern "C"` 声明了 C 语言编写的 `print_message` 函数，使得它可以在 C++ 代码中被调用。

在 `main` 函数中，使用 C 语言的语法和命名规则来调用 `print_message` 函数，输出 "Hello, world!"。

需要注意的是，`extern "C"` 关键字只对函数的名称和调用约定起作用，对于函数的参数类型和返回值类型没有影响。
所以，在使用 `extern "C"` 声明函数时，需要保证函数的参数类型和返回值类型与 C 语言的定义相同，否则会导致编译错误或运行时错误。