# 为什么C++的 member function template不能是virtual的

面试高频指数：★★☆☆☆

这个题目在面试时问得倒不是很多(一些面试官其实也答不上来)，但是却值得了解，背后的原理会涉及到 C++ 的一些语法机制实现。


问题的意思是，为什么在C++里面，一个类的成员函数不能既是 template 又是 virtual 的。比如，下面的代码编译会报错：

```text
class Animal{
  public:
      template<typename T>
      virtual void make_sound(){
        //...
      }
};
```

为啥呢？

这个问题涉及到一点 C++ 的实现机制（C++中模板是如何工作的、虚拟函数是如何实现的、编译器和链接器如何从源代码生成可执行文件），所以很少人能一下子答上来。

具体理由如下:

因为C++的编译与链接模型是"分离"的(至少是部分原因吧)。

- 从Unix/C开始，一个C/C++程序就可以被分开编译，然后用一个linker链接起来。这种模型有一个问题，就是各个编译单元可能对另一个编译单元一无所知。
- 一个 function template最后到底会被 instantiate 为多少个函数，要等**整个程序(所有的编译单元)全部被编译完成**才知道。
- 同时，virtual function的实现大多利用了一个"虚函数表"（参考: [虚函数机制](https://www.yuque.com/csguide/cf15wf/uv8lqhv7gitd6oke)）的东西，这种实现中，一个类的内存布局(或者说虚函数表的内存布局)需要**在这个类编译完成的时候就被完全确定**。

所以当一个虚拟函数是模板函数时，编译器在编译时无法为其生成一个确定的虚函数表条目，因为模板函数可以有无数个实例。但是编译时无法确定需要调用哪个特定的模板实例。因此，C++标准规定member function 不能既是 template 又是 virtual 的。

>  参考: https://www.zhihu.com/question/60911582/answer/182045051

>  [What is the best C++ interview question?](https://softwareengineering.stackexchange.com/questions/25836/what-is-the-best-c-interview-question/25897#25897)