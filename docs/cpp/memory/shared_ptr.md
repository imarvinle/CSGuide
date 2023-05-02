# 深入理解shared_ptr 之手写

**面试高频指数：★★★★☆**

正如这篇文章 [智能指针](https://www.yuque.com/csguide/cf15wf/qpcemwfid8l5xoda) 所说，智能指针是一种可以自动管理内存的指针，它可以在不需要手动释放内存的情况下，确保对象被正确地销毁。

可以显著降低程序中的内存泄漏和悬空指针的风险。

而用得比较多的一种智能指针就是 shared_ptr ，从名字也可以看出来，shared 强调分享，也就是指针的所有权不是独占。

#### shared_ptr 的使用

`shared_ptr` 的一个关键特性是可以共享所有权，即多个 `shared_ptr` 可以同时指向并拥有同一个对象。

当最后一个拥有该对象的 `shared_ptr` 被销毁或者释放该对象的所有权时，对象会自动被删除。

这种行为通过**引用计数**实现，即 `shared_ptr` 有一个成员变量记录有多少个 `shared_ptr` 共享同一个对象。

 `shared_ptr` 的简单用法示例：

```cpp
#include <iostream>
#include <memory>

class MyClass {
public:
    MyClass() { std::cout << "MyClass 构造函数\n"; }
    ~MyClass() { std::cout << "MyClass 析构函数\n"; }
    void do_something() { std::cout << "MyClass::do_something() 被调用\n"; }
};

int main() {
    {
        std::shared_ptr<MyClass> ptr1 = std::make_shared<MyClass>();
        {
            std::shared_ptr<MyClass> ptr2 = ptr1; // 这里共享 MyClass 对象的所有权
            ptr1->do_something();
            ptr2->do_something();
            std::cout << "ptr1 和 ptr2 作用域结束前的引用计数: " << ptr1.use_count() << std::endl;
        } // 这里 ptr2 被销毁，但是 MyClass 对象不会被删除，因为 ptr1 仍然拥有它的所有权
        std::cout << "ptr1 作用域结束前的引用计数: " << ptr1.use_count() << std::endl;
    } // 这里 ptr1 被销毁，同时 MyClass 对象也会被删除，因为它是最后一个拥有对象所有权的 shared_ptr

    return 0;
}
```

输出结果:

```cpp
MyClass 构造函数
MyClass::do_something() 被调用
MyClass::do_something() 被调用
ptr1 和 ptr2 作用域结束前的引用计数: 2
ptr1 作用域结束前的引用计数: 1
MyClass 析构函数
```


####  shared_ptr 的 double free 问题

double free 问题就是一块内存空间或者资源被释放两次。
那么为什么会释放两次呢？double free 可能是下面这些原因造成的：
* 直接使用原始指针创建多个 shared_ptr，而没有使用 shared_ptr 的 make_shared 工厂函数，从而导致多个独立的引用计数。
* 循环引用，即两个或多个 shared_ptr 互相引用，导致引用计数永远无法降为零，从而无法释放内存。

解决 shared_ptr double free 问题的方法：
* 使用 make_shared 函数创建 shared_ptr 实例，而不是直接使用原始指针。这样可以确保所有 shared_ptr 实例共享相同的引用计数。
* 对于可能产生循环引用的情况，使用 weak_ptr。weak_ptr 是一种不控制对象生命周期的智能指针，它只观察对象，而不增加引用计数。这可以避免循环引用导致的内存泄漏问题。关于 weak_ptr 的更多知识可以看这篇: [深入理解weak_ptr](https://www.yuque.com/csguide/cf15wf/vl2ldvm7ksbm86s8)
```cpp
#include <iostream>
#include <memory>
class B; // 前向声明
class A {
public:
    std::shared_ptr<B> b_ptr;
    ~A() {
        std::cout << "A destructor called" << std::endl;
    }
};

class B {
public:
    std::shared_ptr<A> a_ptr;
    ~B() {
        std::cout << "B destructor called" << std::endl;
    }
};

int main() {
    {
        std::shared_ptr<A> a = std::make_shared<A>();
        std::shared_ptr<B> b = std::make_shared<B>();
        a->b_ptr = b; // A 指向 B
        b->a_ptr = a; // B 指向 A
    } // a 和 b 离开作用域，但由于循环引用，它们的析构函数不会被调用

    std::cout << "End of main" << std::endl;
    return 0;
}
```
上面这种循环引用问题可以使用std::weak_ptr来避免循环引用。
std::weak_ptr不会增加所指向对象的引用计数，因此不会导致循环引用。
下面👇这个代码就解决了循环引用问题：
```cpp
#include <iostream>
#include <memory>

class B; // 前向声明

class A {
public:
    std::shared_ptr<B> b_ptr;
    ~A() {
        std::cout << "A destructor called" << std::endl;
    }
};

class B {
public:
    std::weak_ptr<A> a_ptr; // 使用 weak_ptr 替代 shared_ptr
    ~B() {
        std::cout << "B destructor called" << std::endl;
    }
};

int main() {
    {
        std::shared_ptr<A> a = std::make_shared<A>();
        std::shared_ptr<B> b = std::make_shared<B>();
        a->b_ptr = b; // A 指向 B
        b->a_ptr = a; // B 对 A 使用 weak_ptr
    } // a 和 b 离开作用域，它们的析构函数会被正确调用

    std::cout << "End of main" << std::endl;
    return 0;
}
```
但是使用 weak_ptr 也有几点注意事项:
* 如果需要访问 weak_ptr 所指向的对象，需要将std::weak_ptr 通过 weak_ptr::lock() 临时转换为std::shared_ptr.
* 在使用lock()方法之前，应当检查使用 std::weak_ptr::expired() 检查 std::weak_ptr是否有效，即它所指向的对象是否仍然存在。

#### enable_shared_from_this 
从名字可以看出几个关键词：enable: 允许  shared 指 shared_ptr, from_this 则是指从类自身this 构造 shared_ptr。

想象这样一个场景:
```cpp
struct SomeData;
void SomeAPI(const std::shared_ptr<SomeData>& d) {}

struct SomeData {
    void NeedCallSomeAPI() {
        // 需要用this调用SomeAPI
    }
};
```
上面这段代码需要在NeedCallSomeAPI函数中调用SomeAPI，而SomeAPI需要的是一个std::shared_ptr<SomeData>的实参。这个时候应该怎么做？ 这样吗？
```cpp
struct SomeData {
    void NeedCallSomeAPI() {
        SomeAPI(std::shared_ptr<SomeData>{this});
    }
};
```
上面的做法是错误的，因为SomeAPI调用结束后std::shared_ptr<SomeData>对象的引用计数会降为0，导致 this 被意外释放。

这种情况下，我们需要使用std::enable_shared_from_this ，使用方法很简单，只需要让SomeData继承std::enable_shared_from_this<SomeData>，然后调用shared_from_this，例如：
```cpp
#include <memory>

struct SomeData;
void SomeAPI(const std::shared_ptr<SomeData>& d) {}

struct SomeData:std::enable_shared_from_this<SomeData> {
    static std::shared_ptr<SomeData> Create() {
        return std::shared_ptr<SomeData>(new SomeData);
    }
    void NeedCallSomeAPI() {
        SomeAPI(shared_from_this());
    }
private:
    SomeData() {}
};


int main()
{
    auto d{ SomeData::Create() };
    d->NeedCallSomeAPI();
}
```

总结一下，当下面👇这些场景用到 shared_ptr 时，需要搭配上 enable_shared_from_this:

1. 当你需要将`this`指针传递给其他函数或方法，而这些函数或方法需要一个`std::shared_ptr`，而不是裸指针。
2. 当你需要在类的成员函数内部创建指向当前对象的`std::shared_ptr`，例如在回调函数或事件处理中。

> 关于 enable_shared_from_this 的更多原理可以看一下这篇博客: https://0cch.com/2020/08/05/something-about-enable_shared_from_this/


#### 线程安全性

另外，注意智能指针都不是线程安全的，在多线程环境中使用智能指针时，需要采取额外的措施来确保线程安全。

如互斥锁（`std::mutex`）或原子操作（`std::atomic`），来确保线程安全。

#### 手写 shared_ptr

这是 C++ 面试常考的一个环节，有的会让你说实现思路，有的则直接需要手写一个。

刚才上面说过了，shared_ptr 的关键就是在于 **引用计数**。

要实现一个简化版本的 `shared_ptr`，需要考虑以下几点：

1. 在智能指针类中存储裸指针（raw pointer）和引用计数。
2. 在构造函数中为裸指针和引用计数分配内存。
3. 在拷贝构造函数和赋值操作符中正确地更新引用计数。
4. 在析构函数中递减引用计数，并在引用计数为零时删除对象和引用计数。

以下是一个简化版的 `shared_ptr` 的实现：

```cpp
#include <iostream>

template <typename T>
class SimpleSharedPtr {
public:
    // 构造函数
    explicit SimpleSharedPtr(T* ptr = nullptr) : ptr_(ptr), count_(ptr ? new size_t(1) : nullptr) {}

    // 拷贝构造函数
    SimpleSharedPtr(const SimpleSharedPtr& other) : ptr_(other.ptr_), count_(other.count_) {
        if (count_) {
            ++(*count_);
        }
    }

    // 赋值操作符
    SimpleSharedPtr& operator=(const SimpleSharedPtr& other) {
        if (this != &other) {
            release();
            ptr_ = other.ptr_;
            count_ = other.count_;
            if (count_) {
                ++(*count_);
            }
        }
        return *this;
    }

    // 析构函数
    ~SimpleSharedPtr() {
        release();
    }

    T& operator*() const { return *ptr_; }
    T* operator->() const { return ptr_; }
    T* get() const { return ptr_; }
    size_t use_count() const { return count_ ? *count_ : 0; }

private:
    void release() {
        if (count_ && --(*count_) == 0) {
            delete ptr_;
            delete count_;
        }
    }

    T* ptr_;
    size_t* count_;
};

class MyClass {
public:
    MyClass() { std::cout << "MyClass 构造函数\n"; }
    ~MyClass() { std::cout << "MyClass 析构函数\n"; }
    void do_something() { std::cout << "MyClass::do_something() 被调用\n"; }
};

int main() {
    {
        SimpleSharedPtr<MyClass> ptr1(new MyClass());
        {
            SimpleSharedPtr<MyClass> ptr2 = ptr1;
            ptr1->do_something();
            ptr2->do_something();
            std::cout << "引用计数: " << ptr1.use_count() << std::endl;
        }
        std::cout << "引用计数: " << ptr1.use_count() << std::endl;
    }

    return 0;
}
```

这个简化版的 `shared_ptr` 实现了基本的共享所有权和引用计数功能，方便我们学习了解原理。

但是这个实现并不完整，它缺少了很多 `std::shared_ptr` 的高级功能，例如自定义删除器、`std::make_shared()` 和 `std::allocate_shared()` 函数以及线程安全性等。

在实际项目中，还是使用 C++ 标准库提供的 `std::shared_ptr`。

**建议大家学习掌握 shared_ptr 思想之后，自己也手动写一遍，这样印象才深刻~**

#### shared_ptr 常用 API

`std::shared_ptr` 提供了许多有用的 API，以下是一些常用的 API：

* `shared_ptr<T>` 构造函数：创建一个空的 `shared_ptr`，不指向任何对象。

```cpp
std::shared_ptr<int> ptr;
```

* `make_shared<T>(args...)`：创建一个 `shared_ptr`，并在单次内存分配中同时创建对象和控制块。这比直接使用 `shared_ptr` 的构造函数要高效。

```cpp
std::shared_ptr<int> ptr = std::make_shared<int>(42);
```

* `reset()`：释放当前 `shared_ptr` 的所有权，将其设置为 `nullptr`。如果当前 `shared_ptr` 是最后一个拥有对象所有权的智能指针，则会删除对象。

```cpp
ptr.reset();
```

* `reset(T*)`：释放当前 `shared_ptr` 的所有权，并使其指向新的对象。如果当前 `shared_ptr` 是最后一个拥有对象所有权的智能指针，则会删除原对象。

```cpp
ptr.reset(new int(42));
```

* `get()`：返回指向的对象的裸指针。注意，这个裸指针的生命周期由 `shared_ptr` 管理，你不应该使用它来创建另一个智能指针。

```cpp
int* raw_ptr = ptr.get();
```

* `operator*` 和 `operator->`：访问指向的对象。

```cpp
int value = *ptr;
std::shared_ptr<std::vector<int>> vec_ptr = std::make_shared<std::vector<int>>();
vec_ptr->push_back(42);
```

* `use_count()`：返回当前 `shared_ptr` 的引用计数，即有多少个 `shared_ptr` 共享同一个对象。注意，`use_count()` 通常用于调试，不应该用于程序逻辑。

```cpp
size_t count = ptr.use_count();
```

* `unique()`：检查当前 `shared_ptr` 是否是唯一拥有对象所有权的智能指针。等价于 `use_count() == 1`。

```cpp
bool is_unique = ptr.unique();
```

* `swap(shared_ptr&)`：交换两个 `shared_ptr` 的内容。

```cpp
std::shared_ptr<int> ptr1 = std::make_shared<int>(42);
std::shared_ptr<int> ptr2 = std::make_shared<int>(24);
ptr1.swap(ptr2);
```

* `operator bool()`：将 `shared_ptr` 隐式转换为 `bool` 类型，用于检查其是否为空。

```cpp
if (ptr) {
    std::cout << "ptr 不为空" << std::endl;
} else {
    std::cout << "ptr 为空" << std::endl;
}
```

这些都是非常常用的 API，大家可以练习掌握。