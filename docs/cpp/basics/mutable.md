# mutable 的作用

面试高频指数：★★★☆☆

`mutable`是C++中的一个关键字，用于修饰类的成员变量，表示该成员变量即使在一个`const`成员函数中也可以被修改。

mutable的中文意思是“可变的，易变的”，跟constant（既C++中的const）是反义词

因为在C++中，如果一个成员函数被声明为`const`，那么它不能修改类的任何成员变量，除非这个成员变量被声明为`mutable`。

看起来有点奇怪是不是，我也觉得，所以实际上这个关键字我在工作中几乎从来没看见过。。。

但是还是掌握以下吧哈哈

这个关键字主要应用场景就是有些时候我们需要**在const函数里面修改一些跟类状态无关的数据成员，那么这个函数就应该被mutable来修饰，并且放在函数后后面关键字位置**。



#### 举个例子

```c
#include <iostream>

class Counter {
public:
    Counter() : count(0), cache_valid(false), cached_value(0) {}

    int get_count() const {
        if (!cache_valid) {
            // 模拟一个耗时的计算过程
            cached_value = count * 2;
            cache_valid = true;
        }

        return cached_value;
    }

    void increment() {
        count++;
        cache_valid = false; // 使缓存无效，因为count已经更改
    }

private:
    int count;
    mutable bool cache_valid; // 缓存是否有效的标志
    mutable int cached_value; // 缓存的值
};

int main() {
    Counter counter;
    counter.increment();
    counter.increment();

    std::cout << "Count: " << counter.get_count() << std::endl; // 输出 4

    return 0;
}
```

上面定义了一个`Counter`类，该类具有一个计数成员变量`count`。还有两个`mutable`成员变量：`cache_valid`和`cached_value`。

这两个变量用于在`get_count`函数中缓存计算结果，从而提高性能。

`get_count`函数被声明为`const`，因为它在逻辑上不会更改类的状态。

然而，我们需要更新`cache_valid`和`cached_value`变量以提高性能。

为了在`const`成员函数中修改这两个变量，我们将它们声明为`mutable`。

这个例子不那么贴切的展示了`mutable`关键字的用途：

允许我们在`const`成员函数中修改特定的成员变量，以支持内部实现所需的功能，同时仍然保持外部不变性。