# 内存泄露如何检测和避免

**面试高频指数：★★★★☆**

作为一名 C/C++ 程序员，内存泄露几乎是无法避免的事情，那么我们日常用什么工具去检测呢

### 内存泄露是什么？

简单来说就是：在程序中申请了动态内存，却没有释放，如果程序长期运行下去，最终会导致没有内存可供分配。

所以不少大厂的服务有个特点，就是会定期重启服务进程，重启的目的就是让操作系统回收整个进程的资源包括内存，这样一点点的内存泄露问题即使无法定位，也不是什么大问题哈哈哈

### 如何检测

检测内存泄露的方法：

1. 手动检查代码：仔细检查代码中的内存分配和释放，确保每次分配内存后都有相应的释放操作。比如 malloc和free、new和delete是否配对使用了。

2. 使用调试器和工具：有一些工具可以帮助检测内存泄露。例如：
   - **Valgrind**（仅限于Linux和macOS）：Valgrind是一个功能强大的内存管理分析工具，可以检测内存泄露、未初始化的内存访问、数组越界等问题。使用Valgrind分析程序时，只需在命令行中输入`valgrind --leak-check=yes your_program`即可。
   - **Visual Studio中的CRT（C Runtime）调试功能**：Visual Studio提供了一些用于检测内存泄露的C Runtime库调试功能。例如，`_CrtDumpMemoryLeaks`函数可以在程序结束时报告内存泄露。
   - **AddressSanitizer**：AddressSanitizer是一个用于检测内存错误的编译器插件，适用于GCC和Clang。要启用AddressSanitizer，只需在编译时添加`-fsanitize=address`选项。

### 如何避免内存泄露

1. 使用智能指针（C++）：在C++中，可以使用智能指针（如`std::unique_ptr`和`std::shared_ptr`）来自动管理内存。这些智能指针在作用域结束时会自动释放所指向的内存，从而降低忘记释放内存或者程序异常导致内存泄露的风险。

2. **异常安全**：在C++中，如果程序抛出异常，需要确保在异常处理过程中正确释放已分配的内存。使用`try-catch`块来捕获异常并在适当的位置释放内存。或者，更好的做法是使用RAII（Resource Acquisition Is Initialization）技术（关于RAII可以看这篇文章: [如何理解RAII](https://www.yuque.com/csguide/cf15wf/hnp4d3xnb3dbphn8)），将资源（如内存）的管理与对象的生命周期绑定。


