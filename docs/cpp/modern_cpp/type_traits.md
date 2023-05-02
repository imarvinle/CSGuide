# 类型萃取

**面试高频指数：★★☆☆☆**

在C++中，类型萃取（type_traits）是一种编译时技术，用于在编译期间获取和操作类型的信息。

主要用于泛型编程以及在编译时做出决策。

类型萃取可以帮我们检查和处理类型特性，从而优化代码、避免错误或提高性能。

C++11 引入了 `<type_traits>` 头文件，其中包含许多内置的类型萃取。下面是一些常见的例子：

1. `std::is_integral<T>`：判断类型 T 是否为整数类型。
2. `std::is_floating_point<T>`：判断类型 T 是否为浮点数类型。
3. `std::is_pointer<T>`：判断类型 T 是否为指针类型。
4. `std::is_reference<T>`：判断类型 T 是否为引用类型。
5. `std::is_const<T>`：判断类型 T 是否为 const 类型。
6. `std::is_same<T, U>`：判断类型 T 和 U 是否相同。

这些类型萃取通常具有一个静态布尔值 `value`，当类型符合特定条件时，它为 `true`，否则为 `false`。

其实这些萃取函数原理都差不多，这里举一个例子说明下，比如 is_integral 用于判断一个变量是否是整形:

```cpp
#include <type_traits>

template <typename T>
struct is_integral_helper : std::false_type {};

template <>
struct is_integral_helper<bool> : std::true_type {};

template <>
struct is_integral_helper<char> : std::true_type {};

template <>
struct is_integral_helper<short> : std::true_type {};

template <>
struct is_integral_helper<unsigned short> : std::true_type {};

template <>
struct is_integral_helper<int> : std::true_type {};

..... 依次类推各种整形都定义一个特化版本

template <typename T>
struct is_integral : is_integral_helper<typename std::remove_cv<T>::type> {};

```

在这个实现中，我们首先定义一个辅助模板 `is_integral_helper`，它默认继承自 `std::false_type`。

然后，我们为所有标准整数类型（包括有符号、无符号和字符类型）提供特殊化，使它们继承自 `std::true_type`。

（ **核心思想就是为所有的整形提供一个特殊版本，其它非整形的就只能匹配到默认的版本，也就是 false_type**

最后，我们定义 `is_integral` 作为 `is_integral_helper` 的一个包装，它首先移除给定类型的 `const` 和 `volatile` 限定符，然后应用 `is_integral_helper`。

`std::true_type` 和 `std::false_type` 是两个辅助类，分别用于表示编译时的 `true` 和 `false` 值。这两个类都有一个名为 `value` 的静态常量数据成员，它们的值分别是 `true` 和 `false`。

实际的实现可能更复杂，以适应各种编译器和平台的特性。

#### 萃取如何用

举一个例子，如何使用类型萃取来选择不同的函数实现：

```cpp
#include <iostream>
#include <type_traits>

template <typename T>
typename std::enable_if<std::is_integral<T>::value, T>::type
foo(T t) {
    std::cout << "foo() called with an integral type: " << t << std::endl;
    return t;
}

template <typename T>
typename std::enable_if<std::is_floating_point<T>::value, T>::type
foo(T t) {
    std::cout << "foo() called with a floating point type: " << t << std::endl;
    return t;
}

int main() {
    foo(42); // Output: foo() called with an integral type: 42
    foo(3.14); // Output: foo() called with a floating point type: 3.14
}

```

上面代码中使用 `std::enable_if` 和类型萃取来选择不同的函数实现。当类型为整数时，将调用第一个 `foo()` 函数；当类型为浮点数时，将调用第二个 `foo()` 函数。

当然，上面的例子你会说那我可以用 foo 函数重载 int 和float类型作为参数啊，不一定非要用萃取。

那么这里再举一个例子:

假设我们有一个模板函数 `print()`，用于打印容器中的元素。我们希望这个函数对于有 `const_iterator` 类型的容器使用 `const_iterator`，而对于没有 `const_iterator` 的容器使用普通的 `iterator`。

```cpp
#include <iostream>
#include <vector>
#include <type_traits>

template <typename T>
struct has_const_iterator {
private:
    typedef char yes[1];
    typedef char no[2];

    template <typename C>
    static yes& test(typename C::const_iterator*);
    template <typename C>
    static no& test(...);
public:
    static const bool value = sizeof(test<T>(nullptr)) == sizeof(yes);
};

template <typename Container>
typename std::enable_if<has_const_iterator<Container>::value>::type
print(const Container& c) {
    typename Container::const_iterator it;
    std::cout << "Using const_iterator." << std::endl;
    for (it = c.begin(); it != c.end(); ++it) {
        std::cout << *it << " ";
    }
    std::cout << std::endl;
}

template <typename Container>
typename std::enable_if<!has_const_iterator<Container>::value>::type
print(const Container& c) {
    typename Container::iterator it;
    std::cout << "Using iterator." << std::endl;
    for (it = c.begin(); it != c.end(); ++it) {
        std::cout << *it << " ";
    }
    std::cout << std::endl;
}

int main() {
    std::vector<int> v = {1, 2, 3, 4, 5};
    print(v);
}

```

在这个例子中，我们使用类型萃取 `has_const_iterator` 来检查容器是否具有 `const_iterator` 类型。然后，我们使用 `std::enable_if` 选择合适的 `print()` 函数实现。这样，对于有 `const_iterator` 的容器，我们可以优先使用 `const_iterator` 进行遍历。