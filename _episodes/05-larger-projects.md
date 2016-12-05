---
layout: episode
title: "Organizing larger projects"
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

## Organizing larger projects: includes

- CMake looks for `CMakeLists.txt`
- For larger projects it is not practical to put everything into one huge `CMakeLists.txt`
- We can organize the CMake code like this:

```
CMakeList.txt
cmake/ConfigureCompilerFlags.cmake
cmake/ConfigureMPI.cmake
cmake/ConfigureMath.cmake
```

- Then we can include these files into `CMakeLists.txt` (or other included files):

```cmake
# these are paths that cmake will search for module files
set(CMAKE_MODULE_PATH
    ${CMAKE_MODULE_PATH}
    ${PROJECT_SOURCE_DIR}/cmake)

include(ConfigureCompilerFlags)
include(ConfigureMPI)
include(ConfigureMath)
```

---

## Organizing larger projects: split CMakeLists.txt

- Let us assume we have a source tree structure like this:

```
CMakeLists.txt
main.F90
fun1/foo.F90
fun1/bar.F90
fun2/baz.F90
fun2/oof.F90
```

- Assume module fun2 depends on fun1 (module baz uses module foo)

```fortran
module baz
   use foo, only: print_foo
   implicit none
   public print_baz
   private
contains
   subroutine print_baz()
      print *, 'baz'
      call print_foo()
   end subroutine
end module
```

---

## Organizing larger projects: split CMakeLists.txt

- The convenient and compact solution is to do this

```cmake
cmake_minimum_required(VERSION 2.8 FATAL_ERROR)

project(MyProject)

enable_language(Fortran)

# this is where we will place the Fortran module files
set(CMAKE_Fortran_MODULE_DIRECTORY ${PROJECT_BINARY_DIR}/modules)

add_executable(
    myproject.x
    main.F90
    fun1/foo.F90
    fun1/bar.F90
    fun2/baz.F90
    fun2/oof.F90
    )
```

- We do not have to worry (we can compile with make -j12)

---

## Organizing larger projects: split CMakeLists.txt

- Alternative is to use separate `CMakeLists.txt` files

```cmake
cmake_minimum_required(VERSION 2.8 FATAL_ERROR)

project(MyProject)

enable_language(Fortran)

# this is where we will place the Fortran module files
set(CMAKE_Fortran_MODULE_DIRECTORY ${PROJECT_BINARY_DIR}/modules)

add_subdirectory(fun1) # here we use fun1/CMakeLists.txt
add_subdirectory(fun2) # here we use fun2/CMakeLists.txt

# fun1 needs to be compiled before fun2
add_dependencies(fun2 fun1)

add_executable(myproject.x main.F90)

target_link_libraries(myproject.x fun1 fun2)
```

---

## Organizing larger projects: split CMakeLists.txt

- Where `fun1/CMakeLists.txt` contains

```cmake
add_library(
    fun1
    foo.F90
    bar.F90
    )
```

- And where `fun2/CMakeLists.txt` contains

```cmake
add_library(
    fun2
    baz.F90
    oof.F90
    )
```

---

## Organizing larger projects: split CMakeLists.txt

- The advantage is that we separate concerns

```shell
Scanning dependencies of target fun1
[ 20%] Building Fortran object fun1/CMakeFiles/fun1.dir/bar.F90.o
[ 40%] Building Fortran object fun1/CMakeFiles/fun1.dir/foo.F90.o
Linking Fortran static library libfun1.a
[ 40%] Built target fun1
Scanning dependencies of target fun2
[ 60%] Building Fortran object fun2/CMakeFiles/fun2.dir/baz.F90.o
[ 80%] Building Fortran object fun2/CMakeFiles/fun2.dir/oof.F90.o
Linking Fortran static library libfun2.a
[ 80%] Built target fun2
Scanning dependencies of target myproject.x
[100%] Building Fortran object CMakeFiles/myproject.x.dir/main.F90.o
Linking Fortran executable myproject.x
[100%] Built target myproject.x
```

- Useful for maintenance of larger projects
- Simplifies and speeds up (re)compilation because we limit possible dependencies
