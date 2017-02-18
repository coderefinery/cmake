---
layout: episode
title: "Compilers, compiler flags and find-functions "
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
## CMake find functions
As you develop code and your code base grow, you will most certainly depend upon external packages or libraries. When you share your code, which you will because you do research which is repeatable && verifiable (in addition there is other coming after you who will depend upon your work), you can not assume where these packages or libraries are installed.

CMake provides several find functions, which you can use to search for files or libraries:
```cmake
find_file()
find_library()
find_path()
find_program()
find_package()
```
---
## Example with find_library() and find_path()
```cmake
# find libtiff, looking in some standard places
cmake_minimum_required(VERSION 2.8)

find_library (TIFF_LIBRARY
             NAMES tiff tiff2
	     PATHS /opt/local/lib /opt/local/Library usr/local/lib /usr/lib
	     )

# find tiff.h looking in some standard places
find_path(TIFF_INCLUDES tiff.h
  /opt/local/include
  /usr/local/include
  /usr/include
  )

#Typically you would like to use the library or header file as part of the
# linking or compilation, as shown in the commented region below
#include_directories(${TIFF_INCLUDE}
#add_executable(mytiff mytiff.c)
#target_link_libraries(myprogram ${TIFF_LIBRARY})

if (TIFF_LIBRARY_NOTFOUND)
  message("Tiff library not found")
else()
  message("Tiff library: ${TIFF_LIBRARY}")
endif()

if (TIFF_INCLUDES_NOTFOUND)
  message("Tiff header file not found")
else()
  message("Tiff include PATH: ${TIFF_INCLUDES}")
endif()
```
If you have tiff available in any of the paths searche your output will be like this:
```shell
$ cmake ..
Tiff library: /opt/local/lib/libtiff.dylib
Tiff include PATH: /opt/local/include
-- Configuring done
-- Generating done
-- Build files have been written to: /Users/bjornlin/src/cmake/find_library/build

```

## Example with find package()
Here is an example where we search for the package Armadillo. If you do not have Armadillo installed, you will get "Armadillo not found!":
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
      add_definitions(-DENABLE_MPI)
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
When enable the MPI_ENABLE variable, I get a error message since I do not have MPI-library installed:
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
---
## Cheking for system specific constructs
If we are depending upon specific constructs, we can check that these exists:
```cmake
cmake_minimum_required (VERSION 2.8 FATAL_ERROR)

include (CheckFunctionExists)   # Load and execute the module CheckFunctionExits.cmake
include (CheckStructHasMember)  # # Load and execute the module CheckStructHasMember.cmake

CHECK_FUNCTION_EXISTS(vsnprintf VSNPRINTF_EXISTS)
if (NOT VSNPRINTF_EXISTS)
   message (SEND_ERROR "vsnprintf not available on this build machine")
endif ()


CHECK_STRUCT_HAS_MEMBER("struct __sFILE" _lbfsize stdio.h HAS_SxBUF)
if (NOT HAS_SBUF)
   message (SEND_ERROR "__sbuf field not available in stdio.h")
   endif()
   
CHECK_STRUCT_HAS_MEMBER("struct rusage" ru_stime wait.h HAS_STIME)
if (NOT HAS_STIME)
   message (SEND_ERROR " ru_stime field not available in struct rusage")
endif()
```
The Check for HAS_STIME fails on my laptop:
```shell
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
-- Looking for vsnprintf
-- Looking for vsnprintf - found
-- Performing Test HAS_SBUF
-- Performing Test HAS_SBUF - Success
-- Performing Test HAS_STIME
-- Performing Test HAS_STIME - Failed
CMake Error at CMakeLists.txt:20 (message):
   ru_stime field not available in struct rusage


-- Configuring incomplete, errors occurred!
```
Here is from a Linux system:
```shell
-- The C compiler identification is GNU 4.3.4
-- The CXX compiler identification is GNU 4.3.4
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Looking for vsnprintf
-- Looking for vsnprintf - found
-- Performing Test HAS_SBUF
-- Performing Test HAS_SBUF - Failed
CMake Error at CMakeLists.txt:15 (message):
  __sbuf field not available in stdio.h


-- Performing Test HAS_STIME
-- Performing Test HAS_STIME - Success
-- Configuring incomplete, errors occurred!
```
Since message() use SEND_ERROR, the processing of the CMakeLists.txt continues despite an error.
