# 类成员访问权限

**面试高频指数：★★☆☆☆**

这个知识面试考得不多，因为太简单了，但是呢，由于很基础也要掌握。

在 C++ 中，类成员的访问权限是通过访问修饰符来控制的。

有三种访问修饰符：public、private 和 protected，分别定义了类成员的访问级别，控制类成员的可见性和可访问性。

#### public（公共）

 公共成员在任何地方都是可访问的。

调用方可以直接访问和修改公共成员，公共访问修饰符通常用于类的外部接口。

但是一般情况下，不建议将类的成员变量设置为 public，因为这不符合封装的原则。

```cpp
class MyClass {
public:
    int x;
};
```

`x` 是一个公共成员，可以在类的对象中被访问。

#### private（私有） 

私有成员只能在类的内部访问，即仅在类的成员函数中可以访问。

私有成员用于实现类的内部实现细节，这些细节对于类的用户来说是隐藏的。

```cpp
class MyClass {
private:
    int x;
};
```

上面的`x` 是一个私有成员，不能在类的外部被直接访问，要想访问 x，必须由 MyClass 封装一些对外的 public 函数。

#### protected（受保护）

受保护成员类似于私有成员，但它们可以被派生类访问。

受保护成员通常用于继承和多态等场景，这样子类也可以访问父类的成员变量。

```cpp
class MyBaseClass {
protected:
    int x;
};

class MyDerivedClass : public MyBaseClass {
public:
    void setX(int a) {
        x = a;
    }
};
```


