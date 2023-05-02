# class 和 struct 区别
**面试高频指数：★★★☆☆**

C++ 中为了兼容 C 语言而保留了 C 语言的 struct 关键字，并且加以扩充了含义。
在 C 语言中，struct 只能包含成员变量，不能包含成员函数。
而在 C++ 中，struct 类似于 class，既可以包含成员变量，又可以包含成员函数。
C++ 中的 struct 和 class 基本是通用的，唯有几个细节不同：

* class 中类中的成员默认都是 private 属性的；

* 而在 struct 中结构体中的成员默认都是 public 属性的。

* class 继承默认是 private 继承，而 struct 继承默认是 public 继承。

* class 可以用于定义模板参数，struct 不能用于定义模板参数。

  意思是这样可以：

  ```cpp
  template <class T>
  struct  Person {
  public:
      T age;
  };
  ```

  但是这样就会编译报错:

  ```cpp
  template <struct T>
  struct  Person {
  public:
      T age;
  };
  ```
实际使用中，struct 我们通常用来定义一些 POD(plain old data)

POD是 C++ 定义的一类数据结构概念，比如 int、float 等都是 POD 类型的。

Plain 代表它是一个普通类型，Old 代表它是旧的，与几十年前的 C 语言兼容，那么就意味着可以使用 memcpy() 这种最原始的函数进行操作。

两个系统进行交换数据，如果没有办法对数据进行语义检查和解释，那就只能以非常底层的数据形式进行交互，而拥有 POD 特征的类或者结构体通过二进制拷贝后依然能保持数据结构不变。

**也就是说，能用 C 的 memcpy() 等函数进行操作的类、结构体就是 POD 类型的数据**。

而 class 用于定义一些 非 POD 的对象，面向对象编程。