#  malloc、new，free、delete 区别

### 
**面试高频指数：★★★★☆**

malloc、new、free、delete 这几个总是放在一起来对比，今天来彻底的解析一下这几个函数/操作符:

整体用法:

```cpp
#include <cstdlib>

int main() {
    // 使用 malloc 分配内存
    int *ptr = (int *)malloc(sizeof(int));
    // 记得在使用完之后释放内存
    free(ptr);
}
```

```cpp
int main() {
    // 使用 new 分配内存并调用构造函数
    int *ptr = new int;
    // 记得在使用完之后释放内存
    delete ptr;
}
```
#### 1. 简单对比
1. 语法不同：`malloc/free`是一个C语言的函数，而`new/delete`是C++的运算符。
2. 分配内存的方式不同：`malloc`只分配内存，而`new`会分配内存并且调用对象的构造函数来初始化对象。
3. 返回值不同：`malloc`返回一个 void 指针，需要自己强制类型转换，而`new`返回一个指向对象类型的指针。
4. malloc 需要传入需要分配的大小，而 new 编译器会自动计算所构造对象的大小
#### 2. 详细解析
#####  1. 申请的内存所在位置
new 操作符从自由存储区（free store）上为对象动态分配内存空间，而malloc函数从堆上动态分配内存。

自由存储区是 C++ 基于new操作符的一个**抽象概念**，凡是通过 new 操作符进行内存申请，该内存即为自由存储区。

而堆是操作系统中的术语，是操作系统所维护的一块特殊内存，用于程序的内存动态分配，C语言使用malloc从堆上分配内存，使用free释放已分配的对应内存。

那么自由存储区是否能够是堆（问题等价于new是否能在堆上动态分配内存），这取决于operator new 的实现细节。

自由存储区不仅可以是堆，还可以是静态存储区，这都看operator new在哪里为对象分配内存。

不过大家可以基本将自由存储区等价于堆区。

特别的，new 甚至可以不为对象分配内存！placement_new的功能可以办到这一点：
```cpp
new (place_address) type
```
place_address为一个指针，代表一块内存的地址。当使用上面这种仅以一个地址调用new操作符时，new操作符调用特殊的operator new，也就是下面这个版本：
```cpp
void * operator new (size_t,void *) //不允许重定义这个版本的operator new
```
这个operator new不分配任何的内存，它只是简单地返回指针实参，然后 new 表达式负责在place_address指定的地址进行对象的初始化工作。

##### 2. 内存分配失败时返回值
new内存分配失败时，会抛出bac_alloc异常，它不会返回NULL；malloc分配内存失败时返回NULL。
在使用C语言时，我们习惯在malloc分配内存后判断分配是否成功：
```c
int *a  = (int *)malloc ( sizeof (int ));
if(NULL == a) {
    ...
} else {
    ...
}
```
从 C 语言走入 C++ 阵营的新手可能会把这个习惯带入C++：
```cpp
int * a = new int();
if(NULL == a) {
    ...
} else {   
    ...
}
```
实际上这样做一点意义也没有，因为new根本不会返回NULL，而且程序能够执行到if语句已经说明内存分配成功了，如果失败早就抛异常了。正确的做法应该是使用异常机制：
```cpp
try {
    int *a = new int();
} catch (bad_alloc) {
    ...
}
```
##### 3. 是否调用构造函数/析构函数
**使用new操作符来分配对象内存时会经历三个步骤：**

- 第一步：调用operator new 函数（对于数组是operator new[]）分配一块足够大的，**原始**的，未命名的内存空间以便存储特定类型的对象。
- 第二步：编译器运行相应的**构造函数**以构造对象，并为其传入初值。
- 第三部：对象构造完成后，返回一个指向该对象的指针。

**使用delete操作符来释放对象内存时会经历两个步骤：**

- 第一步：调用对象的析构函数。
- 第二步：编译器调用operator delete(或operator delete[])函数释放内存空间。

##### 4. 对数组的处理
C++ 提供了 new[] 与 delete[] 来专门处理数组类型:
```cpp
	A * ptr = new A[10];//分配10个A对象
```
使用 new[] 分配的内存必须使用 delete[] 进行释放：
```cpp
  delete [] ptr;
```
new 对数组的支持体现在它会分别调用构造函数函数初始化每一个数组元素，释放对象时为每个对象调用析构函数。注意 delete[] 要与new[] 配套使用，不然会找出数组对象部分释放的现象，造成内存泄漏。

至于 malloc，它并知道你在这块内存上要放的数组还是啥别的东西，反正它就给你一块原始的内存，在给你个内存的地址就完事。所以如果要动态分配一个数组的内存，还需要我们手动自定数组的大小：
```cpp
int * ptr = (int *) malloc( sizeof(int)* 10 );//分配一个10个int元素的数组
```
##### 5. new 和 malloc 是否可以相互调用
operator new /operator delete的实现可以基于malloc，而malloc的实现不可以去调用new。下面是编写operator new /operator delete 的一种简单方式，其他版本也与之类似：
```
void * operator new (sieze_t size)
{
    if(void * mem = malloc(size)
        return mem;
    else
        throw bad_alloc();
}
void operator delete(void *mem) noexcept
{
    free(mem);
}
```

##### 6. 能够直观地重新分配内存
使用 malloc 分配的内存后，如果在使用过程中发现内存不足，可以使用 realloc 函数进行内存重新分配实现内存的扩充。realloc 先判断当前的指针所指内存是否有足够的连续空间，如果有，原地扩大可分配的内存地址，并且返回原来的地址指针；如果空间不够，先按照新指定的大小分配空间，将原有数据从头到尾拷贝到新分配的内存区域，而后释放原来的内存区域。

new 没有这样直观的配套设施来扩充内存。

|                      |                                       |                                      |
| :------------------: | :-----------------------------------: | :----------------------------------: |
|         特征         |              new/delete               |             malloc/free              |
|    分配内存的位置    |              自由存储区               |                  堆                  |
| 内存分配成功的返回值 |             完整类型指针              |                void*                 |
| 内存分配失败的返回值 |             默认抛出异常              |               返回NULL               |
|    分配内存的大小    |       由编译器根据类型计算得出        |          必须显式指定字节数          |
|       处理数组       |       有处理数组的new版本new[]        | 需要用户计算数组的大小后进行内存分配 |
|   已分配内存的扩充   |            无法直观地处理             |         使用realloc简单完成          |
|     是否相互调用     | 可以，看具体的operator new/delete实现 |             不可调用new              |
|  分配内存时内存不足  | 客户能够指定处理函数或重新制定分配器  |       无法通过用户代码进行处理       |
|       函数重载       |                 允许                  |                不允许                |
|  构造函数与析构函数  |                 调用                  |                不调用                |

> 参考: `https://www.cnblogs.com/qg-whz/p/5140930.html`