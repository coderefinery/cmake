---
layout: episode
title: "Hello-world example"
teaching: 20
exercises: 0
questions:
  - "A question that this episode will answer?"
  - "Another question?"
objectives:
  - "This is one objective of this episode."
  - "This is another objective of this episode."
  - "Yet another objective."
  - "And not to forget this objective."
keypoints:
  - "This is an important key point."
  - "Another important key point."
  - "One more key point."
---

## Hello-world example

- We have a `hello.F90` program and wish to compile it to `hello.x`
- We create a file called `CMakeLists.txt` which contains

```cmake
cmake_minimum_required(VERSION 2.8 FATAL_ERROR)

project(HelloWorld)

enable_language(Fortran)

add_executable(hello.x hello.F90)
```

---

- We configure the project

```shell
$ mkdir build
$ cd build/
$ cmake ..

-- The Fortran compiler identification is GNU
-- Check for working Fortran compiler: /usr/bin/f95
-- Check for working Fortran compiler: /usr/bin/f95  -- works
-- Detecting Fortran compiler ABI info
-- Detecting Fortran compiler ABI info - done
-- Checking whether /usr/bin/f95 supports Fortran 90
-- Checking whether /usr/bin/f95 supports Fortran 90 -- yes
-- Configuring done
-- Generating done
-- Build files have been written to: /home/user/example/build
```

- We compile the code

```shell
$ make

Scanning dependencies of target hello.x
[100%] Building Fortran object CMakeFiles/hello.x.dir/hello.F90.o
Linking Fortran executable hello.x
[100%] Built target hello.x
```

- We are done
- In the following we will learn what happened here behind the scenes

---

## Building a Fortran project

- We have a simple Fortran project consisting of 3 files (`main.F90`, `foo.F90`, and `bar.F90`):

```fortran
program main

   use foo, only: print_foo
   use bar, only: print_bar

   implicit none

   call print_foo()
   call print_bar()

end program
```

---

## Building a Fortran project

- We can build them with the following simple `CMakeLists.txt`:

```cmake
cmake_minimum_required(VERSION 2.8 FATAL_ERROR)

project(MyProject)

enable_language(Fortran)

# this is where we will place the Fortran module files
set(CMAKE_Fortran_MODULE_DIRECTORY ${PROJECT_BINARY_DIR}/modules)

# the executable is built from 3 source files
add_executable(
    myproject.x
    main.F90
    foo.F90
    bar.F90
    )
```

- We can list the source files in any order
- We do not have to worry about module dependencies

---

## Building a Fortran project

- We build the project

```shell
$ mkdir build
$ cd build/
$ cmake ..
$ make
```

- There is nothing special about `build/` - we can do this instead:

```shell
$ cd /tmp/debug
$ cmake /path/to/source
$ make
```

- CMake looks for `CMakeLists.txt` and processes this file
- CMake puts everything into `${PROJECT_BINARY_DIR}`, does not pollute `${PROJECT_SOURCE_DIR}`
- We can build different binaries with the same source!

---

## Multi-language projects

- It is easy to support other languages (C, CXX):

```cmake
project(language-mix)

enable_language(Fortran CXX)

# tell CMake where to find header files
include_directories(${PROJECT_SOURCE_DIR}/include)

add_executable(
    mix.x
    mix/main.F90
    mix/sub.cpp
    mix/baz.c
    )

set_property(TARGET mix.x PROPERTY LINKER_LANGUAGE Fortran)
```

- That was easy
- But how can we select the compiler?
