---
layout: episode
title: "Rest"
teaching: 20
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

## Archaeology: Recover previous configuration settings

- Configuration settings are saved in `${PROJECT_BINARY_DIR}/CMakeCache.txt`

---

## CMake generators

- The list of CMake generators is long
- Unix Makefiles is only one of them:

```
visual studio 6                   Xcode
visual studio 7                   CodeBlocks - MinGW Makefiles
visual studio 10                  CodeBlocks - NMake Makefiles
visual studio 11                  CodeBlocks - Ninja
visual studio 12                  CodeBlocks - Unix Makefiles
visual studio 7 .net 2003         Eclipse CDT4 - MinGW Makefiles
visual studio 8 2005              Eclipse CDT4 - NMake Makefiles
visual studio 9 2008              Eclipse CDT4 - Ninja
borland makefiles                 Eclipse CDT4 - Unix Makefiles
nmake makefiles                   KDevelop3
nmake makefiles jom               KDevelop3 - Unix Makefiles
watcom wmake                      Sublime Text 2 - MinGW Makefiles
msys makefiles                    Sublime Text 2 - NMake Makefiles
mingw makefiles                   Sublime Text 2 - Ninja
unix makefiles                    Sublime Text 2 - Unix Makefiles
Ninja
```

- CMake makes it possible to use the native build environment
