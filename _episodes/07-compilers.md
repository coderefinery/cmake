---
layout: episode
title: "Compilers and compiler flags"
teaching:  5
exercises: 0
questions:
  - How can we select the compiler?
  - How can we change compiler flags?
objectives:
  - Learn how to display and change compilers and compiler flags.
---

## How to specify the compiler

We can specify the compilers like this:

```shell
$ cd build
$ FC=gfortran CC=gcc CXX=g++ cmake ..
$ make
```

Or by exporting the corresponding environment variables:

```shell
$ cd build
$ export FC=gfortran
$ export CC=gcc
$ export CXX=g++
$ cmake ..
$ make
```

---

## Controlling compiler flags

- We can define compiler flags for different compilers and build types

```cmake
if(CMAKE_Fortran_COMPILER_ID MATCHES Intel)
    set(CMAKE_Fortran_FLAGS         "${CMAKE_Fortran_FLAGS} -Wall"
    set(CMAKE_Fortran_FLAGS_DEBUG   "-g -traceback")
    set(CMAKE_Fortran_FLAGS_RELEASE "-O3 -ip -xHOST")
endif()

if(CMAKE_Fortran_COMPILER_ID MATCHES GNU)
    set(CMAKE_Fortran_FLAGS         "${CMAKE_Fortran_FLAGS} -Wall")
    set(CMAKE_Fortran_FLAGS_DEBUG   "-O0 -g3")
    set(CMAKE_Fortran_FLAGS_RELEASE "-Ofast -march=native")
endif()

...
```

Similarly you can set `CMAKE_C_FLAGS` and `CMAKE_CXX_FLAGS`.

This is how we set preprocessor definitions:

```cmake
add_definitions(-DVAR_SOMETHING -DENABLE_DEBUG -DTHIS_DIMENSION=137)
```

---

## Controlling the build type

CMake distinguishes the following build types `${CMAKE_BUILD_TYPE}`:
- Debug
- Release
- RelWithDebInfo
- MinSizeRel

We can select the build type on the command line:

```shell
$ cd build
$ cmake -DCMAKE_BUILD_TYPE=Debug ..
$ make
```

It is often useful to set:

```cmake
# we default to Release build type
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "Release")
endif()
```

---

## What are the actual compiler flags and link line?

The default compilation output is nice and compact.  But sometimes we want to
see the current compiler flags and the gory compiler output:

```shell
$ make clean
$ make VERBOSE=1
```

The link line is saved in `CMakeFiles/<target>.dir/link.txt`.
