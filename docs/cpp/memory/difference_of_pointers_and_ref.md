# 指针和引用的区别

**面试高频指数：★★★☆☆**

指针和引用在 C++ 中都用于间接访问变量，但它们有一些区别：

1. 指针是一个变量，它保存了另一个变量的内存地址；引用是另一个变量的别名，与原变量共享内存地址。
2. 指针可以被重新赋值，指向不同的变量；引用在初始化后不能更改，始终指向同一个变量。
3. 指针可以为 nullptr，表示不指向任何变量；引用必须绑定到一个变量，不能为 nullptr。
4. 使用指针需要对其进行解引用以获取或修改其指向的变量的值；引用可以直接使用，无需解引用。

下面的示例展示了指针和引用的区别：

```cpp
#include <iostream>

int main() {
    int a = 10;
    int b = 20;

    // 指针
    int *p = &a;
    std::cout << "Pointer value: " << *p << std::endl; // 输出：Pointer value: 10

    p = &b;
    std::cout << "Pointer value: " << *p << std::endl; // 输出：Pointer value: 20

    // 引用
    int &r = a;
    std::cout << "Reference value: " << r << std::endl; // 输出：Reference value: 10

    // r = &b; // 错误：引用不能被重新绑定
    int &r2 = b;
    r = r2; // 将 b 的值赋给 a，r 仍然引用 a
    std::cout << "Reference value: " << r << std::endl; // 输出：Reference value: 20

    return 0;
}
```

当然啦，上面说的都是很简单的一些概念，这里继续引用之前公众号发过的一篇文章: [指针和引用有什么区别](https://mp.weixin.qq.com/s/5EuOKO-b-AxhR_SI9aC0ww)

这篇文章中从汇编底层角度来解释 C++ 中引用的实现机制。

###  从汇编看引用和指针

要是问引用和指针有什么区别，相信大多数学过c++的都能回答上几点: 指针是所指内存的地址，引用是别名，引用必须初始化。。。。。

但是引用是别名这是c++语法规定的语义，那么到底引用在汇编层面和指针有什么区别呢？
没区别。 对，引用会被c++编译器当做const指针来进行操作。

####  汇编揭开引用面纱

先分别用指针和引用来写个非常熟悉的函数swap

```cpp
// 指针版
void swap(int *a, int *b) {
    int temp = *a;
    *a = *b;
    *b = temp;
}
// 引用版
void swap(int &a, int &b) {
    int temp = a;
    a = b;
    b = temp;
}
```

直接gcc -S 输出汇编

```assembly
# 引用版汇编
__Z4swapRiS_:                           ## @_Z4swapRiS_
        .cfi_startproc
## %bb.0:
        pushq   %rbp
        .cfi_def_cfa_offset 16
        .cfi_offset %rbp, -16
        movq    %rsp, %rbp          
        .cfi_def_cfa_register %rbp
        movq    %rdi, -8(%rbp)         # 传入的第一个参数存放到%rbp-8  （应该是采用的寄存器传参，而不是常见的压栈）
        movq    %rsi, -16(%rbp)        # 第二个参数 存放到 %rbp-16
        movq    -8(%rbp), %rsi         # 第一个参数赋给 rsi
        movl    (%rsi), %eax           # 以第一个参数为地址取出值赋给eax，取出*a暂存寄存器
        movl    %eax, -20(%rbp)        # temp = a
        movq    -16(%rbp), %rsi        # 将第二个参数重复上面的
        movl    (%rsi), %eax
        movq    -8(%rbp), %rsi    
        movl    %eax, (%rsi)           # a = b
        movl    -20(%rbp), %eax        # eax = temp
        movq    -16(%rbp), %rsi
        movl    %eax, (%rsi)            # b = temp
        popq    %rbp
        retq
        .cfi_endproc
                                        ## -- End function

```

在来一个函数调用引用版本swap

```cpp
void call() {
    int a = 10;
    int b = 3;
    int &ra = a;
    int &rb = b;
    swap(ra, rb);
}
```

对应汇编:

```asm
__Z4callv:                              ## @_Z4callv
        .cfi_startproc
## %bb.0:
        pushq   %rbp
        .cfi_def_cfa_offset 16
        .cfi_offset %rbp, -16
        movq    %rsp, %rbp
        .cfi_def_cfa_register %rbp
        subq    $32, %rsp
        leaq    -8(%rbp), %rax          # rax中是b的地址
        leaq    -4(%rbp), %rcx          # rcx中是a的地址
        movl    $10, -4(%rbp)   
        movl    $3, -8(%rbp)            # 分别初始化a、b
        movq    %rcx, -16(%rbp)         # 赋给ra引用
        movq    %rax, -24(%rbp)         # 赋给rc引用
        movq    -16(%rbp), %rdi         # 寄存器传参， -16(%rbp)就是rcx中的值也就是a的地址
        movq    -24(%rbp), %rsi         # 略
        callq   __Z4swapRiS_
        addq    $32, %rsp
        popq    %rbp
        retq
```

清楚了！ 我们可以看到给引用赋初值也就是把所绑定对象的地址赋给引用所在内存，和指针是一样的。

再来看看指针的汇编吧

```asm
__Z4swapPiS_:                           ## @_Z4swapPiS_
        .cfi_startproc
## %bb.0:
        pushq   %rbp
        .cfi_def_cfa_offset 16
        .cfi_offset %rbp, -16
        movq    %rsp, %rbp
        .cfi_def_cfa_register %rbp
        movq    %rdi, -8(%rbp)
        movq    %rsi, -16(%rbp)
        movq    -8(%rbp), %rsi
        movl    (%rsi), %eax
        movl    %eax, -20(%rbp)
        movq    -16(%rbp), %rsi
        movl    (%rsi), %eax
        movq    -8(%rbp), %rsi
        movl    %eax, (%rsi)
        movl    -20(%rbp), %eax
        movq    -16(%rbp), %rsi
        movl    %eax, (%rsi)
        popq    %rbp
        retq
        .cfi_endproc
                                        ## -- End function
```

汇编我就不注释了，真的是完全一样！并不是我直接复制的引用汇编而是真的是一种方式实现的

指针版调用

```cpp
void pointer_call() {
    int a = 10;
    int b = 3;
    int *pa = &a;
    int *pb = &b;
    swap(pa, pb);
}
```

这次我特意改了下函数名，对应汇编:

```asm
__Z12pointer_callv:                     ## @_Z12pointer_callv
        .cfi_startproc
## %bb.0:
        pushq   %rbp
        .cfi_def_cfa_offset 16
        .cfi_offset %rbp, -16
        movq    %rsp, %rbp
        .cfi_def_cfa_register %rbp
        subq    $32, %rsp
        leaq    -8(%rbp), %rax
        leaq    -4(%rbp), %rcx
        movl    $10, -4(%rbp)
        movl    $3, -8(%rbp)
        movq    %rcx, -16(%rbp)
        movq    %rax, -24(%rbp)
        movq    -16(%rbp), %rdi
        movq    -24(%rbp), %rsi
        callq   __Z4swapPiS_
        addq    $32, %rsp
        popq    %rbp
        retq
```

还是几乎完全一样...。也没再注释

#### 简单总结

1. 引用只是c++语法糖，可以看作编译器自动完成取地址、解引用的常量指针
2. 引用区别于指针的特性都是编译器约束完成的，一旦编译成汇编就喝指针一样
3. 由于引用只是指针包装了下，所以也存在风险，比如如下代码:  

```cpp  
int *a = new int;
int &b = *a;
delete a;
b = 12;    // 对已经释放的内存解引用
```

4. 引用由编译器保证初始化，使用起来较为方便(如不用检查空指针等)
5. 尽量用引用代替指针
6. 引用没有顶层const即```int & const```，因为引用本身就不可变，所以在加顶层const也没有意义； 但是可以有底层const即 ```const int&```，这表示引用所引用的对象本身是常量
7. 指针既有顶层const(```int * const```--指针本身不可变)，也有底层const(```int * const```--指针所指向的对象不可变)
8. 有指针引用--是引用，绑定到指针， 但是没有引用指针--这很显然，因为很多时候指针存在的意义就是间接改变对象的值，但是引用本身的值我们上面说过了是所引用对象的地址，但是引用不能更改所引用的对象，也就当然不能有引用指针了。
9. 指针和引用的自增(++）和自减含义不同，指针是指针运算, 而引用是代表所指向的对象对象执行++或--