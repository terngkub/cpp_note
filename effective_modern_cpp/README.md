# My Note on Effective Modern C++

## Chapter 1: Deducing Types

### Item 1: Understand template type deduction.

```
template<typename T>
void f1(T & param);

teamplate<typename T>
void f2(const T & param);

template<typename T>
void f3(T & param)

int x = 27;
const int cx = x;
const int & rx = x;
const int * px = &x;

// Case 1: ref param
// T ignores &
// Param always have &. If there is const, add const.

f1(x);      // T = int          Param = int &
f1(cx);     // T = const int    Param = const int &
f1(rx);     // T = const int    Param = const int &

// Case 2: const ref param
// T ignores const &.
// Param always have const &.

f2(x);      // T = int          Param = const int &
f2(cx);     // T = int          Param = const int &
f3(rx);     // T = int          Param = const int &

// Case 3: pointer param
// T ignores pointer.
// Param always have pointer. If there is const, add const.

f(&x);      // T = int          Param = int *
f(px);      // T = const int    Param = const int *
```


### Item 2: Understand auto type deduction.

`auto` has the same type deduction as template T.
Given below code, it will set param type to the type of value and the auto = T.
```
auto x = value;
```


### Item 3: Understand decltype.

decltype(name/expr) tell what type name/expr is.
Unlike `auto`, `decltype` also tell constnesss and referenceness.
```
Widget w;
const Widget & cw = w;
auto myWidget1 = cw;            // type = Widget
decltype(auto) myWidget2 = cw;  // type = const Widget &
```

This can be useful when use with template. (C++14)
```
template<typename Container, typename Index>
decltype(auto) authAndAccess(Container & c, Index i)
{
    authenticateUser();
    return c[i];
}
```


### Item 4: Know how to view deduced types.

```
std::cout << typeid(x).name();
```


## Chapter 2: auto

### Item 5: Prefer auto to explicit type declarations.

Nothing special.


### Item 6: Use the explicitly typed initializer idiom when auto deduces undesired types.

example
```
auto idx = static_cast<int>(d * c.size());
```


## Chapter 3: Moving to Modern C++

### Item 7: Distinguish between () and {} when creating objects.

3 ways of initialization in C++
1) int x(0);
2) int x = 0;
3) int x{0}; or int x = {0};

Braced initialization features:
* narrowing conversions
    ```
    double x, y, z;
    int sum{x + y + z}; // error
    int sum = x + y + z; // ok
    ```
* avoid function declaration with default contructor
    ```
    Widget w(); // declare function w that return widget
    Widget w{}; // initialize w with default constructor
    ```
Down side:
* compiler prefer std::initializer_list contructor than normal constructor resulting in implicit casting in some cases.

Empty braces count as default constructor not empty std::initilizer_list.
```
Widget w{};     // default constructor
Widger w({});   // empty std::initilizer_list
```


### Item 8: Prefer nullptr to 0 and NULL.

Passing 0 or NULL never call pointer overload.
0 or NULL are also interpreted as int in template type.


### Item 9: Prefer alias declarations to typedefs.

Syntax
```
using alias = type
```
alias declarations can be templateized.
```
template<typename T>
using alias = container<T>
```


### Item 10: Prefer scoped enums to unscoped enums.

Syntax
```
enum class enum_name {...}
```
Benefits:
* not pollute the scope
```
enum Wine {white, red}
int white = 42; // error, white is already used.
```
* not integral type
* can be forward declare
* better for dependency


### Item 11: Prefer deleted functions to private undefined ones.

Syntax
```
public:
    f(...) = delete
```
Benefits:
* Use with overload to prevent implicit argument casting.
* Use with template to prevent void * which can't be deferenced.


### Item 12: Declare overriding functions override.

Nothing special about override.
Member function reference
```
class Widget
{
    f() &;  // can be invoke only when the object is lvalue
    f() &&; // can be invoke only when the object is rvalue
}
```


### Item 13: Prefer const_iterators to iterators.

Syntax
```
container.cbegin()
container.cend()
```


### Item 14: Declare functions noexcept if they won't emit exceptions.

Nothing special.


## Item 15: Use constexpr whenever possible

* constexpr objects are const and are initialized with values known during compilation.
* constexpr functions can produce compile-time result when called with arguments whose values are known during compilation.


## Item 16: Make const member function thread safe.
Use std::atomic for single variable.
Use std::mutex for multiple variables.


## Item 17: Understand special member function generation.

Special member functions that compilers may generate on their own:
1) default constructor
2) destructor
3) copy constructor
4) copy assignment operation
5) move constructor
6) move assignment operation

* Move operations are generated only for classes lacking explicitly declared move operations, copy operations, and destructor.
* The copy constructor is generated only for classes lacking an explicitly declared copy constructor, and it's deleted if a move is declared. Same thing for the copy assignment operation.


## Chapter 4: Smart Pointer


### Item 18: Use std::unique_ptr for exclusive-ownership resource management.

std::unique_ptr can't be copied.
We have to move it.


### Item 19: Use std::shared_ptr for shared-ownership resource management.
Nothing special.


### Item 20: Use std::weak_ptr for std::shared_ptr-like pointer that can dangle.

Think of it as std::shared_ptr that don't add the count.
Use case example is cache. First, store cache with std::shared_ptr. Then point std::weak_ptr to it. After that, if the cache is there, use it. If it isn't there (expired and deleted), std::weak_ptr will be nullptr so we know that the cache is gone.


### Item 21: Prefer std:make_unique and std::make_shared to direct use of new.

Reasons to use make:
* eliminate source code duplication
* exception safety
    Compilers are not required to generate code that executes in order. `new Widget` can be executed first. Then `computePriority()` is executed but throw an exception. This result in a leak on `new Widget`.
    ```
    processWidget(std::shared_ptr<Widget>(new Widget), computePriority()));

    processWidget(std::make_shared<Widget>(), computePriority());
* smaller and faster code

For both unique and shared ptr, there are situations where make is inappropriate
* custom deleter
* braced initializer

For std::shared_ptr, there are some more situations where make is inappropriate
* classes with custom memory management
    std::shared_ptr allocate size for object and control block. When the class have custom memory management, the allocate size isn't matched with the one the custom memory management allocate.
* system with memory conserns, very large objects, and std::weak_ptrs that outlive the corresponding std::shared_ptrs.
    When the count goes to zero, the object is destroyed but the memory is still there for std::weak_ptr.


### Item 22: When using the Pimpl Idiom, define special member functions in the implementation file.

The Pimpl Idiom decreases build times by reducing compilation dependencies between class clients and class implementations.

```
#include "gadget.h"
#include <string>
#include <vector>

class Widget
{
public:
    Widget();
    ~Widget();

private:
    std::string name;
    std::vector<double> data;
    Gadget g1, g2, g3;
};
```

With Pimpl Idiom, class definition store a pointer to incomplete type. Compilers know the size of pointer and don't need to know the size of type. Below is C++98 code.

Declaration
```
class Widget
{
public:
    Widget();
    ~Widget();

private:
    struct Impl;
    Impl *pImpl;
};
```

Implementation
```
struct Impl
{
    std::string name;
    std::vector<double> data;
    Gadget g1, g2, g3;
};

Widget::Widget() : pImpl(new Widget) {};
Widget::~Widget() { delete pImpl; }
```

In C++11, we have std::unique_ptr which is automatically destroy the object for us so it's better. However, this will lead problem because destructor, copy and move of std::unique_ptr require complete type. The fix is to declare them at the header and then implement them under struct implementation.

Declaration
```
class Widget
{
public:
    Widget();
    ~Widget();
    Widget(Widget&& rhs) noexcept;
    Widget& operator=(Widget&& rhs) noexcept;

private:
    struct Impl;
    std::unique_ptr<Impl> pImpl;
};
```
Implementation
```
struct Impl
{
    std::string name;
    std::vector<double> data;
    Gadget g1, g2, g3;
};

Widget::Widget() : pImpl(std::make_unique<Widget>()) {};
Widget::~Widget() = default;
Widget(Widget&& rhs) noexcept = default;
Widget& operator=(Widget&& rhs) noexcept = default;

```

## Chapter 5: Rvalue references, Move semantics, and Perfect Forwarding

### Item 23: Understand std::move and std::forward.

std::move cast value to rvalue
std::forward cast value to
* lvalue if it binds with lvalue
* rvalue if it binds with rvalue


### Item 24: Distinguish universal references from rvalue references.

Universal ref can reference to lvalue or rvalue.

It appears in place that have type deduction
* auto && name = value;
* template<typename T>
  func(T&&)

Use std::forward with universal ref to pass the value to function that have lvalue and rvalue overload.


### Item 25: Use std::move on rvalue references, std::forward on universal references.

Returning local variable has Return Value Optimization (RVO) so we shouldn't use std::move on it.

### Item 26:

### Item 27:

### Item 28:


## Chapter 8: Tweaks

### Item 41:

### Item 42: Consider emplacement instead of insertion.

`emplace` pass argument(s) to constructor so it can avoid contruction and destruction of temporary object.
`emplace` is most likely to be faster than insert when
1) the value being added is constructed into container, not assigned
2) the argument type(s) passed differ from type held by the container
3) the container won't reject the value being added due to it being a duplicate.