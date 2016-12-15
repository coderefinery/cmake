---
layout: episode
title: "Exercise: Create a CMake framework for an example project"
teaching: 0
exercises: 20
questions:
  - "What is a typical layout for a CMake framework?"
  - "How can we build static and shared libraries?"
  - "How can we get version information into the binary output for reproducibility?"
  - "How does testing work in CMake?"
  - "How can we create an installer and packager?"
---

In this exercise we will CMake-ify a project.
This is interesting for people who use Makefiles
or Autotools.

You can use the exercise time to practice CMake on your own
project(s) but we also provide a mockup project:
https://github.com/juselius/vat-69.git

Your task is to:
 - Create a build system using CMake:
     - Build a shared library
     - Build and link the main program
     - Create an installer so the program can be installed properly (GNU standards)
     - Compile a parallel version with OpenMP

We will also implement a test.


Create a small CMake-built project (C or C++ or Fortran)

Define the project version as a CMake variable.  In this project try to get the
configure-time Git hash and the project version into the output of the code.


---

## Building a Fortran project

- We can list the source files in any order
- We do not have to worry about module dependencies

---

## Multi-language projects

- It is easy to support other languages (C, CXX):
- But how can we select the compiler?

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

---

## Archaeology: Recover previous configuration settings

- Configuration settings are saved in `${PROJECT_BINARY_DIR}/CMakeCache.txt`

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




```shell
$ mkdir build
$ cd build
$ cmake -DCMAKE_INSTALL_PREFIX=/home/user/example ..
$ make
$ make install

Install the project...
-- Install configuration: "Debug"
-- Installing: /home/user/example/cmake-example/bin/main.x
-- Installing: /home/user/example/cmake-example/lib/libmylib_shared.so
-- Installing: /home/user/example/cmake-example/lib/libmylib_static.a
-- Installing: /home/user/example/cmake-example/include/mylib.h
```

---

## CMake

- Cross-platform build systems generator
- CMake is for compiled languages (compilation and linking)
- Not for interpreted languages
- CMake can handle:
    - Many components
    - Complex dependencies
    - Multiple languages
    - Deploy to a variety of platforms (Linux, Mac, Windows, AIX, iOS, Android)

### Credits

- CMake has very responsive mailing list
- Great CMake tutorial given by Eric Noulard at Toulibre 2012

---

## CMake friends

- CMake: cross-platform build systems generator
- CTest: systematic test driver
- CDash: testing dashboard collector
- CPack: package generator

### Typical cycle

- Configuration time (CMake time)
- Build time
- Test time (deploy to CDash)
- Install time
- Packaging time (CPack time)
- Package install time

---

## Overview over CMake commands

- Build specific commands
    - `add_executable`
    - `add_library`
    - `add_definition`
    - `include_directories`
    - `target_link_libraries`

- Powerful installation specification
    - `install`

- Probing commands
    - `try_compile`
    - `try_run`

- Fine control of various properties
    - `set_target_properties`
    - `set_source_files_properties`
    - `set_directory_properties`
    - `set_tests_properties`

---

## Finding help

```shell
$ cmake --help-command-list

add_custom_command       enable_language   get_cmake_property
add_custom_target        enable_testing    get_directory_property
add_definitions          endforeach        get_filename_component
add_dependencies         endfunction       get_property
add_executable           endif             get_source_file_property
add_library              endmacro          get_target_property
add_subdirectory         endwhile          get_test_property
add_test                 execute_process   if
aux_source_directory     export            include
break                    file              include_directories
build_command            find_file         include_external_msproject
cmake_minimum_required   find_library      include_regular_expression
cmake_policy             find_package      install
configure_file           find_path         link_directories
create_test_sourcelist   find_program      list
define_property          fltk_wrap_ui      load_cache
else                     foreach           load_command
elseif                   function          macro
...

$ cmake --help-command add_custom_command
```

---

## Source tree vs. build tree

- CMake supports and encourages out-of-source builds
- Generated files are separate from manually edited files
- Possible to have several builds with the same source
- Possible to nuke entire build just by removing the build directory
- If your `.gitignore` is cluttered with generated files, then this indicates
  that they are in the wrong place
- All generated files belong to the build tree

---

## Source tree vs. build tree variables

- We control the paths of source files and generated files through variables

```cmake
${PROJECT_SOURCE_DIR} # nearest directory where CMakeLists.txt contains
                      # the project() command
${PROJECT_BINARY_DIR} # full path to the top level directory of your build tree

${CMAKE_SOURCE_DIR} # top level source directory

${CMAKE_BINARY_DIR} # top level directory of your build tree

${CMAKE_CURRENT_SOURCE_DIR} # directory where the currently processed
                            # CMakeLists.txt is located in
${CMAKE_CURRENT_BINARY_DIR} # directory where the compiled or generated files
                            # from the current CMakeLists.txt will go to
```

- For simple projects the source tree variables are the same and the build tree variables are the same
- Allows fine-grained control for nested projects
- See also http://www.cmake.org/Wiki/CMake_Useful_Variables

---

template: inverse

# Detecting the environment

---

## Detecting the environment

- We may need to detect few things on the system
    - Operating system
    - Processor
    - MPI
    - Math libraries
    - Other libraries
    - CPU instruction set
    - GPU capabilities
    - Python
    - Git
    - Doxygen
    - ...

- In the following we will learn how to do that

---

## Detecting the system and processor

```cmake
message(STATUS "We are on a ${CMAKE_SYSTEM_NAME} system")

if(${CMAKE_SYSTEM_NAME} STREQUAL "Linux")
    add_definitions(-DSYSTEM_LINUX) # this is our naming ...
endif()
if(${CMAKE_SYSTEM_NAME} STREQUAL "Darwin")
    add_definitions(-DSYSTEM_DARWIN)
endif()
if(${CMAKE_SYSTEM_NAME} STREQUAL "AIX")
    add_definitions(-DSYSTEM_AIX)
endif()
if(${CMAKE_SYSTEM_NAME} MATCHES "Windows")
    add_definitions(-DSYSTEM_WINDOWS)
endif()

message(STATUS "The host processor is ${CMAKE_HOST_SYSTEM_PROCESSOR}")
```

```shell
-- We are on a Linux system
-- The host processor is x86_64
```

- We can use the definitions in the source code to work around system specifics
- In a portable code we should try to minimize these

---

## The Find command family

### High-level module finding mechanism

- `find_package`

### Lower-level finding commands

- `find_program` - find an executable program
- `find_library` - find a library
- `find_file` - find any kind of file
- `find_path` - find a path where some files reside

---

## MPI and CMake

```cmake
option(ENABLE_MPI "Enable MPI" OFF)

if(ENABLE_MPI)
    find_package(MPI) # uses FindMPI.cmake
    if(MPI_FOUND)
        include_directories(${MPI_INCLUDE_PATH})
        add_definitions(-DENABLE_MPI)
    else()
        message(FATAL_ERROR "-- Could not find any MPI installation, check $PATH")
    endif()
endif()
```

- Build process:

```shell
$ mkdir build
$ cd build
$ FC=mpif90 CC=mpicc CXX=mpicxx cmake -DENABLE_MPI=ON ..
$ make [-jN]
```

---

## Find-modules that ship with CMake

```shell
$ ls /usr/share/cmake-2.8/Modules/Find*

FindALSA.cmake       FindFLEX.cmake         FindJasper.cmake          FindOpenThreads.cmake                FindSDL_mixer.cmake     FindosgFX.cmake
FindASPELL.cmake     FindFLTK.cmake         FindJava.cmake            FindPHP4.cmake                       FindSDL_net.cmake       FindosgGA.cmake
FindAVIFile.cmake    FindFLTK2.cmake        FindKDE3.cmake            FindPNG.cmake                        FindSDL_sound.cmake     FindosgIntrospection.cmake
FindArmadillo.cmake  FindFreetype.cmake     FindKDE4.cmake            FindPackageHandleStandardArgs.cmake  FindSDL_ttf.cmake       FindosgManipulator.cmake
FindBISON.cmake      FindGCCXML.cmake       FindLAPACK.cmake          FindPackageMessage.cmake             FindSWIG.cmake          FindosgParticle.cmake
FindBLAS.cmake       FindGDAL.cmake         FindLATEX.cmake           FindPerl.cmake                       FindSelfPackers.cmake   FindosgPresentation.cmake
FindBZip2.cmake      FindGIF.cmake          FindLibArchive.cmake      FindPerlLibs.cmake                   FindSquish.cmake        FindosgProducer.cmake
FindBoost.cmake      FindGLU.cmake          FindLibLZMA.cmake         FindPhysFS.cmake                     FindSubversion.cmake    FindosgQt.cmake
FindBullet.cmake     FindGLUT.cmake         FindLibXml2.cmake         FindPike.cmake                       FindTCL.cmake           FindosgShadow.cmake
FindCABLE.cmake      FindGTK.cmake          FindLibXslt.cmake         FindPkgConfig.cmake                  FindTIFF.cmake          FindosgSim.cmake
FindCUDA.cmake       FindGTK2.cmake         FindLua50.cmake           FindPostgreSQL.cmake                 FindTclStub.cmake       FindosgTerrain.cmake
FindCURL.cmake       FindGTest.cmake        FindLua51.cmake           FindProducer.cmake                   FindTclsh.cmake         FindosgText.cmake
FindCVS.cmake        FindGettext.cmake      FindMFC.cmake             FindProtobuf.cmake                   FindThreads.cmake       FindosgUtil.cmake
FindCoin3D.cmake     FindGit.cmake          FindMPEG.cmake            FindPythonInterp.cmake               FindUnixCommands.cmake  FindosgViewer.cmake
FindCups.cmake       FindGnuTLS.cmake       FindMPEG2.cmake           FindPythonLibs.cmake                 FindVTK.cmake           FindosgVolume.cmake
FindCurses.cmake     FindGnuplot.cmake      FindMPI.cmake             FindQt.cmake                         FindWget.cmake          FindosgWidget.cmake
FindCxxTest.cmake    FindHDF5.cmake         FindMatlab.cmake          FindQt3.cmake                        FindWish.cmake          Findosg_functions.cmake
FindCygwin.cmake     FindHSPELL.cmake       FindMotif.cmake           FindQt4.cmake                        FindX11.cmake           FindwxWidgets.cmake
FindDCMTK.cmake      FindHTMLHelp.cmake     FindOpenAL.cmake          FindQuickTime.cmake                  FindXMLRPC.cmake        FindwxWindows.cmake
FindDart.cmake       FindITK.cmake          FindOpenGL.cmake          FindRTI.cmake                        FindZLIB.cmake
FindDevIL.cmake      FindImageMagick.cmake  FindOpenMP.cmake          FindRuby.cmake                       Findosg.cmake
FindDoxygen.cmake    FindJNI.cmake          FindOpenSSL.cmake         FindSDL.cmake                        FindosgAnimation.cmake
FindEXPAT.cmake      FindJPEG.cmake         FindOpenSceneGraph.cmake  FindSDL_image.cmake                  FindosgDB.cmake
...
```

---

## Detecting math libraries

- `FindBLAS.cmake`
- `FindLAPACK.cmake`
- These modules set the following variables

```shell
BLAS_FOUND          # set to true if a library implementing
                    # the BLAS interface is found
BLAS_LINKER_FLAGS   # uncached list of required linker flags
                    # (excluding -l and -L)
BLAS_LIBRARIES      # uncached list of libraries (using full path name)
                    # to link against to use BLAS
LAPACK_FOUND        # set to true if a library implementing the
                    # LAPACK interface is found
LAPACK_LINKER_FLAGS # uncached list of required linker flags
                    # (excluding -l and -L)
LAPACK_LIBRARIES    # uncached list of libraries
                    # (using full path name) to link against to use LAPACK
```

---

## Detecting the CPU instruction set

- There are many CMake libraries/modules out there that simplify this
- Example: `OptimizeForArchitecture.cmake`

```cmake
include(OptimizeForArchitecture)
OptimizeForArchitecture()
if(AVX_FOUND)
    add_definitions(-DCPU_MEM_ALIGN=32)
    message(STATUS "Setting CPU_MEM_ALIGN=32")
elseif(SSE3_FOUND)
    add_definitions(-DCPU_MEM_ALIGN=16)
    message(STATUS "Setting CPU_MEM_ALIGN=16")
else()
    message(FATAL_ERROR "neither SSE3 nor AVX detected; adapt CMakeLists.txt")
endif()
```

---

## Frequently used CMake recipes

- Controlling compiler flags
- How to modify compiler flags for specific file(s)
- Introducing extra flags for OpenMP or code coverage etc.
- Getting the project version number into the output
- Getting the Git hash into the output
- Source code generation
- Probing compilation
- Examples discussed in this talk can be found in: https://github.com/bast/cmake-example

---

## Getting the Git hash into the output

- First we write a code to detect Git: `FindGit.cmake`

```cmake
find_program(
    GIT_EXECUTABLE
    NAMES git
    DOC "Git command line client"
    )
mark_as_advanced(GIT_EXECUTABLE)

include(FindPackageHandleStandardArgs)
find_package_handle_standard_args(Git DEFAULT_MSG GIT_EXECUTABLE)
```

---

## Getting the Git hash into the output

- Using `FindGit.cmake` we can detect Git

```cmake
find_package(Git)
if(GIT_FOUND)
    execute_process(
        COMMAND ${GIT_EXECUTABLE} rev-list --max-count=1 HEAD
        OUTPUT_VARIABLE GIT_REVISION
        ERROR_QUIET
        )
    if(NOT ${GIT_REVISION} STREQUAL "")
        string(STRIP ${GIT_REVISION} GIT_REVISION)
    endif()
    message(STATUS "Current git revision is ${GIT_REVISION}")
else()
    set(GIT_REVISION "unknown")
endif()
```

---

## Getting the Git hash into the output

- And once we know `${GIT_REVISION}` know how to do the rest

```cpp
// config.h.in

#define VERSION_MAJOR @VERSION_MAJOR@
#define VERSION_MINOR @VERSION_MINOR@
#define VERSION_PATCH @VERSION_PATCH@

const char *GIT_REVISION = "@GIT_REVISION@";
```

```cpp
// source code

#include "config.h"

printf("program version: %i.%i.%i\n", VERSION_MAJOR, VERSION_MINOR, VERSION_PATCH);
printf("git hash: %s\n", GIT_REVISION);
```

```shell
$ ./main.x

program version: 1.0.0
git hash: 70b0e4fbe3e998d9655e153e13fb53d15040368c
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

---

## Packaging with CPack

- CPack makes it possible to conveniently package software
- Archive generators: ZIP, TGZ (all platforms)
- DEB, RPM (Linux)
- Cygwin Source or Binary (Windows/Cygwin)
- NSIS (Windows, Linux)
- DragNDrop, Bundle, OSXX11 (MacOS)

```cmake
# in CMakeLists.txt
include(InstallRequiredSystemLibraries)
set(CPACK_RESOURCE_FILE_LICENSE "${PROJECT_SOURCE_DIR}/LICENSE")
set(CPACK_PACKAGE_VERSION_MAJOR "${VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${VERSION_MINOR}")
set(CPACK_PACKAGE_VERSION_PATCH "${VERSION_PATCH}")
set(CPACK_PACKAGE_CONTACT "Kilgore Trout")
include(CPack)
```
---

## Packaging with CPack

- Creating a package is a one-liner

```shell
$ cpack -G DEB

CPack: Create package using DEB
CPack: Install projects
CPack: - Run preinstall target for: example
CPack: - Install project: example
CPack: Create package
CPack: - package: /home/user/tmp/build/example-1.0.0-Linux.deb generated.
```

- Now you can ship the DEB/RPM/etc. packages and become famous
