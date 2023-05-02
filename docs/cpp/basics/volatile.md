# volatile 的作用

**面试高频指数：★★★☆☆**

`volatile`是C语言中的一个关键字，用于修饰变量，表示该变量的值可能在任何时候被外部因素更改，例如硬件设备、操作系统或其他线程。

当一个变量被声明为`volatile`时，编译器会禁止对该变量进行优化，以确保每次访问变量时都会从内存中读取其值，而不是从寄存器或缓存中读取。

避免因为编译器优化而导致出现不符合预期的结果。



举例:

```cpp
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>

volatile int counter = 0;

void *increment(void *arg) {
    for (int i = 0; i < 100000; i++) {
        counter++;
    }
    return NULL;
}

int main() {
    pthread_t thread1, thread2;

    // 创建两个线程，分别执行increment函数
    pthread_create(&thread1, NULL, increment, NULL);
    pthread_create(&thread2, NULL, increment, NULL);

    // 等待两个线程执行完毕
    pthread_join(thread1, NULL);
    pthread_join(thread2, NULL);

    printf("Counter: %d\n", counter);

    return 0;
}

```

上面声明了一个`volatile int`类型的全局变量`counter`，并创建了两个线程。

每个线程都会对`counter`变量进行100000次自增操作。

由于`counter`变量被声明为`volatile`，编译器不会对其进行优化，确保每次访问都会从内存中读取值。

虽然`volatile`关键字可以确保编译器不对变量进行优化，但上面任然存在并发问题，`counter++`操作仍然可能导致数据不一致。

为了解决这个问题，需要使用互斥锁、原子操作或其他同步机制。


