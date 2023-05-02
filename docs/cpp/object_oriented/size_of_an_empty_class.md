# sizeof 一个空类大小是多大

面试高频指数：★★☆☆☆

也就是下面这个输出多少:

```cpp
class Empty {};

int main() {
    Empty e1;
    Empty e2;
    std::cout << "Size of Empty class: " << sizeof(Empty) << std::endl;
}
```

大多数情况下 `sizeof(Empty) = 1`

#### 原因

这是因为C++标准要求每个对象都必须具有独一无二的内存地址。

为了满足这一要求，编译器会给每个空类分配一定的空间，通常是1字节。

这样，即使是空类，也能保证每个实例都有不同的地址。

#### C++之父的解释

更多可以看 C++ 之父对于这个问题的解释 [sizeof-empty](https://www.stroustrup.com/bs_faq2.html#sizeof-empty):

![](https://cdn.how2cs.cn/csguide/152608.png)

翻译下就是:

为了确保两个不同对象的地址不同。出于相同的原因，“new”总是返回指向不同对象的指针。考虑以下示例：

```cpp
class Empty { };

void f()
{
    Empty a, b;
    if (&a == &b) cout << "impossible: report error to compiler supplier";

    Empty* p1 = new Empty;
    Empty* p2 = new Empty;
    if (p1 == p2) cout << "impossible: report error to compiler supplier";
}
```

有一个有趣的规则是，如果一个空类做基类，那么在派生类中不需要用一个单独的字节来表示：

```cpp
struct X : Empty {
  int a;
  // ...
};

void f(X* p)
{
  void* p1 = p;
  void* p2 = &p->a;
  if (p1 == p2) cout << "nice: good optimizer";
}

```

这种优化是安全且非常有用的。

它允许程序员使用空类来表示非常简单的概念，而无需额外开销。一些当前的编译器提供了这种“空基类优化”。