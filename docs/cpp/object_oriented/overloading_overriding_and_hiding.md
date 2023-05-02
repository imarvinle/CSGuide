# 重载、重写、隐藏的区别

面试高频指数：**★★★☆☆**
在 C++ 中，重载（Overloading）、重写（Overriding）和隐藏（Hiding）这几个概念很容易搞混，接下来解释一下它们之间的区别：

#### 1. 重载（Overloading）:
重载是指相同作用域(比如命名空间或者同一个类)内拥有相同的方法名，但具有不同的参数类型和/或参数数量的方法。
重载允许根据所提供的参数不同来调用不同的函数。它主要在以下情况下使用：

- 方法具有相同的名称。
- 方法具有不同的参数类型或参数数量。
- 返回类型可以相同或不同。
- 同一作用域，比如都是一个类的成员函数，或者都是全局函数

例如：
```cpp
class OverloadingExample {
  void display(int a) {
    System.out.println("Display method with integer: " + a);
  }

  void display(String b) {
    System.out.println("Display method with string: " + b);
  }
}
```
#### 重写（Overriding）
重写是指在派生类中重新定义基类中的方法。

当派生类需要改变或扩展基类方法的功能时，就需要用到重写。

重写的条件包括：

- 方法具有相同的名称。
- 方法具有相同的参数类型和数量。
- 方法具有相同的返回类型。
- 重写的基类中被重写的函数必须有virtual修饰。
- **重写主要在继承关系的类之间发生。**

例如:
```cpp
#include <iostream>

class BaseClass {
public:
    virtual void display() {
        std::cout << "Display method in base class" << std::endl;
    }
};

class DerivedClass : public BaseClass {
public:
    void display() override {
        std::cout << "Display method in derived class" << std::endl;
    }
};

int main() {
    DerivedClass derived;
    derived.display();

    return 0;
}
```
#### 隐藏（Hiding）
隐藏是指派生类的函数屏蔽了与其同名的基类函数。注意只要同名函数，不管参数列表是否相同，基类函数都会被隐藏。
示例:
```cpp
#include<iostream>
using namespace std;

classA{
public:
  void fun1(int i, int j){
    cout <<"A::fun1() : " << i <<" " << j << endl;
  }
};
classB : public A{
public:
  //隐藏
  void fun1(double i){
    cout <<"B::fun1() : " << i << endl;
  }
};
int main(){
  B b;
  b.fun1(5);//调用B类中的函数
  b.fun1(1, 2);//出错，因为基类函数被隐藏
  system("pause");
  return 0;
}
```
#### 区别
##### 重载和重写的区别：
- 范围区别：重写和被重写的函数在不同的类中，重载和被重载的函数在同一类中（同一作用域）。
- 参数区别：重写与被重写的函数参数列表一定相同，重载和被重载的函数参数列表一定不同。
- virtual的区别：重写的基类必须要有virtual修饰，重载函数和被重载函数可以被virtual修饰，也可以没有。
##### 隐藏和重写，重载的区别：

- 与重载范围不同：隐藏函数和被隐藏函数在不同类中。
- 参数的区别：隐藏函数和被隐藏函数参数列表可以相同，也可以不同，但函数名一定同；当参数不同时，无论基类中的函数是否被virtual修饰，基类函数都是被隐藏，而不是被重写。

##### 示例代码:
```cpp
#include<iostream>
using namespace std;
class A{
public:
  void fun1(int i, int j){
    cout <<"A::fun1() : " << i <<" " << j << endl;
  }
  void fun2(int i){
    cout <<"A::fun2() : " << i << endl;
  }
  virtual void fun3(int i){
    cout <<"A::fun3(int) : " << i << endl;
  }
};
class B : public A{
public:
//隐藏
   void fun1(double i){
     cout <<"B::fun1() : " << i << endl;
  }
//重写
  void fun3(int i){
    cout <<"B::fun3(int) : " << i << endl;
  }
//隐藏
  void fun3(double i){
    cout <<"B::fun3(double) : " << i << endl;
  }
};

int main(){
  B b;
  A * pa = &b;
  B * pb = &b;
  pa->fun3(3);  // 重写，多态性，调用B的函数
  b.fun3(10);   // 根据参数选择调用哪个函数，可能重写也可能隐藏，调用B的函数
  pb->fun3(20); //根据参数选择调用哪个函数，可能重写也可能隐藏，调用B的函数
  system("pause");
  return 0;
}
```
输出结果:

```cpp
B::fun3(int) : 3
B::fun3(int) : 10
B::fun3(int) : 20
请按任意键继续. . .
```
>  参考: `https://blog.csdn.net/zx3517288/article/details/48976097`， `https://www.cnblogs.com/jeakeven/p/5310182.html`