---
layout: episode
title: "Exercise: Create a CMake framework for an example project"
teaching: 20
exercises: 0
questions:
  - "What is a typical layout for a CMake framework?"
  - "How can we build static and shared libraries?"
  - "How can we get version information into the binary output for reproducibility?"
  - "How does testing work in CMake?"
  - "How can we create an installer and packager?"
---

## Creating a CMake framework for an example project

In this exercise we will CMake-ify an [example
project](https://github.com/bast/fizz-buzz).  This exercise can be interesting
for people who use Makefiles or Autotools.

On the way we will experiment with some CMake features.


### Background

Inspired by a [Project Euler exercise](https://projecteuler.net/problem=1) and
based on the [popular game](https://en.wikipedia.org/wiki/Fizz_buzz) and
popular programming job interview question where you have to say/print "Fizz" if a
natural number is divisible by 3 and "Buzz" if it is divisible by 5:

```
1
2
Fizz
4
Buzz
Fizz
7
8
Fizz
Buzz
11
Fizz
13
14
Fizz Buzz
...
```

We wish to build a code which computes the "Fizz-buzz sum" for a given natural
number N, summing all natural numbers <= N which are multiples of 3 or 5. For
N=10 the sum is 3+5+6+9+10 = 33.

The source code and unit tests are there:

```shell
.
|-- LICENSE.md
|-- README.md
|-- src
|   |-- divisible.f90
|   |-- fizz_buzz.f90
|   |-- fizz_buzz.h
|   `-- main.cpp
`-- test
    |-- fizz_buzz.cpp
    `-- main.cpp
```

You can also browse them [on the web](https://github.com/bast/fizz-buzz).


### Tasks

- Build a shared library.
- Build and link the main program.
- Build the unit tests and link against [Google Test](https://github.com/google/googletest).
- Define a version number inside CMake and print it to the output of the executable.
- Print the Git hash to the output of the executable.
- Create an installer so the program can be installed properly (GNU standards).
- Create a DEB or RPM package (if relevant for your distribution).

---

## Building the sources

First we create a file called `CMakeLists.txt` containing:

```cmake
cmake_minimum_required(VERSION 2.8 FATAL_ERROR)

project(fizz-buzz)

enable_language(CXX)
enable_language(Fortran)

add_subdirectory(src)
```

We also create a file `src/CMakeLists.txt` containing:

```cmake
add_library(
    fizz_buzz
    fizz_buzz.f90
    fizz_buzz.h
    divisible.f90
    )

add_executable(
    fb.x
    main.cpp
    )

target_link_libraries(
    fb.x
    fizz_buzz
    )
```

```shell
$ mkdir build
$ cd build
$ cmake ..
$ make

Scanning dependencies of target fizz_buzz
[ 20%] Building Fortran object src/CMakeFiles/fizz_buzz.dir/divisible.f90.o
[ 40%] Building Fortran object src/CMakeFiles/fizz_buzz.dir/fizz_buzz.f90.o
[ 60%] Linking Fortran static library libfizz_buzz.a
[ 60%] Built target fizz_buzz
Scanning dependencies of target fb.x
[ 80%] Building CXX object src/CMakeFiles/fb.x.dir/main.cpp.o
[100%] Linking CXX executable fb.x
[100%] Built target fb.x
```

Let us change one thing to get a shared library: In `src/CMakeLists.txt` lest us modify:

```cmake
add_library(
    fizz_buzz
    SHARED  # we add this line
    fizz_buzz.f90
    fizz_buzz.h
    divisible.f90
    )
```

And recompile:

```shell
$ make

-- Configuring done
-- Generating done
-- Build files have been written to: /home/bast/tmp/fizz/build
[ 20%] Building Fortran object src/CMakeFiles/fizz_buzz.dir/divisible.f90.o
[ 40%] Building Fortran object src/CMakeFiles/fizz_buzz.dir/fizz_buzz.f90.o
[ 60%] Linking Fortran shared library libfizz_buzz.so
[ 60%] Built target fizz_buzz
[ 80%] Linking CXX executable fb.x
[100%] Built target fb.x
```

---

## Introduce a tweak for a specific architecture

It turns out that we will need the following tweak for Mac:

```cmake
# workaround for CMP0042 warning on Mac
if(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
    if(NOT DEFINED CMAKE_MACOSX_RPATH)
        set(CMAKE_MACOSX_RPATH ON)
    endif()
endif()
```

We could place it into the main `CMakeLists.txt` but we will rather place this
into a separate file `cmake/arch.cmake` (name is our choice) and include it in
the main `CMakeLists.txt`. We do this to have a cleaner and more modular
structure of the CMake code.

Save the following code into `cmake/arch.cmake`:

```cmake
message(STATUS "My system is ${CMAKE_SYSTEM_NAME} and my processor is ${CMAKE_HOST_SYSTEM_PROCESSOR}")

# workaround for CMP0042 warning on Mac
if(${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
    if(NOT DEFINED CMAKE_MACOSX_RPATH)
        set(CMAKE_MACOSX_RPATH ON)
    endif()
endif()
```

Now we need to include this in the main `CMakeLists.txt`:

```cmake
cmake_minimum_required(VERSION 2.8 FATAL_ERROR)

project(fizz-buzz)

enable_language(CXX)
enable_language(Fortran)

# tell CMake where to find *.cmake module files
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${PROJECT_SOURCE_DIR}/cmake)

include(arch)

add_subdirectory(src)
```

Then try it out. On my system I get:

```shell
$ make

-- My system is Linux and my processor is x86_64
...
```

---

## Build the unit tests and link against [Google Test](https://github.com/google/googletest).



---

## Define a version number inside CMake and print it to the output of the executable.


---

## Print the Git hash to the output of the executable.


---

## Create an installer so the program can be installed properly (GNU standards).


---

## Create a DEB or RPM package (if relevant for your distribution).



