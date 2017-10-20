---
layout: episode
title: "Making use of a new package - building an example from Intel Threaded Building Blocks"
teaching: 15
exercises: 0
questions:
  - Downloadable source code packages often contain a CMake configuration.
  - How can can a software library be used with CMake?
objectives:
  - Learn how to make use of find_packages()
  - Learn how to set and print variables
keypoints:
  - CMake ease integration of other software libraries.
---

## Downloading Intel Threaded Building Blocks ([TBB](https://github.com/01org/tbb/releases))
Use wget or curl, following the description below to download TBB or go to the release download page (link in the title) and select the package for your platform.

On a Mac:
```shell
$ mkdir tbb
$ cd tbb
$ curl -OL https://github.com/01org/tbb/releases/download/2018_U1/tbb2018_20170919oss_mac.tgz
$ tar xf tbb2018_20170919oss_mac.tgz
```

On a Linux:
```shell
$ mkdir tbb
$ cd tbb
$ wget -O tbb2018.tgz https://github.com/01org/tbb/releases/download/2018_U1/tbb2018_20170919oss_lin.tgz
$ tar xf tbb2018.tgz
```

We will build a TBB Getting Started example with the use of CMake. Copy the example C++ file to a
src subdirectory:
```
$ mkdir src
$ cp tbb2018_20170919oss/examples/GettingStarted/sub_string_finder/sub_string_finder.cpp src
```

Create two CMakeLists.txt files, one in the current directory and another one in the src sub-directory:
Here is the CMakeLists.txt for the current directory:
```cmake
cmake_minimum_required(VERSION 3.0)
project(tbb_tutorial)

# HINT to <prefix> for search path for Intel Threaded Building Blocks
find_package(TBB REQUIRED CONFIG HINTS tbb2018_20170919oss)

add_subdirectory(src bin)   # if output directory is not specified, the output will be placed in a directory OUTPUT_DIR/src
```

Here is the CMakeLists.txt in the src-subdirecotry:
```cmake
add_executable(substring.x sub_string_finder.cpp)
# TBB::tbb could also be used like: target_link_libraries(substring.x TBB::tbb)
target_link_libraries(substring.x ${TBB_IMPORTED_TARGETS})
```

Build the binary with cmake and make:
```shell
$ mkdir build
$ cd build
$ cmake ..
$ make
$ ./bin/substring.x
```

The crux in this CMake setup is the find_package(). If the TBB is not found CMake stops and return an error.
It is the 'REQUIRED' argument that stops the CMake processing if TBB is not found.

find_package() can be executed in different modes. The Module Mode is the default. Here we request 'CONFIG'.
Config modes means find_package will look for <Package>Config.CMake, in our case TBBConfig.cmake.

If you look at the download TBB library, you will find a CMake sub-directory which contains TBBConfig.cmake.
The HINTS statement take a path-prefix as argument, in our case a relative path. We could have used a full path.
find_package() will search for the config file ing <prefix>, <prefix>/CMake|cmake, share/cmake|CMake. In our
case it finds it under tbb2018_20170919oss/cmake.

If you download software which you want to use, search for a cmake config file in the  download software. 

### Bonus
Build and install the [Armadillo C++ Matrix library](http://arma.sourceforge.net/docs.html) and
use the steps shown here to build the [example program](http://arma.sourceforge.net/docs.html#example_prog).



---

## Variables
We can make the configuration process more verbose with message(). Here message() shows the values of variables set by project(). We also set our own variable ${HINT_PATH}
and use it in find_package()
```cmake
cmake_minimum_required(VERSION 3.0)
project(tbb_tutorial)
message("PROJECT_SOURCE_DIR: ${PROJECT_SOURCE_DIR} and PRJOECT_BINARY_DIR:${PROJECT_BINARY_DIR}")
set(HINT_PATH tbb2018_20170919oss)
# HINT to <prefix> for search path for Intel Threaded Building Blocks
find_package(TBB REQUIRED CONFIG HINTS ${HINT_PATH})

add_subdirectory(src bin)   # if output directory is not specified, the output will be placed in a directory OUTPUT_DIR/src

```

## More find functions
CMake provides several find functions, which you can use to search for files,
libraries, paths, header files, or entire packages:

- [find_file](https://cmake.org/cmake/help/latest/command/find_file.html)
- [find_library](https://cmake.org/cmake/help/latest/command/find_library.html)
- [find_path](https://cmake.org/cmake/help/latest/command/find_path.html)
- [find_program](https://cmake.org/cmake/help/latest/command/find_program.html)

Reading the documentation and doing some experimentation is needed to make use of
these functions.

### Bonus II
More on the CMake scripting language: [A 15 minute learning example](http://preshing.com/20170522/learn-cmakes-scripting-language-in-15-minutes/)
