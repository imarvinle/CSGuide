#  虚函数表

**面试高频指数：★★★★☆**

虚函数表在 C++ 面试中出现频率非常高，常常以各种形式的问题出现，比如：

**1. C++ 多态了解么？C++ 多态的实现原理？**
**2. 虚函数是什么？虚函数的实现原理？**
**3. 虚表是什么？虚表的内存结构布局如何？虚表的第一项（或第二项）是什么？**

这些问题最终基本上都指向了对 C++的内存模型虚函数表部分的理解。

对 C++ 了解的人都应该知道虚函数（Virtual Function）是通过一张虚函数表（Virtual Table）来实现的，简称为V-Table。
在这个表中，存放的是一个类的虚函数的地址表，这张表解决了继承、覆盖的问题，保证其真实反应实际的函数。
这样，这个类的实例内存中都有一个虚函数表的指针，所以，当我们用父类的指针来操作一个子类的时候，这张虚函数表就显得由为重要了，它就像一个地图一样，指明了实际所应该调用的函数。

```cpp
class roint (
public:
	Point(float xval ); 
	virtoal ~Point();
	float x() const,
	static int PointCount();
protected:
	virtual ostream& print( ostream &os ) const;
	float _x;
	static int _point_count;
};
```
比如上面这个类，它的对象模型如下：
![](https://cdn.how2cs.cn/csguide/113608.png)
上图就是《深度探索C++对象模型》这本书第一章介绍的 C++的对象模型，也就是 C++ 是如何存储一个对象的数据（成员函数、成员变量、静态变量、虚函数等等），建议学习C++的同学都看下这本书，需要获取这本书 PDF可以看下这个仓库: https://github.com/imarvinle/awesome-cs-books

在上面的示例中，意思就是一个对象在内存中一般由成员变量（非静态）、虚函数表指针(vptr)构成。

虚函数表指针指向一个数组，数组的元素就是各个虚函数的地址，通过函数的索引，我们就能直接访问对应的虚函数。

👇下面看个实际的例子，我们通过直接访问虚函数表来调用各个虚函数（注意，这种行为是未定义的，因为每个编译器/平台上，虚函数表的位置/实现都有可能不同）

假如我们有这样一个类: 

```cpp
#include <iostream>

// 函数指针
typedef void(*Func)(void);

class MyBaseClass {
public:
    virtual void virtualMethod1() {
        std::cout << "BaseClass::virtualMethod1()" << std::endl;
    }
    virtual void virtualMethod2() {
        std::cout << "BaseClass::virtualMethod2()" << std::endl;
    }
    virtual void virtualMethod3() {
        std::cout << "BaseClass::virtualMethod3()" << std::endl;
    }

};

class MyDerivedClass : public MyBaseClass {
public:
    virtual void virtualMethod3() {
        std::cout << "DerivedClass::virtualMethod3()" << std::endl;
    }
    virtual void virtualMethod4() {
        std::cout << "DerivedClass::virtualMethod4()" << std::endl;
    }
    virtual void virtualMethod5() {
        std::cout << "DerivedClass::virtualMethod5()" << std::endl;
    }
};

void PrintVtable(void** vtable) {
    // 输出虚函数表中每个函数的地址
    for (int i = 0; vtable[i] != nullptr; i++) {
        // 最多调用五个函数，怕访问到虚函数表非法的地址，因为就五个函数
        if (i >= 5)  {
            return;
        }
        std::cout << "Function " << i << ": " << vtable[i] << std::endl;
        // 将虚函数表中的虚函数转换为函数指针，然后进行调用
        Func func = (Func) vtable[i];
        func();
    }
}

int main() {
    MyDerivedClass obj;

    // 取对象的首地址，然后转换为的指针，就取到了虚函数表指针，指向 obj 对象的虚函数表
    // 因为大多数实现上，虚函数表指针一般都放在对象第一个位置
    void** vtable = *(void***)(&obj);
    std::cout << "DerivedClass Vetable:" << std::endl;
    // 打印子类的虚函数表
    PrintVtable(vtable);

    std::cout << std::endl <<  "BaseClass Vetable:" << std::endl;
    MyBaseClass base_obj;
    void** vbtable = *(void***)(&base_obj);
    // 打印父类的虚函数表
    PrintVtable(vbtable);
    return 0;
}
```

在我的电脑上(64位 macos) 编译运行的结果如下:

```cpp
DerivedClass Vetable:
Function 0: 0x10d675050
BaseClass::virtualMethod1()
Function 1: 0x10d675090
BaseClass::virtualMethod2()
Function 2: 0x10d6750d0
DerivedClass::virtualMethod3()
Function 3: 0x10d675110
DerivedClass::virtualMethod4()
Function 4: 0x10d675150
DerivedClass::virtualMethod5()

BaseClass Vetable:
Function 0: 0x10d675050
BaseClass::virtualMethod1()
Function 1: 0x10d675090
BaseClass::virtualMethod2()
Function 2: 0x10d675190
BaseClass::virtualMethod3()
```

注意到，这个结果说明了，子类的虚函数表中 virtualMethod3 的地址和父类中 virtualMethod3 地址不同，这就是虚函数实现多态的底层原理，就是子类的虚函数表中用子类重写的函数地址去取代父类的函数地址。

当然虚函数表遇到继承、多重继承等就稍微复杂一些，但是大体的原理都像上面解释的这样，理解到这里基本也就理解了虚机制。

> 关于虚函数表的更多知识就不细讲了，感兴趣可以看下这篇博客: https://coolshell.cn/articles/12165.html

### 动态多态底层原理
正如这篇[C++实现多态的方式](https://www.yuque.com/csguide/cf15wf/mns7rlkgder0mup8) 文章所说，C++ 中的动态多态性是通过虚函数实现的。

当基类指针或引用指向一个派生类对象时，调用虚函数时，实际上会调用派生类中的虚函数，而不是基类中的虚函数。
**在底层，当一个类声明一个虚函数时，编译器会为该类创建一个虚函数表（Virtual Table）。**
这个表存储着该类的虚函数指针，这些指针指向实际实现该虚函数的代码地址。

每个对象都包含一个指向该类的虚函数表的指针，这个指针在对象创建时被初始化，通常是作为对象的第一个成员变量。

当调用一个虚函数时，编译器会通过对象的虚函数指针查找到该对象所属的类的虚函数表，并根据函数的索引值（通常是函数在表中的位置，编译时就能确定）来找到对应的虚函数地址。

然后将控制转移到该地址，实际执行该函数的代码。
对于派生类，其虚函数表通常是在基类的虚函数表的基础上扩展而来的。

**在派生类中，如果重写了基类的虚函数，那么该函数在派生类的虚函数表中的地址会被更新为指向派生类中实际实现该函数的代码地址。**
```cpp
Shape *shape;
Rectangle rec(10,7);
shape = &rec;
shape->area();
```
在上面的示例中，通过基类指针 shape 去调用 area() 方法时，实际调用到的就是虚函数表中被替换后的子类方法。

C++的动态多态必须满足两个条件：
- **必须通过基类的指针或者引用调用虚函数**
- **被调用的函数是虚函数，且必须完成对基类虚函数的重写**

其中第一条很重要，当我们使用派生类的指针去访问/调用虚函数时，实际上并未发生动态多态，因为编译时就能确定对象类型为派生类型，然后直接生成调用派生类虚函数的代码即可，这种叫做**静态绑定**
通过基类的指针或引用调用虚函数才能构成多态，因为这种情况下运行时才能确定对象的实际类型，这种称为**动态绑定**


