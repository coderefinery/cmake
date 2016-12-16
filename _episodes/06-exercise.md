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


### Solution

You can find a solution on the [solution branch](https://github.com/bast/fizz-buzz/tree/solution).

---

## Building the sources

First we create a file called `CMakeLists.txt` containing:

```cmake
cmake_minimum_required(VERSION 2.8 FATAL_ERROR)

project(fizz-buzz)

enable_language(CXX)
enable_language(Fortran)

# specify where to place binaries and libraries
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY
    ${CMAKE_BINARY_DIR}/bin
    )
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY
    ${CMAKE_BINARY_DIR}/lib
    )

add_subdirectory(src)
```

We also create a file `src/CMakeLists.txt` containing:

```cmake
add_executable(
    fb.x
    main.cpp
    fizz_buzz.f90
    fizz_buzz.h
    divisible.f90
    )
```

```shell
$ mkdir build
$ cd build
$ cmake ..
$ make

Scanning dependencies of target fb.x
[ 25%] Building Fortran object src/CMakeFiles/fb.x.dir/divisible.f90.o
[ 50%] Building Fortran object src/CMakeFiles/fb.x.dir/fizz_buzz.f90.o
[ 75%] Building CXX object src/CMakeFiles/fb.x.dir/main.cpp.o
[100%] Linking CXX executable ../bin/fb.x
[100%] Built target fb.x
```

Let us rewrite the `src/CMakeLists.txt` a bit to isolate the library, we also ask for a shared library:

```cmake
add_library(
    fizz_buzz
    SHARED
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

And recompile:

```shell
$ make

-- Configuring done
-- Generating done
-- Build files have been written to: /home/bast/tmp/fizz-buzz/build
Scanning dependencies of target fizz_buzz
[ 20%] Building Fortran object src/CMakeFiles/fizz_buzz.dir/divisible.f90.o
[ 40%] Building Fortran object src/CMakeFiles/fizz_buzz.dir/fizz_buzz.f90.o
[ 60%] Linking Fortran shared library ../lib/libfizz_buzz.so
[ 60%] Built target fizz_buzz
Scanning dependencies of target fb.x
[ 80%] Building CXX object src/CMakeFiles/fb.x.dir/main.cpp.o
[100%] Linking CXX executable ../bin/fb.x
[100%] Built target fb.x
```

We have now managed to compile a binary to `build/bin/fb.x` and a shared
library to `build/lib/libfizz_buzz.so`.

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
# ... rest of CMakeLists.txt

# tell CMake where to find *.cmake module files
set(CMAKE_MODULE_PATH
    ${CMAKE_MODULE_PATH}
    ${PROJECT_SOURCE_DIR}/cmake
    )

include(arch)

add_subdirectory(src)
```

Then try it out. On Mac, the warning should be gone. On my system I get:

```shell
$ make

-- My system is Linux and my processor is x86_64
...
```

---

## Build the unit tests and link against [Google Test](https://github.com/google/googletest).

Save this to `cmake/tests.cmake`:

```cmake
include(ExternalProject)

ExternalProject_Add(
    gtest
    PREFIX "${PROJECT_BINARY_DIR}/gtest"
    GIT_REPOSITORY https://github.com/google/googletest.git
    GIT_TAG master
    INSTALL_COMMAND true  # currently no install command
    )

include_directories(${PROJECT_BINARY_DIR}/gtest/src/gtest/googletest/include)
include_directories(${PROJECT_SOURCE_DIR}/src)

link_directories(${PROJECT_BINARY_DIR}/gtest/src/gtest-build/googlemock/gtest/)

add_executable(
    unit_tests
    test/main.cpp
    test/fizz_buzz.cpp
    )

target_link_libraries(
    unit_tests
    libgtest.a
    fizz_buzz
    pthread
    )

add_dependencies(unit_tests gtest)

include(CTest)
enable_testing()

add_test(unit ${PROJECT_BINARY_DIR}/bin/unit_tests)
```

```cmake
# ... rest of CMakeLists.txt

include(arch)

add_subdirectory(src)

include(tests)
```

Now try:

```shell
$ make
$ make test
$ ./bin/unit_tests
```

What is the difference between `make test` and `./bin/unit_tests`?

We will enhance this a bit: We indent the code and add an option and an if
check to be able to toggle compilation of unit tests on or off:

```cmake
option(ENABLE_UNIT_TESTS "Enable unit tests" ON)
message(STATUS "Enable testing: ${ENABLE_UNIT_TESTS}")

if(ENABLE_UNIT_TESTS)
    include(ExternalProject)

    ExternalProject_Add(
        gtest
        PREFIX "${PROJECT_BINARY_DIR}/gtest"
        GIT_REPOSITORY https://github.com/google/googletest.git
        GIT_TAG master
        INSTALL_COMMAND true  # currently no install command
        )

    include_directories(${PROJECT_BINARY_DIR}/gtest/src/gtest/googletest/include)
    include_directories(${PROJECT_SOURCE_DIR}/src)

    link_directories(${PROJECT_BINARY_DIR}/gtest/src/gtest-build/googlemock/gtest/)

    add_executable(
        unit_tests
        test/main.cpp
        test/fizz_buzz.cpp
        )

    target_link_libraries(
        unit_tests
        libgtest.a
        fizz_buzz
        pthread
        )

    add_dependencies(unit_tests gtest)

    include(CTest)
    enable_testing()

    add_test(unit ${PROJECT_BINARY_DIR}/bin/unit_tests)
endif()
```

Having the option we could now toggle the unit testing off:

```shell
$ cd build
$ cmake -DENABLE_UNIT_TESTS=OFF ..
$ make
```

---

## Define a version number inside CMake and print it to the output of the executable.

Create a file `cmake/config.h.in`:

```shell
#define VERSION_MAJOR @VERSION_MAJOR@
#define VERSION_MINOR @VERSION_MINOR@
#define VERSION_PATCH @VERSION_PATCH@
```

Create a file `cmake/version.cmake`:

```cmake
set(VERSION_MAJOR 1)
set(VERSION_MINOR 0)
set(VERSION_PATCH 0)

configure_file(
    ${PROJECT_SOURCE_DIR}/cmake/config.h.in
    ${PROJECT_BINARY_DIR}/generated/config.h
    @ONLY
    )
```

Also include it in the main `CMakeLists.txt`.

We also need to add `include_directories(${PROJECT_BINARY_DIR}/generated)` to `src/CMakeLists.txt`.

Include `config.h` in `src/main.cpp` and try to print the code version.

---

## Print the Git hash to the output of the executable.

For this we enhance `cmake/config.h.in`:

```shell
#define VERSION_MAJOR @VERSION_MAJOR@
#define VERSION_MINOR @VERSION_MINOR@
#define VERSION_PATCH @VERSION_PATCH@
#define GIT_HASH "@GIT_HASH@"
```

As well as `cmake/version.cmake`:

```cmake
set(VERSION_MAJOR 1)
set(VERSION_MINOR 0)
set(VERSION_PATCH 0)

set(GIT_HASH "unknown")

find_package(Git)
if(GIT_FOUND)
    execute_process(
        COMMAND ${GIT_EXECUTABLE} --no-pager show -s --pretty=format:%h -n 1
        OUTPUT_VARIABLE GIT_HASH
        OUTPUT_STRIP_TRAILING_WHITESPACE
        ERROR_QUIET
        )
endif()

configure_file(
    ${PROJECT_SOURCE_DIR}/cmake/config.h.in
    ${PROJECT_BINARY_DIR}/generated/config.h
    @ONLY
    )
```

Try to now print the configure-time Git hash in `src/main.cpp`.

---

## Create an installer so the program can be installed properly (GNU standards).

Append the following to `src/CMakeLists.txt`:

```cmake
install(
    TARGETS fb.x
    RUNTIME DESTINATION bin
    )

install(
    TARGETS fizz_buzz
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    )
```

Then try the following:

```shell
$ mkdir build
$ cd build
$ cmake -DCMAKE_INSTALL_PREFIX=/tmp/cmake-example
$ make
$ make test
$ make install
```

---

## Create a DEB or RPM package (if relevant for your distribution).

Include a `cmake/packager.cmake`:

```cmake
set(CPACK_PACKAGE_CONTACT "Slim Shady")

include(CPack)
```

Then run:

```cmake
$ cpack -G DEB
```

or:

```cmake
$ cpack -G RPM
```
