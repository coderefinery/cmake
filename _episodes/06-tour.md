---
layout: episode
title: "A tour of more advanced concepts"
teaching: 5
exercises: 0
questions:
  - "What else is possible?"
objectives:
  - "Provide a resource for more advanced concepts."
---

## Detecting CUDA

```cmake
# uses FindCUDA.cmake
find_package(CUDA)

if(CUDA_FOUND)
    set(CUDA_NVCC_FLAGS ${CUDA_NVCC_FLAGS};-gencode arch=compute_20,code=sm_20)

    cuda_compile(
        cuda_objects
        cuda_interface.cu
        contract.cu
        sssp.cu
        sssd.cu
        spsp.cu
        sspp.cu
        sppp.cu
        spsd.cu
        sdsp.cu
        sdsd.cu
        sdpp.cu
        pppp.cu
        add.cu
        )

    cuda_add_library(cuda_interface ${cuda_objects})
endif()
```

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
