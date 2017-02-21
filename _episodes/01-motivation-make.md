---
layout: episode
title: "Motivation for Make"
teaching: 5
exercises: 0
questions:
  - "I only do compilation on small private projects, do I need Make?"
  - "Make seems so complex. It is not exactly easy to read Makefiles?"
objectives:
  - "Develop a simple Makefile, which can be used in small projects."
  - "See what creates the 'magic' in Make."
keypoints:
  - "Make structure the build process"
  - "Make -p can be very helpful"

---

## Why are many projects using Makefiles?

Why not just writing a script that will compile and link everything?
Discuss with the group.

---

## Many projects use Makefiles

Makefiles express targets and their dependencies and are a popular way to compile software.

Motivation for using Makefiles:

- Express dependencies and let Make rebuild those targets and dependencies which are out of date

Advantages:

- Simple and many people are comfortable with it
- Portable across Unix, Linux, and Mac OS X
- Has been around for a very long time and is very well tested

Disadvantages:

- Relatively low-level
- Portability is a problem (Windows; not sure about Windows 10)
- Typically needs to be configured (you either have to offer a configure script or offer several Makefiles)
- Difficult to manage projects that depend on many libraries
- Offers no functions to discover OS, processor, libraries, etc.
- Fortran 90 dependencies need to be explicitly expressed
- Difficult to do complex tasks and remain portable
- Typically we need to accommodate multiple languages, complex dependencies, math libraries, MPI, OpenMP, conditional
  builds and testing, external libraries, etc.
- Typically projects grow out of Makefiles and use GNU Autotools to generate Makefiles

---
