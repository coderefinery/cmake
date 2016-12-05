---
layout: episode
title: "Compilers and compiler flags"
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

## How to set the compiler

- We set the compilers like this:

```shell
$ FC=gfortran CC=gcc CXX=g++ cmake ..
$ make
```

- Or via export:

```shell
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

- Similarly you can set `CMAKE_C_FLAGS` and `CMAKE_CXX_FLAGS`
- This is how we set CPP definitions:

```cmake
add_definitions(-DVAR_SOMETHING -DENABLE_DEBUG -DTHIS_DIMENSION=137)
```

---

## Controlling the build type

- We can select the build type on the command line:

```shell
$ cd build
$ cmake -DCMAKE_BUILD_TYPE=Debug ..
$ make
```

- It is often useful to set

```cmake
# we default to Release build type
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "Release")
endif()
```

---

## See actual compiler flags and link line

- The default compilation output is nice and compact
- But sometimes we want to see the current compiler flags and the gory compiler output

```shell
$ make VERBOSE=1
```

- The link line is saved in `CMakeFiles/<target>.dir/link.txt`
