---
layout: episode
title: "Compilers and compiler flags"
teaching: 10
exercises: 0
questions:
  - "How can we select the compiler?"
  - "How can we change compiler flags?"
objectives:
  - "Learn how to display and change compilers and compiler flags."
---

## How to specify the compiler

We can speciy the compilers like this:

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

## See actual compiler flags and link line

The default compilation output is nice and compact.
But sometimes we want to see the current compiler flags and the gory compiler output:

```shell
$ make clean
$ make VERBOSE=1
```

The link line is saved in `CMakeFiles/<target>.dir/link.txt`.

---
## CMake can find packages
Here is an example where we search for the package Armadillo. If you do not have Armadillo installed, you will get that "Armadillo not found!":
```cmake
cmake_minimum_required(VERSION 2.8)

find_package(Armadillo REQUIRED)
if (ARMADILLO_FOUND)
	message("Armadillo version: ${ARMADILLO_VERSION_MAJOR} ${ARMADILLO_VERSION_MINOR} ${ARMADILLO_VERSION_PATCH}")
else()
	message("Armadillo not found!!")
endif()
	
	
option(ENABLE_MPI "Enable MPI" OFF)

if (ENABLE_MPI)
   find_package(MPI)
   if (MPI_FOUND)
      include_directories(${MPI_INCLUDE_PATH})
      add_definititions(-DENABLE_MPI)
   else()
	message(FATAL_ERROR "Could not find any MPI installation, check $PATH")  # CMake Error, stop processing and generation
   endif()
endif()
```

My output, since I have Armadillo installed on my laptop:
```shell
ls
CMakeLists.txt
$ mkdir build && cd build
$ cmake ..
-- The C compiler identification is AppleClang 7.3.0.7030031
-- The CXX compiler identification is AppleClang 7.3.0.7030031
-- Check for working C compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/cc
-- Check for working C compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++
-- Check for working CXX compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Found Armadillo: /opt/local/lib/libarmadillo.dylib (found version "7.600.2") 
Armadillo version: 7 600 2
-- Configuring done
-- Generating done
-- Build files have been written to: /Users/bjornlin/src/cmake/find_packages/build
```
When enable the MPI_ENABLE variable, I get a error message:
```shell
$ cmake -DENABLE_MPI=ON ..
Armadillo version: 7 600 2
-- Could NOT find MPI_C (missing:  MPI_C_LIBRARIES MPI_C_INCLUDE_PATH) 
-- Could NOT find MPI_CXX (missing:  MPI_CXX_LIBRARIES MPI_CXX_INCLUDE_PATH) 
CMake Error at CMakeLists.txt:19 (message):
  Could not find any MPI installation, check $PATH


-- Configuring incomplete, errors occurred!
See also "/Users/bjornlin/src/cmake/find_packages/build/CMakeFiles/CMakeOutput.log".
$

```