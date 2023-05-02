# C++ 几种类型转换

**面试高频指数：★★★☆☆**

在 C 语言中，我们大多数是用 `(type_name) expression` 这种方式来做强制类型转换，但是在 C++ 中，更推荐使用四个转换操作符来实现显式类型转换。

#### static_cast

用法: `static_cast <new_type> (expression)`

其实 static_cast 和 C 语言 () 做强制类型转换基本是等价的。

主要用于以下场景:

##### 基本类型之间的转换

将一个基本类型转换为另一个基本类型，例如将整数转换为浮点数或将字符转换为整数。

```cpp
int a = 42;
double b = static_cast<double>(a); // 将整数a转换为双精度浮点数b
```

##### 指针类型之间的转换

将一个指针类型转换为另一个指针类型，尤其是在类层次结构中从基类指针转换为派生类指针。这种转换不执行运行时类型检查，可能不安全，要自己保证指针确实可以互相转换。

```cpp
class Base {};
class Derived : public Base {};

Base* base_ptr = new Derived();
Derived* derived_ptr = static_cast<Derived*>(base_ptr); // 将基类指针base_ptr转换为派生类指针derived_ptr
```

##### 引用类型之间的转换

类似于指针类型之间的转换，可以将一个引用类型转换为另一个引用类型。在这种情况下，也应注意安全性。

```cpp
Derived derived_obj;
Base& base_ref = derived_obj;
Derived& derived_ref = static_cast<Derived&>(base_ref); // 将基类引用base_ref转换为派生类引用derived_ref
```

`static_cast`在编译时执行类型转换，在进行指针或引用类型转换时，需要自己保证合法性。

如果想要运行时类型检查，可以使用`dynamic_cast`进行安全的向下类型转换。

#### dynamic_cast

用法: `dynamic_cast <new_type> (expression)`

`dynamic_cast`在C++中主要应用于父子类层次结构中的安全类型转换。

它在运行时执行类型检查，因此相比于`static_cast`，它更加安全。

`dynamic_cast`的主要应用场景：

##### 向下类型转换

当需要将基类指针或引用转换为派生类指针或引用时，`dynamic_cast`可以确保类型兼容性。

如果转换失败，`dynamic_cast`将返回空指针（对于指针类型）或抛出异常（对于引用类型）。

```cpp
class Base { virtual void dummy() {} };
class Derived : public Base { int a; };

Base* base_ptr = new Derived();
Derived* derived_ptr = dynamic_cast<Derived*>(base_ptr); // 将基类指针base_ptr转换为派生类指针derived_ptr，如果类型兼容，则成功
```

##### 用于多态类型检查

处理多态对象时，`dynamic_cast`可以用来确定对象的实际类型，例如：

```cpp
class Animal { public: virtual ~Animal() {} };
class Dog : public Animal { public: void bark() { /* ... */ } };
class Cat : public Animal { public: void meow() { /* ... */ } };

Animal* animal_ptr = /* ... */;

// 尝试将Animal指针转换为Dog指针
Dog* dog_ptr = dynamic_cast<Dog*>(animal_ptr);
if (dog_ptr) {
    dog_ptr->bark();
}

// 尝试将Animal指针转换为Cat指针
Cat* cat_ptr = dynamic_cast<Cat*>(animal_ptr);
if (cat_ptr) {
    cat_ptr->meow();
}
```

另外，要使用`dynamic_cast`有效，基类至少需要一个虚拟函数。

因为，`dynamic_cast`只有在基类存在虚函数(虚函数表)的情况下才有可能将基类指针转化为子类。

#### dynamic_cast 底层原理

`dynamic_cast`的底层原理依赖于运行时类型信息（RTTI, Runtime Type Information）。

C++编译器在编译时为支持多态的类生成RTTI，它包含了类的类型信息和类层次结构。

我们都知道当使用虚函数时，编译器会为每个类生成一个虚函数表（vtable），并在其中存储指向虚函数的指针。

伴随虚函数表的还有 RTTI(运行时类型信息)，这些辅助的信息可以用来帮助我们运行时识别对象的类型信息。

《深度探索C++对象模型》中有个例子：

```cpp
class Point
{
public:
	Point(float xval);
	virtual ~Point();
 
	float x() const;
	static int PointCount();
 
protected:
	virtual ostream& print(ostream& os) const;
 
	float _x;
	static int _point_count;
};
```
![](https://cdn.how2cs.cn/csguide/155928.png)
首先，每个多态对象都有一个指向其vtable的指针，称为vptr。
RTTI（就是上面图中的 type_info 结构)通常与vtable关联。
`dynamic_cast`就是利用RTTI来执行运行时类型检查和安全类型转换。
以下是`dynamic_cast`的工作原理的简化描述：

1. 首先，`dynamic_cast`通过查询对象的 vptr 来获取其RTTI（这也是为什么 dynamic_cast 要求对象有虚函数）
2. 然后，`dynamic_cast`比较请求的目标类型与从RTTI获得的实际类型。如果目标类型是实际类型或其基类，则转换成功。
3. 如果目标类型是派生类，`dynamic_cast`会检查类层次结构，以确定转换是否合法。如果在类层次结构中找到了目标类型，则转换成功；否则，转换失败。
4. 当转换成功时，`dynamic_cast`返回转换后的指针或引用。
5. 如果转换失败，对于指针类型，`dynamic_cast`返回空指针；对于引用类型，它会抛出一个`std::bad_cast`异常。

因为`dynamic_cast`依赖于运行时类型信息，它的性能可能低于其他类型转换操作（如`static_cast`），static 是编译器静态转换，编译时期就完成了。

#### const_cast
用法: `const_cast <new_type> (expression)`
new_type 必须是一个指针、引用或者指向对象类型成员的指针。

##### 修改const对象
当需要修改const对象时，可以使用`const_cast`来删除const属性。

```cpp
const int a = 42;
int* mutable_ptr = const_cast<int*>(&a); // 删除const属性，使得可以修改a的值
*mutable_ptr = 43; // 修改a的值
```

##### const对象调用非const成员函数

当需要使用const对象调用非const成员函数时，可以使用`const_cast`删除对象的const属性。

```cpp
class MyClass {
public:
    void non_const_function() { /* ... */ }
};

const MyClass my_const_obj;
MyClass* mutable_obj_ptr = const_cast<MyClass*>(&my_const_obj); // 删除const属性，使得可以调用非const成员函数
mutable_obj_ptr->non_const_function(); // 调用非const成员函数
```

不过上述行为都不是很安全，可能导致未定义的行为，因此应谨慎使用。


#### reinterpret_cast

用法: `reinterpret_cast <new_type> (expression)`

`reinterpret_cast`用于在不同类型之间进行低级别的转换。

首先从英文字面的意思理解，interpret是“解释，诠释”的意思，加上前缀“re”，就是“重新诠释”的意思；

cast 在这里可以翻译成“转型”（在侯捷大大翻译的《深度探索C++对象模型》、《Effective C++（第三版）》中，cast都被翻译成了转型），这样整个词顺下来就是“重新诠释的转型”。

它仅仅是重新解释底层比特（也就是对指针所指针的那片比特位换个类型做解释），而不进行任何类型检查。

因此，`reinterpret_cast`可能导致未定义的行为，应谨慎使用。

`reinterpret_cast`的一些典型应用场景：

##### 指针类型之间的转换

在某些情况下，需要在不同指针类型之间进行转换，如将一个`int`指针转换为`char`指针。

这在 C 语言中用的非常多，C语言中就是直接使用 () 进行强制类型转换

```cpp
int a = 42;
int* int_ptr = &a;
char* char_ptr = reinterpret_cast<char*>(int_ptr); // 将int指针转换为char指针
```

另外，这篇文章对于 reinterpret_cast 讲解得很不错，可以看一下：https://zhuanlan.zhihu.com/p/33040213

