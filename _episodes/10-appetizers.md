---
layout: episode
title: "Appetizers for more advanced concepts"
teaching: 5
exercises: 0
questions:
  - "What else is possible?"
objectives:
  - "Provide a resource for more advanced concepts."
---

## Probing compilation

```cmake
# provides check_fortran_source_compiles
include(CheckFortranSourceCompiles)

# read source file into variable _source
file(READ "${CMAKE_SOURCE_DIR}/cmake/probe/probe-advanced-feature.F90" _source)

# check whether file compiles
check_fortran_source_compiles(
    ${_source}
    COMPILER_SUPPORTS_ADVANCED_FEATURE
    )

# based on result decide what to do
if(COMPILER_SUPPORTS_ADVANCED_FEATURE)
    message(STATUS "Compiler supports advanced feature")
    add_definitions(-DUSE_ADVANCED_FEATURE)
else()
    message(STATUS "WARNING: Compiler does not support advanced feature")
endif()
```

---

## Source code generation

- Example: we want to use Python to autogenerate C++ code

```cmake
# find python
find_package(PythonInterp)
if(NOT PYTHONINTERP_FOUND)
    message(FATAL_ERROR "ERROR: Python interpreter not found.")
endif()

# generate the file
add_custom_target(
    generate_file
    COMMAND ${PYTHON_EXECUTABLE} ${PROJECT_SOURCE_DIR}/src/generate.py >
                                     ${PROJECT_BINARY_DIR}/mylib_generated.cpp
    )

# mark the file as generated
set_source_files_properties(
    ${PROJECT_BINARY_DIR}/mylib_generated.cpp
    PROPERTIES GENERATED 1)

# build library
add_library(
    mylib_static
    STATIC
    src/mylib.cpp
    ${PROJECT_BINARY_DIR}/mylib_generated.cpp
    )

add_dependencies(mylib_static generate_file)
```
