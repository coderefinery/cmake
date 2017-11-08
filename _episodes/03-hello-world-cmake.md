---
layout: episode
title: "Hello-world example using CMake"
teaching: 20
exercises: 0
questions:
  - How are CMake projects configured and built?
  - What is an out of source compilation?
objectives:
  - Learn how to configure and build CMake projects.
keypoints:
  - We always compile out of source.
  - The name and path of the build directory can be changed.
  - When configuring we point CMake at the location of the CMakeLists.txt file.
---

## Hello-world example

We start with our now familiar C++ program:

```cpp
#include <iostream>

int main()
{
    std::cout << "Hello World!" << std::endl;
}
```

Create a fresh directory and save the C++ code to a file called
`hello.cpp`.

We wish to compile this code to `hello.x`.

For this we create a file called `CMakeLists.txt` which contains:

```cmake
# stop if cmake version is below 3.0
cmake_minimum_required(VERSION 3.0 FATAL_ERROR)

# project name and enable C++ support
project(hello CXX)

# we define the executable and its dependencies
add_executable(hello.x hello.cpp)
```

Your directory should look like this:

```shell
$ ls

CMakeLists.txt	hello.cpp
```

Now we create a build directory (out of source compilation), change to it,
and configure the project:

```shell
$ mkdir build
$ cd build/
$ cmake ..

-- The CXX compiler identification is GNU 7.2.0
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /home/user/example/build
```

Now we are ready to compile the code:

```shell
$ make

Scanning dependencies of target hello.x
[ 50%] Building CXX object CMakeFiles/hello.x.dir/hello.cpp.o
[100%] Linking CXX executable hello.x
[100%] Built target hello.x
```

Done. In the following we will learn what happened here behind the scenes.

Have you used `git init`? Good! You probably want to ignore the `build/` directory.

---

## Name and location of the build directory

We have configured and compiled the code like this:

```shell
$ mkdir build
$ cd build/
$ cmake ..
$ make
```

But there is nothing special about `build/` - we could have done this instead:

```shell
$ mkdir -p /tmp/debug
$ cd /tmp/debug
$ cmake /path/to/source
$ make
```

---

## Benefits of out-of-source compilation

- CMake looks for `CMakeLists.txt` and processes this file
- CMake puts everything into `${PROJECT_BINARY_DIR}` and does not pollute `${PROJECT_SOURCE_DIR}`
- We can build different binaries with the same source:
  - Sequential and parallel builds
  - Debug build or optimized build
  - Production and debugging compilations
