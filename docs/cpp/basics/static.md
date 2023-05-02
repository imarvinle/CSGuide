# C++ 中的 static

**面试高频指数：★★★★★**

在 C/C++ 中，static 是一个非常重要的关键字，它可以用于变量、函数和类中。

下面分别举例说明下：

#### 1. static 修饰全局变量
static 修饰全局变量可以将变量的作用域限定在当前文件中，使得其他文件无法访问该变量。
同时，static 修饰的全局变量在程序启动时被初始化（可以简单理解为在执行 main 函数之前，会执行一个全局的初始化函数，在那里会执行全局变量的初始化），生命周期和程序一样长。

```cpp
// a.cpp 文件
static int a = 10;  // static 修饰全局变量
int main() {
    a++;  // 合法，可以在当前文件中访问 a
    return 0;
}

// b.cpp 文件
extern int a;  // 声明 a
void foo() {
    a++;  // 非法，会报链接错误，其他文件无法访问 a
}
```

关于这里为什么会是非法、报链接错误，可以看看这篇文章：[extern 的作用-从链接角度理解](https://www.yuque.com/csguide/cf15wf/dzsd2pviwodzb6v5)

了解一下 extern 和 链接方面的知识。

#### 2. static 修饰局部变量

static 修饰局部变量可以使得变量在函数调用结束后不会被销毁，而是一直存在于内存中，下次调用该函数时可以继续使用。

同时，由于 static 修饰的局部变量的作用域仅限于函数内部，因此其他函数无法访问该变量。

```cpp
void foo() {
    static int count = 0;  // static 修饰局部变量
    count++;
    cout << count << endl;
}

int main() {
    foo();  // 输出 1
    foo();  // 输出 2
    foo();  // 输出 3
    return 0;
}
```

#### 3. static 修饰函数

static 修饰函数可以将函数的作用域限定在当前文件中，使得其他文件无法访问该函数。同时，由于 static 修饰的函数只能在当前文件中被调用，因此可以避免命名冲突和代码重复定义。

```cpp
// a.cpp 文件
static void foo() {  // static 修饰函数
    cout << "Hello, world!" << endl;
}

int main() {
    foo();  // 合法，可以在当前文件中调用 foo 函数
    return 0;
}

// b.cpp 文件
extern void foo(); // 声明 foo
void bar() {
    foo();  // 非法，会报链接错误，找不到 foo 函数，其他文件无法调用 foo 函数
}
```

#### 4. static 修饰类成员变量和函数
static 修饰类成员变量和函数可以使得它们在所有类对象中共享，且不需要创建对象就可以直接访问。

```cpp
class MyClass {
public:
    static int count;  // static 修饰类成员变量
    static void foo() {  // static 修饰类成员函数
        cout << count << endl;
    }
};
// 访问：

MyClass::count;
MyClass::foo();
```


