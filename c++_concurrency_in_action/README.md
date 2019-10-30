# My Note on C++ Concurrency in Action

## Chapter 1: Hello, World of concurrency in C++!

Concurrency vs Parallelism
Both mean doing something at the same time
Concurrency: different task (like one thread run game loop and another thread waiting for player input)
Parallelism: same task (like reading the same file on different part to make the reading faster)


## Chapter 2: Managing threads

### The basics

```
#include <thread>

// create and run thread
std::thread t1{some_function};

// run with argument(s)
int arg = 42;
std::thread t2{some_function2, arg};

// wait for the thread to finish
t1.join();

// or keep running thread in the background
t2.detach():
```

### Reference arguments
std::thread passes arguments by values. When function parameters are references, the function will receive value instead of reference which results in compilation error. To fix this problem, we have to use std::ref which wraps reference with std::reference_wrapper and make it copyable.

```
void func(int & i)
{
    // do something
}

void foo()
{
    int i = 42;
    std::thread(func, std::ref(i));
}
```

### Rvalue or move-only arguments
