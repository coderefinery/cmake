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

## Useful links

- [List of CMake variables](https://cmake.org/Wiki/CMake_Useful_Variables)
