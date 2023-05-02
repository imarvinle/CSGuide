# RAII 思想

**面试高频指数：★★★★☆**

#### 什么是 RAII

这里我们引用cppreference 的定义: [RAII](https://en.cppreference.com/w/cpp/language/raii)

资源获取即初始化（Resource Acquisition Is Initialization，简称 RAII）是一种 C++ 编程技术，它将在使用前获取（分配的堆内存、执行线程、打开的套接字、打开的文件、锁定的互斥量、磁盘空间、数据库连接等有限资源）的资源的生命周期与某个对象的生命周期绑定在一起。

确保在控制对象的生命周期结束时，按照资源获取的相反顺序释放所有资源。

同样，如果资源获取失败（构造函数退出并带有异常），则按照初始化的相反顺序释放所有已完全构造的成员和基类子对象所获取的资源。

这利用了核心语言特性（对象生命周期、作用域退出、初始化顺序和堆栈展开），以消除资源泄漏并确保异常安全。

#### RAII 的原理

 RAII的核心思想就是：

**利用栈上局部变量的自动析构来保证资源一定会被释放**。

因为我们平常 C++ 编程过程中，经常会忘了释放资源，比如申请的堆内存忘了手动释放，那么就会导致内存泄露。

还有一些常见是程序遇到了异常，提前终止了，我们的资源也来不及释放。

但是变量的析构函数的调用是由编译器保证的一定会被执行，所以如果资源的获取和释放与对象的构造和析构绑定在一起，就不会有各种资源泄露问题。

#### RAII 类实现步骤

一般设计实现一个 RAII 类需要四个步骤:

* 设计一个类封装资源，资源可以是内存、文件、socket、锁等等一切

* 在构造函数中执行资源的初始化，比如申请内存、打开文件、申请锁

* 在析构函数中执行销毁操作，比如释放内存、关闭文件、释放锁
* 使用时声明一个该对象的类，一般在你希望的作用域声明即可，比如在函数开始，或者作为类的成员变量

#### 示例

下面写一个 RAII 示例，用来演示使用 RAII 思想包装文件的操作，假设我们需要在程序中使用一个文件：

```cpp
#include <iostream>
#include <fstream>

int main() {
    std::ifstream myfile("example.txt"); // 换自己的文件路径
    if (myfile.is_open()) {
        std::cout << "File is opened." << std::endl;
        // do some work with the file
    }
    else {
        std::cout << "Failed to open the file." << std::endl;
    }
    myfile.close();
    return 0;
}
```

上面这个例子中，我们手动打开和关闭了文件。

如果在程序执行的过程中发生了异常或者程序提前退出，可能会导致文件没有被关闭，从而产生资源未释放等问题。

现在使用 RAII 来改进这个例子，通过定义一个包含文件句柄的类，在类的构造函数中打开文件，在析构函数中关闭文件：

```cpp
#include <iostream>
#include <fstream>

class File {
public:
    File(const char* filename) : m_handle(std::ifstream(filename)) {}
    ~File() {
        if (m_handle.is_open()) {
            std::cout << "File is closed." << std::endl;
            m_handle.close();
        }
    }

    std::ifstream& getHandle() {
        return m_handle;
    }

private:
    std::ifstream m_handle;
};

int main() {
    File myfile("example.txt");
    if (myfile.getHandle().is_open()) {
        std::cout << "File is opened." << std::endl;
        // do some work with the file
    }
    else {
        std::cout << "Failed to open the file." << std::endl;
    }
    return 0;
}

```

这样，在程序退出时，File类的析构函数会自动被调用，从而自动关闭文件，即使程序提前退出或者发生异常，也不会产生内存泄漏等问题。



#### 用 RAII 思想包装 mutex

在 C++ 中，可以使用 RAII 思想来包装 mutex，确保在多线程编程中始终能安全锁定和解锁互斥量。

```cpp
#include <iostream>
#include <mutex>
#include <thread>

class LockGuard {
public:
    explicit LockGuard(std::mutex &mtx) : mutex_(mtx) {
        mutex_.lock();
    }

    ~LockGuard() {
        mutex_.unlock();
    }

    // 禁止复制
    LockGuard(const LockGuard &) = delete;
    LockGuard &operator=(const LockGuard &) = delete;

private:
    std::mutex &mutex_;
};

// 互斥量
std::mutex mtx;
// 多线程操作的变量
int shared_data = 0;

void increment() {
    for (int i = 0; i < 10000; ++i) {
        // 申请锁
        LockGuard lock(mtx);
        ++shared_data;
        // 作用域结束后会析构 然后释放锁
    }
}

int main() {
    std::thread t1(increment);
    std::thread t2(increment);

    t1.join();
    t2.join();

    std::cout << "Shared data: " << shared_data << std::endl;

    return 0;
}

```

上面定义了一个 `LockGuard` 类，该类在构造函数中接收一个互斥量（mutex）引用并对其进行锁定，在析构函数中对互斥量进行解锁。这样，我们可以将互斥量传递给 `LockGuard` 对象，并在需要保护的代码块内创建该对象，确保在执行保护代码期间始终正确锁定和解锁互斥量。

在 main 函数中，用两个线程同时更新一个共享变量，通过 RAII 包装的 `LockGuard` 确保互斥量的正确使用。

#### 总结

RAII（**R**esource **A**cquisition **I**s **I**nitialization）是由 C++ 之父Bjarne Stroustrup提出的，中文翻译为**资源获取即初始化**，

他说：使用局部对象来管理资源的技术称为资源获取即初始化；

这里的资源主要是指操作系统中有限的东西如指针内存、网络套接字、文件等等，局部对象是指存储在栈的对象，它的生命周期是由操作系统来管理的，无需人工介入；