# My Note on Effective C++

## Item 1 View C++ as a federation of languages

* C
* Object-Oriented C++
* Template C++
* The STL


## Item 2 Prefer `const`s, `enum`s and `inline`s to `#define`s

Reasons
* The symbolic name of #define may not be seen by compilers because preprocessor can remove it
* Macro could result in multiple copies of data.

