---
layout: episode
title: "Motivation"
teaching: 10
exercises: 0
questions:
  - "I am already using Makefiles, do I need CMake?"
  - "Why CMake and not Autotools?"
objectives:
  - "Building software involves many layers of tools. The objective is to be able to place CMake in this stack."
keypoints:
  - "CMake is not a replacement for Makefiles."
  - "CMake can generate Makefiles."
  - "CMake is an alternative to Autotools."
---

## Why are many projects using Makefiles?

- Why not just writing a script that will compile and link everything?
- Discuss with the group

---

## Many projects use Makefiles

Makefiles express targets and their dependencies and are a popular way to compile software.

Motivation for using Makefiles:
- Express dependencies and let Make rebuild those targets and dependencies which are out of date

Advantages:
- Simple and many people are comfortable with it
- Portable across Unix, Linux, and Mac OS X
- Old and very well tested

Disadvantages:
- Relatively low-level
- Portability is a problem (impossible to port to Windows)
- Typically needs to be configured (you either have to offer a configure script or offer several Makefiles)
- Difficult to handle multi-language projects
- Difficult to manage projects that depend on many libraries
- Offers no functions to discover OS, processor, libraries, etc.
- Fortran 90 dependencies need to be explicitly expressed
- Difficult to do complex tasks and remain portable
- Typically we need to accommodate multiple languages, complex dependencies, math libraries, MPI, OpenMP, conditional
  builds and testing, external libraries, etc.
- Typically projects grow out of Makefiles and use GNU Autotools to generate Makefiles

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
- CTest uses a Makefile (possible to run tests with -jN)
- Personal opinion: easier than Autotools

Popular:
- We are in a good company: CMake is used by many prominent projects:
  MySQL, Boost, VTK, Blender, KDE, LyX, Mendeley, MikTeX, Compiz, Google Test, ParaView, Second Life, Avogadro, and many more ...

General:
- Not bound to the generation of Makefiles
