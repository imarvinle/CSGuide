# C++ 中 sizeof 关键字

**面试高频指数：★★★★☆**


sizeof 是 C 语言中的运算符，用来计算一个类型/对象的所占用的内存大小，有些 C/C++ 面试官会出一些 sizeof(xxx) 的大小是多少的问题。

## sizeof计算大小的小技巧

我总结了几个关键的点，掌握这几个基本上就能答对大部分类似问题了：

1. **指针的大小永远是固定的，取决于处理器位数，32位就是 4 字节，64位就是 8 字节**

2. **数组作为函数参数时会退化为指针，大小要按指针的计算，** 详细请看这篇文章[数组退化为指针](https://csguide.cn/cpp/basics/array_and_pointer.html)

3. **struct 结构体要考虑字节对齐**，字节对齐的具体规则请看这篇文章: [字节对齐](https://csguide.cn/cpp/basics/byte_alignment.html)

4. **字符串数组要算上末尾的 '\0'**

## 举例：

```cpp
// 前提: 64 位电脑上
char  str[] = "Hello World" ;
char   *p = str;
double *dp;
int n = 10;

sizeof(str )＝___12_____  // 11个字符 + 末尾'\0'，总结第四点
sizeof ( p ) =  ___8 ___  // 64 位电脑，指针 8 个字节
sizeof ( n ) = ___4______  // int 一般 4 个字节
  
void Func (char str[10])
{
   sizeof( str ) = _8__  // 数组做参数退化为 char类型指针，即 8 个字节，总结第2点
}
void *vp = malloc( 100 );

sizeof ( vp )＝__8____  // vp 是一个 void 类型指针，还是 8 个字节

struct AlignedStruct {
    char a;   // 本来1字节，padding 3 字节
    int b;    //  4 字节
    short c;  // 本来 short 2字节，但是整体需要按照 4 字节对齐(成员对齐边界最大的是int 4) ，
    //所以需要padding 2，总共: 4 + 4 + 4
};
```

```sizeof(AlignedStruct) = ___12__```， 具体分析见上面 👆


## 练手题

接下来出几道题，大家分别思考一下在 32 位和 64 位下，sizeof 的结果：

1. 给定以下结构体定义：
```cpp
struct MyStruct {
    int a;
    double b;
    char c;
};
```
sizeof(MyStruct)的结果是多少？

2.  给定以下数组定义：

```cpp
int arr[10];
```
sizeof(arr)的结果是多少？

3. 给定以下结构体定义：

```cpp
struct EmptyStruct {};
```
sizeof(EmptyStruct)的结果是多少？

**大家可以在评论区写一下自己的答案，如果有不确定的话，可以运行一下代码看看输出结果。**
