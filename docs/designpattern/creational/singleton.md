# 单例模式

## 一、什么是单例模式

单例模式是一种创建型设计模式， 核心在于一个类只有一个实例， 并且提供一个访问该实例的全局节点（可以理解为全局变量）。

这样，在整个应用程序中，这个类的实例可以轻松访问并进行操作。

有两个关键的点：

* 一个类只有一个实例
* 提供访问该实例的全局节点

但是一个普通类往往可以构造出很多个实例，单例模式实现的常见的方法就是将**类的构造函数私有化，禁止外部直接构造对象**，然后该类对外提供一个获取唯一实例的方法：
```GetInstance```，这个方法内部先判断是否已经构造有了该实例，有的话，直接取出返回，没有就调用私有化的构造函数实例化一个。


## 二、单例模式的应用场景

在实际开发过程中，常常会有一些数据在系统中应该只存在一份或者一个实例即可，比如系统配置信息类、数据库连接池、日志管理器等等。

单例模式有助于减少系统资源的开销，提高效率，并确保资源的一致性。

然而，在使用单例模式时，需要注意线程安全问题，特别是在多线程环境下，后面我们也会提到解决方案。

### 2.1 案例: 日志记录器

举个例子，比如我们的服务器程序中，需要将运行日志写入 run.log 文件中。多个模块、组件、线程可能会同时尝试写入日志。

如果没有适当的同步机制，这可能导致文件内容混乱或损坏。

比如下面这样:

```cpp
#include <iostream>
#include <fstream>
#include <thread>

class SimpleLogger {
public:
    SimpleLogger() {
        logFile.open("run.log", std::ios_base::app);
        if (!logFile.is_open()) {
            throw std::runtime_error("Unable to open log file.");
        }
    }

    void writeLog(const std::string& message) {
        logFile << message << std::endl;
    }

private:
    std::ofstream logFile;
};

int main() {
    SimpleLogger logger;

    // 创建多个线程，同时执行写入日志操作
    std::thread t1(&SimpleLogger::writeLog, &logger, "Log message from thread 1");
    std::thread t2(&SimpleLogger::writeLog, &logger, "Log message from thread 2");
    std::thread t3(&SimpleLogger::writeLog, &logger, "Log message from thread 3");

    // 等待所有线程执行完毕
    t1.join();
    t2.join();
    t3.join();

    return 0;
}
```
这个代码有什么问题呢？


由于没有适当的同步机制，多个线程同时写入可能会导致日志内容混乱。

那大家可能说加锁不就行啦？

可以拿我们来看下加个锁是什么情况:

```cpp
#include <iostream>
#include <fstream>
#include <thread>
#include <mutex>

class SimpleLogger {
public:
    SimpleLogger() {
        logFile.open("run.log", std::ios_base::app);
        if (!logFile.is_open()) {
            throw std::runtime_error("Unable to open log file.");
        }
    }

    void writeLog(const std::string& message) {
        std::unique_lock<std::mutex> lock(logMutex);
        logFile << message << std::endl;
        lock.unlock();
    }

private:
    std::ofstream logFile;
    std::mutex logMutex;
};

int main() {
    SimpleLogger logger;

    // 创建多个线程，同时执行写入日志操作
    std::thread t1(&SimpleLogger::writeLog, &logger, "Log message from thread 1");
    std::thread t2(&SimpleLogger::writeLog, &logger, "Log message from thread 2");
    std::thread t3(&SimpleLogger::writeLog, &logger, "Log message from thread 3");

    // 等待所有线程执行完毕
    t1.join();
    t2.join();
    t3.join();

    return 0;
}
```
上面这个代码中，的确是解决了问题，但这是因为三个线程都使用的 logger 这一个对象，所以没问题。

但是如果还有其它线程实例化了其它 SimpleLogger 对象呢？

那就还是有并发问题，因为这个 mutex 只能在一个 SimpleLogger 对象内起作用，多个 SimpleLogger 对象之间是不同的 mutex。

那要解决这个问题，就要保证所有线程使用的 logger 都是同一个对象，这就是**单例**。

这本质是一个资源所有权的问题，因为我们只有一个 run.log 文件，所以理论上只需要一个 logger 拥有这个文件读写入口就可以了。

以下是这个问题的单例解决办法:

```cpp
#include <iostream>
#include <fstream>
#include <thread>
#include <mutex>

class SingletonLogger {
public:
    // 禁止拷贝
    SingletonLogger(const SingletonLogger&) = delete;
    SingletonLogger& operator=(const SingletonLogger&) = delete;

    // 获取单例实例的静态方法
    static SingletonLogger& getInstance() {
        static SingletonLogger instance;
        return instance;
    }

    // 写入日志
    void writeLog(const std::string& message) {
        std::unique_lock<std::mutex> lock(logMutex);
        logFile << message << std::endl;
        lock.unlock();
    }

private:
    // 私有构造函数
    SingletonLogger() {
        logFile.open("log.txt", std::ios_base::app);
        if (!logFile.is_open()) {
            throw std::runtime_error("Unable to open log file.");
        }
    }

    // 日志文件
    std::ofstream logFile;
    // 互斥锁
    std::mutex logMutex;
};

int main() {

      SimpleLogger logger =  SingletonLogger::getInstance();

    // 创建多个线程，同时执行写入日志操作
    std::thread t1(&SimpleLogger::writeLog, &logger, "Log message from thread 1");
    std::thread t2(&SimpleLogger::writeLog, &logger, "Log message from thread 2");
    std::thread t3(&SimpleLogger::writeLog, &logger, "Log message from thread 3");

    // 等待所有线程执行完毕
    t1.join();
    t2.join();
    t3.join();

    return 0;
}
```

上面代码通过将构造函数设置为私有，防止了外部实例化。

并且提供了一个静态方法getInstance()来获取唯一的SingletonLogger实例。

此外，我们在writeLog方法内使用互斥锁logMutex保证线程安全。

这样就确保了整个应用程序中只有一个日志记录器实例，同时解决了资源冲突问题。

总结一下单例的常见结构吧：

## 三、单例模式的结构

![单例模式](https://cdn.how2cs.cn/csguide/160456.png)


### 3.1 如何实现单例

根据上面图片，要实现一个单例，需要考虑如下几点：

* 构造函数私有化，禁止外部构造实例
* 对象创建时的线程安全问题
* 是否支持延迟加载；

在这里给大家介绍几种，经典的单例实现方法

## 四、经典的单例实现方法

### 4.1 饿汉式


饿汉式单例是在类加载时就创建实例，避免了多线程同步问题，这种方法不支持延迟加载，对于占用大量资源或者初始化耗时长的场景，可能会导致资源浪费。

> 优点

线程安全

> 缺点

不支持延迟加载，可能存在资源浪费

> 实现原理

都是利用类加载时创建实例，C++、Java 中都是利用声明 static 变量实现，因为 static 变量 在 C++ 中是在全局初始化时被创建。

#### Java 语言

```java
public class Singleton {
    // 私有静态实例
    private static Singleton instance = new Singleton();

    // 私有构造函数
    private Singleton() {
        System.out.println("Singleton constructor called");
    }

    // 静态方法返回单例实例
    public static Singleton getInstance() {
        return instance;
    }

    public void doSomething() {
        System.out.println("Do something in Singleton");
    }

    public static void main(String[] args) {
        Singleton singleton = Singleton.getInstance();
        singleton.doSomething();
    }
}
```

#### C++实现

```cpp
#include <iostream>

class Singleton {
public:
    // 获取单例实例的静态方法
    static Singleton& getInstance() {
        return instance;
    }

    // 禁止拷贝
    Singleton(const Singleton&) = delete;
    Singleton& operator=(const Singleton&) = delete;

    void doSomething() {
        std::cout << "Do something in Singleton" << std::endl;
    }

private:
    // 私有构造函数
    Singleton() {
        std::cout << "Singleton constructor called" << std::endl;
    }

    // 单例实例
    static Singleton instance;
};

// 在类外初始化静态成员变量
Singleton Singleton::instance;

int main() {
    Singleton& singleton = Singleton::getInstance();
    singleton.doSomething();

    return 0;
}
```

### 4.2 懒汉式

与饿汉式相反的就是懒汉式单例，

何为懒？

就是等到需要时才创建实例，这样可以避免提前初始化导致的资源浪费，但存在多线程环境下的线程安全问题。

优缺点恰恰和饿汉式相反。


> 优点

避免资源浪费，按需加载

> 缺点

存在线程安全问题，需要加锁，并发度降低

> 实现原理


#### Java实现

以下是一个Java语言的懒汉式单例实现：

```java
public class Singleton {
    // 私有静态实例变量
    private static Singleton instance;

    // 私有构造函数
    private Singleton() {
        System.out.println("Singleton constructor called");
    }

    // 公有静态方法获取单例实例，使用 synchronized 关键字实现线程安全
    public static synchronized Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }

    public void doSomething() {
        System.out.println("Do something in Singleton");
    }

    public static void main(String[] args) {
        Singleton singleton = Singleton.getInstance();
        singleton.doSomething();
    }
}
```

getInstance()方法中，会先判断 instance 是否已经被创建，如果没有则创建，否则直接返回instance。

为了保证线程安全，还需要使用 synchronized 关键字，避免多个线程同时访问getInstance()方法导致的多次实例化问题。

synchronized关键字实现线程安全会带来性能损失，如果对性能有较高的要求，可以考虑双重检查锁定（DCL）或静态内部类实现，后面我们都会一一提到。

#### C++ 实现

```cpp
#include <iostream>
#include <mutex>

class Singleton {
public:
    // 获取单例实例的静态方法
    static Singleton& getInstance() {
        // 使用双重检查锁定实现线程安全
        if (instance == nullptr) {
            std::lock_guard<std::mutex> lock(mutex_);
            if (instance == nullptr) {
                instance = new Singleton();
            }
        }
        return *instance;
    }

    // 禁止拷贝
    Singleton(const Singleton&) = delete;
    Singleton& operator=(const Singleton&) = delete;

    void doSomething() {
        std::cout << "Do something in Singleton" << std::endl;
    }

private:
    // 私有构造函数
    Singleton() {
        std::cout << "Singleton constructor called" << std::endl;
    }

    // 私有静态成员变量
    static Singleton* instance;

    // 互斥锁
    static std::mutex mutex_;
};

// 在类外初始化静态成员变量
Singleton* Singleton::instance = nullptr;
std::mutex Singleton::mutex_;

int main() {
    Singleton& singleton = Singleton::getInstance();
    singleton.doSomething();

    return 0;
}
```

和 Java 类似，都使用了 static 变量，这里使用 mutex 来做并发控制。


### 4.3 双重检测


刚才我们说到饿汉式不支持延迟加载，存在资源浪费；懒汉式存在性能问题，高并发下有瓶颈。

那么分析一下，为什么懒汉式有瓶颈呢？ 

原因在于每一次 getInstance 的时候我们都要去上锁、等待锁，实际上大多数场景，我们只有第一次请求的时候才会有并发创建问题。

一旦创建过后，后续都是读请求，不存在并发问题。

所以这就有优化空间了，双重检测的核心思想是：

**只要 instance 被创建之后，即便再调用 getInstance() 函数也不会再进入到加锁逻辑中了。**


所以，这种实现方式解决了懒汉式并发度低的问题。

>具体实现过程如下

  1. 在静态成员变量中声明一个指针类型的`instance`，并初始化为`nullptr`；
  2. 在`getInstance()`方法中，先进行一次非同步的检查，判断`instance`是否为`nullptr`，如果是，才进行同步检查；
  3. 在同步检查之前，加锁保证线程安全，然后再次判断`instance`是否为`nullptr`；
  4. 如果`instance`为`nullptr`，则创建一个单例实例，赋值给`instance`指针；
  5. 最后释放锁并返回`instance`指针。

具体的代码实现如下所示：

#### C++语言的双重检查锁定（DCL）单例实现示例

```cpp
#include <iostream>
#include <mutex>

class Singleton {
public:
    // 获取单例实例的静态方法
    static Singleton& getInstance() {
        if (instance == nullptr) {
            std::lock_guard<std::mutex> lock(mutex_);
            if (instance == nullptr) {
                instance = new Singleton();
            }
        }
        return *instance;
    }

    // 禁止拷贝
    Singleton(const Singleton&) = delete;
    Singleton& operator=(const Singleton&) = delete;

    void doSomething() {
        std::cout << "Do something in Singleton" << std::endl;
    }

private:
    // 私有构造函数
    Singleton() {
        std::cout << "Singleton constructor called" << std::endl;
    }

    // 私有静态成员变量
    static Singleton* instance;

    // 互斥锁
    static std::mutex mutex_;
};

// 在类外初始化静态成员变量
Singleton* Singleton::instance = nullptr;
std::mutex Singleton::mutex_;

int main() {
    Singleton& singleton = Singleton::getInstance();
    singleton.doSomething();

    return 0;
}
```

#### Java 语言的双重检查锁定（DCL）单例实现示例

```java
public class Singleton {
    // 声明一个静态的volatile变量instance
    private static volatile Singleton instance;

    // 构造函数声明为private，避免外部实例化
    private Singleton() {}

    // 双重检测锁定实现单例模式
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized (Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

持续更新中

