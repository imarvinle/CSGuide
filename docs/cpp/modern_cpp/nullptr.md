# nullptr 和 NULL的区别

**面试高频指数：★★★★☆**

在 C++11 之前，我们通常使用 `NULL` 来表示空指针。

然而，在 C++ 中，`NULL` 的定义实际上是一个整数值 `0`，而不是一个真正的指针类型。

在函数重载和模板编程中这可能会导致一些问题和歧义。

为了解决这个问题，C++11 引入了一个新的关键字 `nullptr`，用于表示空指针。

`nullptr` 是一种特殊类型的字面值，类型为 `std::nullptr_t`，定义为: `typedef decltype(nullptr) nullptr_t`，可以隐式转换为任何指针类型。

与 `NULL` 不同，`nullptr` 是一个真正的指针类型，因此可以避免一些由于 `NULL` 是整数类型而引起的问题。

以下是 `nullptr` 和 `NULL` 之间区别的一些例子：

#### 函数重载

```cpp
#include <iostream>

void foo(int x) {
    std::cout << "foo() called with an int: " << x << std::endl;
}

void foo(char* x) {
    std::cout << "foo() called with a char*: " << x << std::endl;
}

int main() {
    // foo(NULL); // 编译错误：因为 NULL 会被解析为整数 0，导致二义性
    foo(nullptr); // 无歧义：调用 void foo(char* x)
}

```

#### 函数模板

```cpp
#include <iostream>
#include <type_traits>

template <typename T>
void bar(T x) {
    if (std::is_same<T, std::nullptr_t>::value) {
        std::cout << "bar() called with nullptr" << std::endl;
    } else {
        std::cout << "bar() called with a non-nullptr value" << std::endl;
    }
}

int main() {
    bar(NULL); // 输出：bar() called with a non-nullptr value，因为 NULL 被解析为整数 0
    bar(nullptr); // 输出：bar() called with nullptr
}
```

总之，C++11 引入了 `nullptr` 作为一个更安全、更明确的空指针表示，可以避免与整数 `0`（即 `NULL`）相关的一些问题。

在 C++11 及以后的代码中，建议使用 `nullptr` 代替 `NULL` 表示空指针。