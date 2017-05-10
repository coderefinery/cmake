---
layout: episode
title: "Motivation for CMake"
teaching: 5
exercises: 0
questions:
  - "I am already using Makefiles, do I need CMake?"
  - "Why CMake and not Autotools?"
  - "I am a Python developer, why should I care?"
objectives:
  - "Building software involves many layers of tools. The objective is to be able to place CMake in this stack."
keypoints:
  - "CMake is not a replacement for Makefiles."
  - "CMake can generate Makefiles."
  - "CMake is an alternative to Autotools."
---

## About [CMake](https://cmake.org)

- Cross-platform
- Open-source
- [Actively developed](https://github.com/Kitware/CMake)
- Manages the build process in a compiler-independent manner
- Designed to be used in conjunction with the native build environment
- Written in C++ (does not matter but if you want to build CMake yourself, C++ is all you need)
- CMake is a generator - this means it does not compile the code
- Not a replacement for Makefiles, rather a replacement for Autotools

---

## Why CMake?

Separation of source and build path:

- Out-of-source compilation (possibility to compile several builds with the same source)

Portability:

- Really cross-platform (Linux, Mac, Windows, AIX, iOS, Android)
- CMake defines portable variables about the system
- Cross-platform system- and library-discovery

Language support:

- Excellent support for Fortran, C, C++, and Java, as well as mixed-language projects
- CMake understands Fortran 90 dependencies very well; no need to program a dependency scanner
- Excellent support for multi-component and multi-library projects

Supports modular code development:

- Makes it possible and relatively easy to download, configure, build, install, and link external modules

Provides tools:

- Generates user interface (command-line or text-UI or GUI)
- Full-fledged testing and packaging framework with CTest and CPack
- CTest uses a Makefile (possible to run sequential tests concurrently)

Popular:

- CMake is used by many prominent projects:
  MySQL, Boost, VTK, Blender, KDE, LyX, Mendeley, MikTeX, Compiz, Google Test, ParaView, Second Life, Avogadro, and many more ...

General:

- Not bound to the generation of Makefiles

---

## Note to Python developers

One day you might need to implement low-level operations to your Python library
in Fortran or C or C++ and then CMake can come in handy.


## CMake Motivation

- When you move your application or code base to another platform, you will need to modify your Makefiles or write a configuration script.
- If your software is used on several platforms, you easily end up doing almost identical modifications in several places.
- On Microsoft Windows you may have to use a separate build systems.
- Your changes in the Makefiles do rarely apply to Microsoft Windows.

*(following points taken from the book "Mastering CMake", K. Martin, B. Hoffman, Kitware)*

* The ability to search for programs, libraries and header files required by the software being built
* The ability to build in a directory tree outside the source code directory tree
* The ability to build complex custom commands for automatically generated files such as  SWIG wrapper generators
* The ability to select optional components at configuration time
* The ability to automatically generate workspaces and projects from a simple text file
* The ability to switch between static and shared builds
* Automatic generation of file dependencies and support for parallel builds on most platforms
* The ability to test for byte-order and other hardware-specific characteristics
* A single set of build configuration files that work on all platforms
* Ability to build shared libraries on all platforms that support it
* Ability to configure files with system-dependent information, such as the location of data files and other information
