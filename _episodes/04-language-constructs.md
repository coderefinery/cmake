---
layout: episode
title: "Language constructs"
teaching: 10
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

## Presenting options to the user

```cmake
# default is OFF
option(ENABLE_MPI "Enable MPI parallelization" OFF)

if(ENABLE_MPI)
    message("MPI is enabled")
else()
    message("MPI is disabled")
endif()
```

- We can select options on the command line:

```shell
$ cd build
$ cmake -DENABLE_MPI=ON ..
$ make
```

- These options become automatically available in GUI/ccmake

---

## Good way to set defaults

```cmake
set(MAX_BUFFER "1024" CACHE STRING "Max buffer size")

# this is passed on to the code we compile
add_definitions(-DMAX_BUFFER=${MAX_BUFFER})

message(STATUS "Set max buffer to ${MAX_BUFFER}")
```

- We can override them from the command line

```shell
$ cmake ..
-- Set max buffer to 1024

$ cmake -DMAX_BUFFER=2048 ..
-- Set max buffer to 2048
```

---

## Messages to the user (or to you)

- Useful for debugging CMake files

```cmake
# display message
message("We are right here.")

# display STATUS message with a -- in the command line
message(STATUS "Still everything under control ...")

# display message and halt configuration
message(FATAL_ERROR "Something unexpected happened!")
```

---

## Variables, lists and loops

```cmake
set(CURRENT_WEATHER "sunny day")

message("We'll meet again some ${CURRENT_WEATHER} ...")

set(FRUITS Apple Banana Orange Kiwi Mango)

foreach(fruit ${FRUITS})
    message("${fruit} is a tasty fruit")
endforeach()
```

---

## Functions and macros

```cmake
function(my_function foo bar)
    message("called my_function with the arguments: '${foo}' and '${bar}'")
    set(MY_VARIABLE "local scope")
endfunction()

macro(my_macro foo bar)
    message("called my_macro with the arguments: '${foo}' and '${bar}'")
    set(MY_VARIABLE "${bar}")
endmacro()

my_function(this that)
message("MY_VARIABLE set to: '${MY_VARIABLE}'")

my_macro(this that)
message("MY_VARIABLE set to: '${MY_VARIABLE}'")
```

- Function variables have scope

```shell
called my_function with the arguments: 'this' and 'that'
MY_VARIABLE set to: ''
called my_macro with the arguments: 'this' and 'that'
MY_VARIABLE set to: 'that'
```

---

## Configuring files

- We can configure files

```cmake
configure_file(
    ${PROJECT_SOURCE_DIR}/infile
    ${PROJECT_BINARY_DIR}/outfile
    @ONLY
    )
```

- CMake takes `${PROJECT_SOURCE_DIR}/infile`:

```shell
My system is @CMAKE_SYSTEM_NAME@ and my
processor is @CMAKE_HOST_SYSTEM_PROCESSOR@.
```

- And generates `${PROJECT_BINARY_DIR}/outfile`:

```shell
My system is Linux and my
processor is x86_64.
```
