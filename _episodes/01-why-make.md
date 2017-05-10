---
layout: episode
title: "Why Make?"
teaching: 5
exercises: 0
questions:
  - Why do many projects use Makefiles?
  - What are typical problems with Makefiles?
---

## Makefiles are over 40 years old!

- And they are probably here to stay. Why?
- Why do we divide projects into many files?
- Why not just write a script that compiles and links all files?

---

## Many projects use Makefiles

Makefiles express targets and their dependencies and are a popular way to compile software.


### Motivation

- Express dependencies and let Make rebuild those targets and dependencies which are out of date.


### Advantages

- Simple and many people are comfortable with it.
- Portable across Unix, Linux, and Mac OS X.
- Has been around for a very long time and is very well tested.
- General: not only for compiling files! We can define what targets mean.


### Disadvantages

- Relatively low-level.
- Portability is a problem (Windows; not sure about Windows 10).
- Typically needs to be configured (you either have to offer a configure script or offer several Makefiles).
- Offers no functions to discover OS, processor, libraries, etc.
- Fortran 90 dependencies need to be explicitly expressed.
- Difficult to manage projects that depend on many libraries.
- Difficult to do complex tasks and remain portable.
- Typically we need to accommodate multiple languages, complex dependencies, math libraries, MPI, OpenMP, conditional
  builds and testing, external libraries, etc.
- Typically projects grow out of Makefiles and use GNU Autotools to generate Makefiles.

---

## Applications

- What applications of Makefiles can you imagine?
