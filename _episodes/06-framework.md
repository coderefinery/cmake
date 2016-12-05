---
layout: episode
title: "Exercise: Create a CMake framework for an example project"
teaching: 0
exercises: 20
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
