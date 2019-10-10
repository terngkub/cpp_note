# My Note on C++ Concurrency in Action

## Chapter 1: Hello, World of concurrency in C++!

Concurrency vs Parallelism
Both mean doing something at the same time
Concurrency: different task (like one thread run game loop and another thread waiting for player input)
Parallelism: same task (like reading the same file on different part to make the reading faster)


## Chapter 2: Managing threads

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