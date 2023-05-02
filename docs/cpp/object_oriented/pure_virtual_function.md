# 纯虚函数是什么？

**面试高频指数：★★★☆☆**

#### 定义

纯虚函数是一种在基类中声明但没有实现的虚函数。

**它的作用是定义了一种接口，这个接口需要由派生类来实现。**（PS: C++ 中没有接口，纯虚函数可以提供类似的功能

包含纯虚函数的类称为抽象类（Abstract Class）。

抽象类仅仅提供了一些接口，但是没有实现具体的功能。

作用就是制定各种接口，通过派生类来实现不同的功能，从而实现代码的复用和可扩展性。

另外，抽象类无法实例化，也就是无法创建对象。

原因很简单，纯虚函数没有函数体，不是完整的函数，无法调用，也无法为其分配内存空间。

#### 例子

举例说明下:

```cpp
#include <iostream>
using namespace std;

class Shape {
   public:
      // 纯虚函数
      virtual void draw() = 0;
};

class Circle : public Shape {
   public:
      void draw() {
         cout << "画一个圆形" << endl;
      }
};

class Square : public Shape {
   public:
      void draw() {
         cout << "画一个正方形" << endl;
      }
};

int main() {
   Circle circle;
   Square square;

   Shape *pShape1 = &circle;
   Shape *pShape2 = &square;

   pShape1->draw();
   pShape2->draw();

   return 0;
}
```

在上面的代码中，定义了一个抽象类 `Shape`，它包含了一个纯虚函数 `draw()`。

`Circle` 和 `Square` 是 `Shape` 的两个派生类，它们必须实现 `draw()` 函数，否则它们也会是一个抽象类。

在 `main()` 函数中，创建了 `Circle` 和 `Square` 的实例，并且使用指向基类 `Shape` 的指针来调用 `draw()` 函数。

由于 `Shape` 是一个抽象类，能创建 `Shape` 的实例，但是可以使用 `Shape` 类型指针来指向派生类，从而实现多态。

