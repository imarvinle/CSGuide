# 宏定义 (define) 和 typedef 的区别

**面试高频指数：★★☆☆☆**

宏定义（#define）和 typedef 都是 C++ 语言中用于定义别名的方法，但它们有一些关键区别：
#### 1. 语法和实现机制：
宏定义 #define 在编译期间将宏展开，并替换宏定义中的代码。预处理器只进行简单的文本替换，不涉及类型检查。如：
```cpp
#define INT_VECTOR std::vector<int>
```
typedef 是一种类型定义关键字，用于为现有类型创建新的名称（别名）。
与宏定义不同，typedef 是在编译阶段处理的，有更严格的类型检查。
```cpp
typedef std::vector<int> IntVector;
```
#### 2. 作用域限制
宏定义没有作用域限制，只要在宏定义之后的地方，就可以使用宏。
通常用于定义常量、简单的表达式或简单的代码片段。
typedef 遵循 C++ 的作用域规则，可以受到命名空间、类等结构的作用域限制。
typedef 通常用于定义复杂类型的别名，使代码更易读和易于维护，如：
```cpp
typedef std::map<std::string, std::vector<int>> StringToIntVectorMap;
```
#### 3.   模板支持
宏定义不支持模板，因此不能用于定义模板类型别名。
typedef 可以与模板结合使用，但在 C++11 之后，推荐使用 using 关键字定义模板类型别名。
```cpp
// 使用 typedef 定义模板类型别名
template <typename T>
struct MyContainer {
    typedef std::vector<T> Type;
};

// 使用 using 定义模板类型别名（C++11 及以后）
template <typename T>
struct MyContainer {
    using Type = std::vector<T>;
};
```