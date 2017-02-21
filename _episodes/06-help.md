---
layout: episode
title: "Help"
teaching: 5
exercises: 0
questions:
  - "Where can I find help?"
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

## Useful resources

- [List of CMake variables](https://cmake.org/Wiki/CMake_Useful_Variables)
- [Official CMake documentation](https://cmake.org/documentation/)

Here is a list of commands that we have use in our examples. The quick search
function on the CMake Documentation web page will give you an explanation of
these commands:

```cmake
cmake_minimum_required
project
enable_language
add_executable
add_custom_command
```

---

A useful trick to get an overview of available variables is to make use of
CMakes GUI (Mac & Windows) or CMake's curses Interface (Unix). Change directory
to the build subdirectory and start the CMake curses interface:

```shell
$ cd build
$ ccmake ..
```

You will get a interface like this. Press to (t) for the toggling on the advanced mode:
```cmake
                                                     Page 1 of 3
 CMAKE_AR                         /Applications/Xcode.app/Contents/Developer/Toolch
 CMAKE_BACKWARDS_COMPATIBILITY    2.4
 CMAKE_BUILD_TYPE
 CMAKE_COLOR_MAKEFILE             ON
 CMAKE_CXX_COMPILER               /Applications/Xcode.app/Contents/Developer/Toolch
 CMAKE_CXX_FLAGS
 CMAKE_CXX_FLAGS_DEBUG            -g
 CMAKE_CXX_FLAGS_MINSIZEREL       -Os -DNDEBUG
 CMAKE_CXX_FLAGS_RELEASE          -O3 -DNDEBUG
 CMAKE_CXX_FLAGS_RELWITHDEBINFO   -O2 -g -DNDEBUG
 CMAKE_C_COMPILER                 /Applications/Xcode.app/Contents/Developer/Toolch
 CMAKE_C_FLAGS
 CMAKE_C_FLAGS_DEBUG              -g
 CMAKE_C_FLAGS_MINSIZEREL         -Os -DNDEBUG
 CMAKE_C_FLAGS_RELEASE            -O3 -DNDEBUG
 CMAKE_C_FLAGS_RELWITHDEBINFO     -O2 -g -DNDEBUG
 CMAKE_EXE_LINKER_FLAGS
 CMAKE_EXE_LINKER_FLAGS_DEBUG

CMAKE_AR: Path to a program.
Press [enter] to edit option Press [d] to delete an entry        CMake Version 3.7.1
Press [c] to configure
Press [h] for help           Press [q] to quit without generating
Press [t] to toggle advanced mode (Currently On)
```

If you need to build a package which uses CMake as the configuration tool, this
is a way to get a quick overview of what is needed for building the package.
Here is an example for Armadillo 7.5.002:
```cmake
                                                    Page 1 of 3
 ARPACK_LIBRARY                  *ARPACK_LIBRARY-NOTFOUND
 BUILD_SHARED_LIBS               *ON
 CMAKE_AR                        */Applications/Xcode.app/Contents/Developer/Toolch
 CMAKE_BUILD_TYPE                *
 CMAKE_COLOR_MAKEFILE            *ON
 CMAKE_CXX_COMPILER              */Applications/Xcode.app/Contents/Developer/Toolch
 CMAKE_CXX_FLAGS                 *
 CMAKE_CXX_FLAGS_DEBUG           *-g
 CMAKE_CXX_FLAGS_MINSIZEREL      *-Os -DNDEBUG
 CMAKE_CXX_FLAGS_RELEASE         *-O3 -DNDEBUG
 CMAKE_CXX_FLAGS_RELWITHDEBINFO  *-O2 -g -DNDEBUG
 CMAKE_EXE_LINKER_FLAGS          *
 CMAKE_EXE_LINKER_FLAGS_DEBUG    *
 CMAKE_EXE_LINKER_FLAGS_MINSIZE  *
 CMAKE_EXE_LINKER_FLAGS_RELEASE  *
 CMAKE_EXE_LINKER_FLAGS_RELWITH  *
 CMAKE_EXPORT_COMPILE_COMMANDS   *OFF
 CMAKE_INSTALL_NAME_TOOL         */opt/local/bin/install_name_tool

ARPACK_LIBRARY: Path to a library.
Press [enter] to edit option Press [d] to delete an entry        CMake Version 3.7.1
Press [c] to configure
Press [h] for help           Press [q] to quit without generating
```
