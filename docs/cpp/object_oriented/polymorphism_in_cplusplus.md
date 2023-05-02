# C++多态的实现方式

**面试高频指数：★★★★☆**

C++中的多态是指同一个函数或者操作在不同的对象上有不同的表现形式。
**CPP实现多态的方法主要包括虚函数、纯虚函数和模板函数**
其中虚函数、纯虚函数实现的多态叫动态多态，模板函数、重载等实现的叫静态多态。
区分静态多态和动态多态的一个方法就是看决定所调用的具体方法是在编译期还是运行时，运行时就叫动态多态。

#### 虚函数、纯虚函数实现多态
在 CPP 中，可以使用虚函数来实现多态性。
虚函数是指在基类中声明的函数，它在派生类中可以被重写。
当我们使用基类指针或引用指向派生类对象时，通过虚函数的机制，可以调用到派生类中重写的函数，从而实现多态。
CPP 的多态必须满足两个条件：

-  **必须通过基类的指针或者引用调用虚函数**
-  **被调用的函数是虚函数，且必须完成对基类虚函数的重写**

举例说明:

```cpp
class Shape {
   public:
      virtual int area() = 0;
};

class Rectangle: public Shape {
   public:
      int area () { 
         cout << "Rectangle class area :"; 
         return (width * height); 
      }
};

class Triangle: public Shape{
   public:
      int area () { 
         cout << "Triangle class area :"; 
         return (width * height / 2); 
      }
};

int main() {
   Shape *shape;
   Rectangle rec(10,7);
   Triangle  tri(10,5);

   shape = &rec;
   shape->area();

   shape = &tri;
   shape->area();

   return 0;
}
```

#### 模板函数多态

模板函数可以根据传递参数的不同类型，自动生成相应类型的函数代码。模板函数可以用来实现多态。
举例说明：

```cpp
template <class T>
T GetMax (T a, T b) {
   return (a>b?a:b);
}

int main () {
   int i=5, j=6, k;
   long l=10, m=5, n;
   k=GetMax<int>(i,j);
   n=GetMax<long>(l,m);
   cout << k << endl;
   cout << n << endl;
   return 0;
}
```

在上面这个例子用，编译器会生成两个 GetMax 函数实例，参数类型分别是 int 和 long 类型，这种调用的函数在编译期就能确定下来的叫静态多态。
静态多态还包括了函数重载。