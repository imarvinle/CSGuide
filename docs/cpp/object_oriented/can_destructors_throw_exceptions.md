# 析构函数可以抛出异常吗？

**面试高频指数：★★☆☆☆**

首先，从语法层面 C++ 并没有禁止析构函数抛出异常，但是实践中建议不要这样做。

#### Effective C++ 条款08 也提到：别让异常逃离析构函数

------

由于析构函数常常被自动调用，在析构函数中抛出的异常往往会难以捕获，引发程序非正常退出或未定义行为。

另外，我们都知道在容器析构时，会逐个调用容器中的对象析构函数，而某个对象析构时抛出异常还会引起后续的对象无法被析构，导致资源泄漏。 

这里的资源可以是内存，也可以是数据库连接，或者其他类型的计算机资源。

析构函数是由C++来调用的，源代码中不包含对它的调用，因此它抛出的异常不可被捕获。 对于栈中的对象而言，在它离开作用域时会被析构；对于堆中的对象而言，在它被`delete`时析构。

如:

```cpp
class C{
public:
    ~C(){ throw 1;}
};
void main(){
    try{
        C c;
    }
    catch(...){}
}
```

析构的异常并不会被捕获，因为`try{}`代码块中只有一行代码`C c`，它并未抛出异常。运行时会产生这样的错误输出：

```cpp
libC++abi.dylib: terminating with uncaught exception of type int
```

那么如果在析构函数中真的可能存在异常，该如何处理呢？

看一个例子，假设你使用一个 Class 负责数据库连接：

```cpp
class DBConnection
{ 
public:
　　 ...
　　 static DBConnection create(); //返回DBConnection对象；为求简化暂略参数
　　 void close(); //关闭联机；失败则抛出异常。
};
```

为确保客户不忘记在DBConnection对象身上调用 close()，一个合理的想法是创建一个用来管理DBConection资源的class，并在其析构函数中调用close。这就是著名的以对象管理资源即 RAII（参考这篇: [**RAII如何理解**](https://www.yuque.com/csguide/cf15wf/hnp4d3xnb3dbphn8)）。

```cpp
//这个class用来管理DBConnection对象
class DBConn
{ 
public:
　　 ...
　　DBConn(const DBConnection& db)
　　{
       this->db=db;
   }
　 ~DBConn() //确保数据库连接总是会被关闭
　　{
　　    db.close();
　　}
　　
private:
　　 DBConnection db;
};
```

如果调用close成功，没有任何问题。但如果该调用导致异常，DBConn析构函数会传播该异常，如果离开析构函数，那会造成问题，解决办法如下：

1. 如果close抛出异常就结束程序，通常调用abort完成：

```cpp
DBConn::~DBconn()
{
    try
    {
	    db.close(); 
    }
    catch(...)
    {
        abort();
    }
}
```

如果程序遭遇一个“于析构期间发生的错误”后无法继续执行，“强制结束程序”是个合理选项，毕竟它可以阻止异常从析构函数传播出去导致不明确行为。

2. 吞下因调用 close 而发生的异常

```cpp
DBConn::~DBConn
{
    try{ db.close();}
    catch(...) 
    {
        //制作运转记录，记下对close的调用失败！
    }
}
```

一般而言，将异常吞掉是个坏主意，因为面对动作失败选择无所作为，然而有时候吞下异常比“草率结束程序”或“不明确行为带来的风险”好。能够这么做的一个前提就是程序必须能够继续可靠的执行。

3. 重新设计 DBConn 接口，使其客户有机会对可能出现的异常作出反应
   我们可以给DBConn添加一个close函数，赋予客户一个机会可以处理“因该操作而发生的异常”。
   把调用close的责任从DBConn析构函数手上移到DBConn客户手中，你也许会认为它违反了“让接口容易被正确使用”的忠告。
   实际上这污名并不成立。如果某个操作可能在失败的时候抛出异常，而又存在某种需要必须处理该异常，那么这个异常必须来自析构函数以外的某个函数。
   因为析构函数吐出异常就是危险，总会带来“过早结束程序”或“发生不明确行为”的风险。

#### 总结

析构函数可以抛出异常，但是这种做法是非常危险的，通常不推荐。
因为析构函数具有一种清理资源的特性，如果析构函数本身抛出异常，可能导致以下问题：

* 资源泄露：当一个对象被析构时，析构函数负责释放该对象持有的资源。如果析构函数抛出异常，这个过程可能会中断，导致资源泄露。

* 叠加异常：如果析构函数在处理另一个异常时抛出异常，会导致异常叠加。这种情况下，程序将无法处理两个异常，从而可能导致未定义行为或程序崩溃。

为了避免这些问题，通常建议在析构函数中处理异常或者避免执行会抛出异常的函数，可以在析构函数中使用 try-catch 块来捕获和处理潜在的异常，确保资源得到正确释放和清理。
保证程序的稳定性和健壮性。

> 参考: https://blog.ycshao.com/2012/03/22/effective-c-item-8-prevent-exceptions-from-leaving-destructors/、https://blog.csdn.net/wallwind/article/details/6765167、https://blog.csdn.net/K346K346/article/details/55214384
> https://harttle.land/2015/07/26/effective-cpp-8.html

