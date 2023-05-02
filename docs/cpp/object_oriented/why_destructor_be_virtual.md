# 为什么基类析构函数需要是虚函数？

**面试高频指数：★★★☆☆**

#### 析构函数作用

首先我们需要知道析构函数的作用是什么。

析构函数是进行类的清理工作，比如释放内存、关闭DB链接、关闭Socket等等，

前面我们在介绍虚函数的时候就说到，为实现多态性（[C++多态](https://www.yuque.com/csguide/cf15wf/mns7rlkgder0mup8)），可以通过基类的指针或引用访问派生类的成员。

也就是说，声明一个基类指针，这个基类指针可以指向派生类对象。

#### 举例

下面我们来看一个例子：
```cpp
#include <iostream>

class Base {
public:
    // 注意，这里的析构函数没有定义为虚函数
    ~Base() {
        std::cout << "Base destructor called." << std::endl;
    }
};

class Derived : public Base {
public:
    Derived() {
        resource = new int[100]; // 分配资源
    }

    ~Derived() {
        std::cout << "Derived destructor called." << std::endl;
        delete[] resource; // 释放资源
    }

private:
    int* resource; // 存储资源的指针
};

int main() {
    Base* ptr = new Derived();
    delete ptr; // 只会调用Base的析构函数，Derived的析构函数不会被调用
    return 0;
}
```

这个例子执行的输出结果为:

```cpp
Base destructor called.
```

由于基类`Base`的析构函数没有定义为虚函数，当创建一个派生类`Derived`的对象，并通过基类指针`ptr`删除它时，只有基类`Base`的析构函数被调用（因为这里没有多态，构造多态的必要条件就是虚函数）。

派生类`Derived`的析构函数不会被调用，导致资源（这里是`resource`）没有被释放，从而产生资源泄漏。



再来看一个将基类定义为虚函数的例子:

```cpp
class Base {
public:
    virtual ~Base() {
        std::cout << "Base destructor called." << std::endl;
    }
};

class Derived : public Base {
public:
    ~Derived() {
        std::cout << "Derived destructor called." << std::endl;
    }
};

int main() {
    Base* ptr = new Derived();
    delete ptr; // 调用Derived的析构函数，然后调用Base的析构函数
    return 0;
}
```

输出结果为:

```cpp
Derived destructor called.
Base destructor called.
```

在这个例子中，基类`Base`的析构函数是虚函数，所以当删除`ptr`时，会首先调用派生类`Derived`的析构函数，然后调用基类`Base`的析构函数，这样可以确保对象被正确销毁。

#### 为什么默认的析构函数不是虚函数？

那么既然基类的析构函数如此有必要被定义成虚函数，为何类的默认析构函数却是非虚函数呢？

首先一点，语言设计者如此设计，肯定是有道理的。

原来是因为，虚函数不同于普通成员函数，当类中有虚成员函数时，类会自动进行一些额外工作。

这些额外的工作包括生成虚函数表和虚表指针，虚表指针指向虚函数表。

每个类都有自己的虚函数表，虚函数表的作用就是保存本类中虚函数的地址，我们可以把虚函数表形象地看成一个数组，这个数组的每个元素存放的就是各个虚函数的地址。（关于虚函数表可以看这篇文章[虚函数](https://www.yuque.com/csguide/cf15wf/uv8lqhv7gitd6oke)）

这样一来，就会占用额外的内存，当们定义的类不被其他类继承时，这种内存开销无疑是浪费的。

这样一说，问题就不言而喻了。当我们创建一个类时，系统默认我们不会将该类作为基类，所以就将默认的析构函数定义成非虚函数，这样就不会占用额外的内存空间。

同时，系统也相信程序开发者在定义一个基类时，会显示地将基类的析构函数定义成虚函数，此时该类才会维护虚函数表和虚表指针。

#### 零成本抽象原则

这也就是 C++ 的一个设计哲学：**zero overhead abstraction**

我的理解，所谓「零成本抽象」有两个层面的意思。

1. 不需要为没有使用到的语言特性付出代价。
2. 使用某种语言特性，不会带来运行时的代价。

放在这个地方就是，如果我们知道一个类不会被其它类继承，那么也就没必要将析构函数设置为虚函数，因为一旦引入虚函数就会引入虚表机制，这会造成额外的开销。

#### 总结

在C++中，基类的析构函数需要定义为虚函数，以确保在通过基类指针或引用删除派生类对象时，能够正确地调用派生类的析构函数，否则派生类的析构函数不会被调用，这部分资源也就并无法被释放。

将基类的析构函数定义为虚函数后，C++运行时会自动识别指向的对象的实际类型，并确保调用正确的派生类析构函数。

当派生类析构函数执行完毕后，基类的析构函数也会自动调用，以确保对象完全销毁。



>  参考 https://blog.csdn.net/qq_42247231/article/details/105109709