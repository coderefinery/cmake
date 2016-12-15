---
layout: episode
title: "Variables, lists, loops, and branching"
teaching: 10
exercises: 0
questions:
  - "Is CMake a full-fledged programming language?"
objectives:
  - "Learn how to set and print variables."
  - "Learn how to avoid code repetition."
keypoints:
  - "CMake allows you to structure your code as you are used from other languages."
---

## Messages to the user (or to you)

Useful for debugging CMake files:

```cmake
# display message
message("We are right here.")

# display STATUS message with a -- in the command line
message(STATUS "Still everything under control ...")

# display message and halt configuration
message(FATAL_ERROR "Something unexpected happened!")
```

---

## Variables, lists, and loops

```cmake
set(CURRENT_WEATHER "sunny day")

message("We'll meet again some ${CURRENT_WEATHER} ...")

set(FRUITS apple banana orange kiwi mango)

foreach(fruit ${FRUITS})
    message("${fruit} is a tasty fruit")
endforeach()
```

You can append to sets like this:

```cmake
set(FRUITS apple banana orange kiwi mango)

# we add grapes to the list of fruits
set(FRUITS ${FRUITS} grapes)
```

---

## Branching

Note the unusual parentheses in `else()` and `endif()`:

```cmake
if(CURRENT_WEATHER STREQUAL "sunny day")
    message("Today is a ${CURRENT_WEATHER}. Let us go for a walk.")
else()
    message("Better stay inside the house.")
endif()
```

---

## Functions and macros

```cmake
function(my_function foo bar)
    message("called my_function with the arguments: '${foo}' and '${bar}'")
    set(MY_VARIABLE "${bar}")
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

Function variables have function scope:

```shell
called my_function with the arguments: 'this' and 'that'
MY_VARIABLE set to: ''
called my_macro with the arguments: 'this' and 'that'
MY_VARIABLE set to: 'that'
```
