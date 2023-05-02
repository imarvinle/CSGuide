# 深拷贝和浅拷贝

**面试高频指数：★★★★☆**

C++中的深拷贝和浅拷贝涉及到对象的复制。
当对象包含指针成员时，这两种拷贝方式的区别变得尤为重要。
#### 浅拷贝（Shallow Copy）
浅拷贝是一种简单的拷贝方式，它仅复制对象的基本类型成员和指针成员的值，而不复制指针所指向的内存。
这可能导致两个对象共享相同的资源，从而引发潜在的问题，如内存泄漏、意外修改共享资源等。
一般来说编译器默认帮我们实现的拷贝构造函数就是一种浅拷贝。
POD(plain old data) 类型的数据就适合浅拷贝，对于 POD的理解可以看下这篇[class和struct的区别](https://www.yuque.com/csguide/cf15wf/pltsql0qhyffzrbv)
简单来说，浅拷贝也可以理解为直接按 bit 位复制，基本等价于 memcpy()函数。
#### 深拷贝（Deep Copy）
深拷贝不仅复制对象的基本类型成员和指针成员的值，还复制指针所指向的内存。
因此，两个对象不会共享相同的资源，避免了潜在问题。
深拷贝通常需要显式实现拷贝构造函数和赋值运算符重载。

举个例子：

```cpp
#include <iostream>
#include <cstring>

class MyClass {
public:
    MyClass(const char* str) {
        data = new char[strlen(str) + 1];
        strcpy(data, str);
    }

    // 深拷贝的拷贝构造函数
    MyClass(const MyClass& other) {
        data = new char[strlen(other.data) + 1];
        strcpy(data, other.data);
    }

    // 深拷贝的赋值运算符重载
    MyClass& operator=(const MyClass& other) {
        if (this == &other) {
            return *this;
        }
        
        delete[] data;
        data = new char[strlen(other.data) + 1];
        strcpy(data, other.data);
        
        return *this;
    }
    
   void SetString(const char* str) {
     if (data != NULL) {
       delete[] data;
     }
     data = new char[strlen(str) + 1];
     strcpy(data, str);
   }
   
    ~MyClass() {
        delete[] data;
    }

    void print() {
        std::cout << data << std::endl;
    }

private:
    char* data;
};

int main() {
    MyClass obj1("Hello, World!");
    MyClass obj2 = obj1; // 深拷贝

    obj1.print(); // 输出：Hello, World!
    obj2.print(); // 输出：Hello, World!

    // 修改obj2中的数据，不会影响obj1
    obj1.SetString("Test");
    obj2.print(); // 输出：Hello, World!
    return 0;
}
```

在这个例子中，实现了一个简单的`MyClass`类，其中包含一个指向动态分配内存的指针。

在拷贝构造函数和赋值运算符重载中，实现了深拷贝。

这样，当创建一个新对象并从另一个对象拷贝数据时，两个对象不会共享相同的资源。

