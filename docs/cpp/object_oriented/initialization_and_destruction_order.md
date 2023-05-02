# C++ 类对象的初始化和析构顺序

**面试高频指数：★★★★☆**

整体上来说，在C++中，类对象的初始化顺序遵循以下规则：

1. 基类初始化顺序：如果当前类继承自一个或多个基类，它们将按照声明顺序进行初始化，但是在有虚继承和一般继承存在的情况下，优先虚继承。比如虚继承：`class MyClass : public Base1, public virtual Base2`，此时应当先调用 Base2 的构造函数，再调用 Base1 的构造函数。
2. 成员变量初始化顺序：类的成员变量按照它们在类定义中的声明顺序进行初始化（这里一定要注意，成员变量的初始化顺序只与声明的顺序有关！！）。
3. 构造函数执行：在基类和成员变量初始化完成后，执行类的构造函数。

举个例子:

```cpp
#include <iostream>

class Base {
public:
    Base() { std::cout << "Base constructor" << std::endl; }
    ~Base() {
        std::cout << "Base destructor" << std::endl;
    }
};

class Base1 {
public:
    Base1() { std::cout << "Base1 constructor" << std::endl; }
    ~Base1() {
        std::cout << "Base1 destructor" << std::endl;
    }
};

class Base2 {
public:
    Base2() { std::cout << "Base2 constructor" << std::endl; }
    ~Base2() {
        std::cout << "Base2 destructor" << std::endl;
    }
};

class Base3 {
public:
    Base3() { std::cout << "Base3 constructor" << std::endl; }
    ~Base3() {
        std::cout << "Base3 destructor" << std::endl;
    }
};

class MyClass : public virtual Base3, public Base1, public virtual Base2 {
public:
    MyClass() : num1(1), num2(2) {
        std::cout << "MyClass constructor" << std::endl;
    }
    ~MyClass() {
        std::cout << "MyClass destructor" << std::endl;
    }

private:
    int num1;
    int num2;
    // 这个是为了看成员变量的初始化顺序
    Base base;
};

int main() {
    MyClass obj;
    return 0;
}
```

输出结果:

```cpp
Base3 constructor  // 虚继承排第一
Base2 constructor  // 虚继承优先
Base1 constructor  // 普通基类
Base constructor   // 成员函数构造
MyClass constructor // 构造函数
```

> 关于虚继承实际用得比较少，感兴趣的可以阅读以下这篇文章: https://www.jianshu.com/p/ab96f88e5285

#### 类的析构顺序

记住一点即可，类的析构顺序和构造顺序完全相反

示例:

```cpp
#include <iostream>

class Base {
public:
    Base() { std::cout << "Base constructor" << std::endl; }
    ~Base() {
        std::cout << "Base destructor" << std::endl;
    }
};

class Base1 {
public:
    Base1() { std::cout << "Base1 constructor" << std::endl; }
    ~Base1() {
        std::cout << "Base1 destructor" << std::endl;
    }
};

class Base2 {
public:
    Base2() { std::cout << "Base2 constructor" << std::endl; }
    ~Base2() {
        std::cout << "Base2 destructor" << std::endl;
    }
};

class Base3 {
public:
    Base3() { std::cout << "Base3 constructor" << std::endl; }
    ~Base3() {
        std::cout << "Base3 destructor" << std::endl;
    }
};

class MyClass : public virtual Base3, public Base1, public virtual Base2 {
public:
    MyClass() : num1(1), num2(2) {
        std::cout << "MyClass constructor" << std::endl;
    }
    ~MyClass() {
        std::cout << "MyClass destructor" << std::endl;
    }

private:
    int num1;
    int num2;
    Base base;
};

int main() {
    MyClass obj;
    return 0;
}

```

输出结果:

```cpp
Base3 constructor
Base2 constructor
Base1 constructor
Base constructor
MyClass constructor
MyClass destructor
Base destructor
Base1 destructor
Base2 destructor
Base3 destructor
```

