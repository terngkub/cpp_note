# My note on Professional CMake

## Chapter 1 Introduction
Process
1. Configure (CMake)
2. Generate (CMake)
3. Build
4. Test (CTest)
5. Package (CPack)

## Chapter 2 Setting Up A Project
Given the project tree
```
BASE_DIR
- source
- build
```

the commands will be
```
mkdir build
cd build

# Configure and generate
cmake -G "Unix Makefiles" ../source

# Use CMake to run build tool
cmake --build . --config Release --target MyApp
```

## Chapter 3 Minimal Project
```
# comment
cmake_minimum_required(VERSION 3.2)
project(MyApp)
add_executable(myExe main.cpp)
```

## Chapter 4 Building Simple Targets

### Defining Libraries
```
add_library(targetName [STATIC | SHARED | MODULE] source1)
```
Best practice is to leave the STATIC/SHARED/MODULE keyword out and specify it when configuring.
```
cmake -DBUILD_SHARED_LIBS=YES /path/to/source
```

### Linking Targets
Link modes:
* PRIVATE - A use B in its internal implementation
* PUBLIC - to use A, B is required (for example A has a function with a parameter containing a type from B)
* INTERFACE - *** I don't understand this ***
```
target_link_libraries(targetName
    <PRIVATE|PUBLIC|INTERFACE> item1 [...]
    [<PRIVATE|PUBLIC|INTERFACE> item2 [...]]
    ...
)
```

Example
```
add_library(collector src1.cpp)
add_library(algo src2.cpp)
add_library(engine src3.cpp)
add_library(ui src4.cpp)
add_executable(myApp main.cpp)
target_link_libraries(collector
    PUBLIC  ui
    PRIVATE algo engine
)
target_link_libraries(myApp PRIVATE collector)
```
