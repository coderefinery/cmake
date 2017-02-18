---
layout: episode
title: "Hello-world example using CMake"
teaching: 20
exercises: 0
questions:
  - "How are CMake projects configured and built?"
  - "What is an out of source compilation?"
objectives:
  - "Learn how to configure and build CMake projects."
keypoints:
  - "We always compile out of source."
  - "The name and path of the build directory can be changed."
  - "When configuring we point CMake at the location of the CMakeLists.txt file."
---

## CMake Motivation

- When you move your application or code base to another platform, you will need to modify your Makefiles or write a configuration script.
- If your software is used on several platforms, you easily end up doing almost identical modifications in several places.
- On Microsoft Windows you may have to use a separate build systems.
- Your changes in the Makefiles do rarely apply to Microsoft Windows.

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
# stop if cmake version is below 2.8
cmake_minimum_required(VERSION 2.8 FATAL_ERROR)

# project name
project(hello)

# we enable C++ support
enable_language(CXX)

# we define the executable and its dependencies
add_executable(hello.x hello.cpp)
```

Your subdirectory should like this:
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

-- The C compiler identification is GNU 6.2.1
-- The CXX compiler identification is GNU 6.2.1
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
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

## Build directories
If your source code structure is something like:
```shell
 foo/
	subdir1
	subdir2
```
CMAKE per default supports an out of source structure which is similar:
```shell
foobin/
	subdir1
	subdir2
```
If you need to support several architectures, the CMAKE writers suggest a structure like:
```shell
projectfoo/
	foo/
		subdir1
		subdir2
	foo-linux/
		subdir1
		subdir2
	foo-osx/
		subdir1
		subdir2
	foo-solaris/
		subdir1
		subdir2
```
According to the CMAKE writers, the following structure will not work well with CMake and it should be avoided:
```shell
#The JAM configuring and building system generates binaries directory structure like this
# Should be AVOIDED with CMake
projectfoo/
	subdir1/
		linux
		osx
		solaris
		hpux
	subdir2/
		linux
		osx
		solaris
	        hpux

# Should be AVOIDED with CMake
```
---
Let us develop our hello world example a little bit more. We move the source
code to a subdirectory and remove the previous build subdirectory. CMake will
visit the source directory and generate the build files accordingly:

```shell
$ mkdir src
$ mv hello.cpp src
$ rm -rf build
```

In our file CMakeLists.txt the add_executable is changed to:
```CMake
add_executable(hello.x src/hello.cpp)
```

In addition we copy the produced binary to its own subdirectory. Add the following to the end of the CMakeLists.txt:
```cmake
# add the custom command to copy it
add_custom_command ( TARGET hello.x
	POST_BUILD
	COMMAND ${CMAKE_COMMAND} ARGS -E chdir .. mkdir bin
	COMMAND ${CMAKE_COMMAND} ARGS -E copy $<TARGET_FILE:hello.x> ${PROJECT_BINARY_DIR}/../bin
)
```

This copy operation is a bit artificial. It is most for illustrating the point with commands which can be used on any platform. CMake provides a few platform independent custom commands:
+ **chdir dir commands args**
+ **copy file destination**
+ **copy_if_different in-file out-file**
+ **remove file1 file2 ...**
+ **echo string**
+ **time commands**

For more advanced platform independent processing, search for a scripting
language like Python or Perl (find_package(perl), find_package(python)). Of
course then your application installation get depending upon other packages.

---

## Benefits of using CMake

- CMake looks for `CMakeLists.txt` and processes this file
- CMake puts everything into `${PROJECT_BINARY_DIR}` and does not pollute `${PROJECT_SOURCE_DIR}`
- We can build different binaries with the same source:
  - Sequential and parallel builds
  - Debug build or optimized build
  - Production and debugging compilations

*(following points taken from the book "Mastering CMake", K. Martin, B. Hoffman, Kitware)*

* The ability to search for programs, libraries and header files required by the software being built
* The ability to build in a directory tree outside the source code directory tree
* The ability to build complex custom commands for automatically generated files such as  SWIG wrapper generators
* The ability to select optional components at configuration time
* The ability to automatically generate workspaces and projects from a simple text file
* The ability to switch between static and shared builds
* Automatic generation of file dependencies and support for parallel builds on most platforms
* The ability to test for byte-order and other hardware-specific characteristics
* A single set of build configuration files that work on all platforms
* Ability to build shared libraries on all platforms that support it
* Ability to configure files with system-dependent information, such as the location of data files and other information
