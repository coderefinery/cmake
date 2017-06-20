---
layout: episode
title: "Exercise: CMake-ify a Tetris game"
teaching: 0
exercises: 10
questions:
  - How to configure and compile a code that you found on the net.
keypoints:
  - Have fun!
---

## Exercise: Create a CMake framework for a Tetris game

- Work in a team (or on your own if you prefer).
- Clone [this project](https://github.com/Gregwar/ASCII-Tetris).
- Take `CMakeLists.txt` from [/04-hello-world-cmake/](Hello-world example using CMake) as a starting point.
- Try to configure and compile the game with CMake.
- Play Tetris and try to get a good score.


### Hints

The language is now C instead of CXX.

You will need this in your `CMakeLists.txt`:

```cmake
include_directories(${CMAKE_SOURCE_DIR})
```
