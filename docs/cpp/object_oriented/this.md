# this 指针

**面试高频指数：★★★★☆**

1. `this` 是一个指向当前对象的指针。
2. 在面向对象的编程语言中，都存在`this`指针这种机制， Java、C++ 叫 `this`，而 Python 中叫 `self`。
3. 当我们在类的成员函数中访问类的成员变量或调用成员函数时，编译器会隐式地将当前对象的地址作为 `this` 指针传递给成员函数。
    因此，`this` 指针可以用来访问类的成员变量和成员函数，以及在成员函数中引用当前对象。
#### 例子: 
```cpp
#include <iostream>
class MyClass {
public:
    MyClass(int value) : value_(value) {}

    // 成员函数中使用 this 指针访问成员变量
    void printValue() const {
        std::cout << "Value: " << this->value_ << std::endl;
    }

    // 使用 this 指针实现链式调用
    MyClass& setValue(int value) {
        this->value_ = value;
        return *this; // 返回当前对象的引用
    }

private:
    int value_;
};

int main() {
    MyClass obj(10);
    obj.printValue(); // 输出 "Value: 10"

    // 链式调用 setValue 函数
    obj.setValue(20).setValue(30);
    obj.printValue(); // 输出 "Value: 30"

    return 0;
}

```
需要注意的是，在常量成员函数（const member function）中，`this` 指针的类型是指向常量对象的常量指针（const pointer to const object），因此不能用来修改成员变量的值。
#### static 函数不能访问成员变量
在C++中，static 函数是一种静态成员函数，它与类本身相关，而不是与类的对象相关。
大家可以将 static 函数视为在类作用域下的全局函数，而非成员函数。
因为静态函数没有 this 指针，所以它不能访问任何非静态成员变量。
如果在静态函数中尝试访问非静态成员变量，编译器会报错。
下面这个示例代码，说明了静态函数不能访问非静态成员变量：

```cpp
class MyClass {
public:
    static void myStaticFunction() {
        // 以下代码会导致编译错误
        // cout << this->myMemberVariable << endl;
    }
private:
    int myMemberVariable;
};
```
在上面的示例中，myStaticFunction是一个静态函数，它尝试使用this指针来访问非静态成员变量myMemberVariable，但这会导致编译错误。

#### 总结：
this 实际上是成员函数的一个形参，在调用成员函数时将对象的地址作为实参传递给 this。

不过 this 这个形参是隐式的，它并不出现在代码中，而是在编译阶段由编译器默默地将它添加到参数列表中。
this 作为隐式形参，本质上是成员函数的局部变量，所以只能用在成员函数的内部，并且只有在通过对象调用成员函数时才给 this 赋值。
成员函数最终被编译成与对象无关的普通函数，除了成员变量，会丢失所有信息，所以编译时要在成员函数中添加一个额外的参数，把当前对象的首地址传入，以此来关联成员函数和成员变量。

**这个额外的参数，实际上就是 this，它是成员函数和成员变量关联的桥梁。**