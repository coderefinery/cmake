---
layout: episode
title: "Motivation"
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

## Many projects use Makefiles

- Simple and many people are comfortable with it
- Relatively low-level
- Portability is a problem (impossible to port to Windows)
- Typically needs to be configured (configure scripts)
- Difficult to handle multi-language projects
- Difficult to manage projects that depend on many libraries
- Offers no functions to discover OS, processor, libraries, etc.
- Fortran 90 dependencies need to be explicitly expressed
- Difficult to do complex tasks and remain portable
- Typically we need to accommodate multiple languages, complex dependencies, math libraries, MPI, OpenMP, conditional
  builds and testing, external libraries, etc.
- Typically projects grow out of Makefiles and use GNU Autotools to generate Makefiles

---

## About CMake

- Cross-platform
- Open-source
- Actively developed
- Manages the build process in a compiler-independent manner
- Designed to be used in conjunction with the native build environment
- Written in C++ (does not matter but if you want to build CMake yourself, C++ is all you need)
- CMake is a generator, so it does not compile
- Not a replacement for make, rather replacement for Autotools

---

## Why CMake (1/2)

- Out-of-source compilation (possibility to compile several builds with the same source)
- Really cross-platform (Linux, Mac, Windows, AIX, iOS, Android)
- Excellent support for Fortran, C, C++, and Java, as well as mixed-language projects
- CMake understands Fortran 90 dependencies very well; no need to program a dependency scanner
- Excellent support for multi-component and multi-library projects
- Makes it possible and relatively easy to download, configure, build, install, and link external modules
- CMake defines portable variables about the system for us
- Cross-platform system- and library-discovery
- CTest uses a Makefile (possible to run tests with -jN)

---

## Why CMake (2/2)

- Generates user interface (command-line or text-UI or GUI)
- Can achieve complex build tasks with less typing and less headaches in a portable way
- We are in a good company: CMake is used by many prominent projects:
  MySQL, Boost, VTK, Blender, KDE, LyX, Mendeley, MikTeX, Compiz, Google Test, ParaView, Second Life, Avogadro, and many more ...
- Not bound to the generation of Makefiles
- Full-fledged testing and packaging framework with CTest and CPack
- We spend less time writing build system files and more time developing code
- Personal opinion: easier than Autotools
