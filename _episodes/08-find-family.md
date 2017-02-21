---
layout: episode
title: "Find command family"
teaching: 5
exercises: 0
questions:
  - "How can I find programs, libraries, files, and paths with CMake?"
---

## CMake find functions

As you develop code and your code base grow, you will most certainly depend
upon external packages or libraries. When you share your code, which you will
because you do research which is repeatable && verifiable (in addition there is
other coming after you who will depend upon your work), you can not assume
where these packages or libraries are installed.

CMake provides several find functions, which you can use to search for files or libraries:
```cmake
find_file()
find_library()
find_path()
find_program()
find_package()
```
---
## Example with find_library() and find_path()
```cmake
# find libtiff, looking in some standard places
cmake_minimum_required(VERSION 2.8)

find_library (TIFF_LIBRARY
             NAMES tiff tiff2
	     PATHS /opt/local/lib /opt/local/Library usr/local/lib /usr/lib
	     )

# find tiff.h looking in some standard places
find_path(TIFF_INCLUDES tiff.h
  /opt/local/include
  /usr/local/include
  /usr/include
  )

#Typically you would like to use the library or header file as part of the
# linking or compilation, as shown in the commented region below
#include_directories(${TIFF_INCLUDE}
#add_executable(mytiff mytiff.c)
#target_link_libraries(myprogram ${TIFF_LIBRARY})

if (TIFF_LIBRARY_NOTFOUND)
  message("Tiff library not found")
else()
  message("Tiff library: ${TIFF_LIBRARY}")
endif()

if (TIFF_INCLUDES_NOTFOUND)
  message("Tiff header file not found")
else()
  message("Tiff include PATH: ${TIFF_INCLUDES}")
endif()
```
If you have tiff available in any of the paths searche your output will be like this:
```shell
$ cmake ..
Tiff library: /opt/local/lib/libtiff.dylib
Tiff include PATH: /opt/local/include
-- Configuring done
-- Generating done
-- Build files have been written to: /Users/bjornlin/src/cmake/find_library/build

```

## Example with find package()
Here is an example where we search for the package Armadillo. If you do not have Armadillo installed, you will get "Armadillo not found!":
```cmake
cmake_minimum_required(VERSION 2.8)

find_package(Armadillo REQUIRED)
if (ARMADILLO_FOUND)
	message("Armadillo version: ${ARMADILLO_VERSION_MAJOR} ${ARMADILLO_VERSION_MINOR} ${ARMADILLO_VERSION_PATCH}")
else()
	message("Armadillo not found!!")
endif()


option(ENABLE_MPI "Enable MPI" OFF)

if (ENABLE_MPI)
   find_package(MPI)
   if (MPI_FOUND)
      include_directories(${MPI_INCLUDE_PATH})
      add_definitions(-DENABLE_MPI)
   else()
	message(FATAL_ERROR "Could not find any MPI installation, check $PATH")  # CMake Error, stop processing and generation
   endif()
endif()
```

My output, since I have Armadillo installed on my laptop:
```shell
ls
CMakeLists.txt
$ mkdir build && cd build
$ cmake ..
-- The C compiler identification is AppleClang 7.3.0.7030031
-- The CXX compiler identification is AppleClang 7.3.0.7030031
-- Check for working C compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/cc
-- Check for working C compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++
-- Check for working CXX compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Found Armadillo: /opt/local/lib/libarmadillo.dylib (found version "7.600.2")
Armadillo version: 7 600 2
-- Configuring done
-- Generating done
-- Build files have been written to: /Users/bjornlin/src/cmake/find_packages/build
```

When enable the MPI_ENABLE variable, I get a error message since I do not have MPI-library installed:
```shell
$ cmake -DENABLE_MPI=ON ..
Armadillo version: 7 600 2
-- Could NOT find MPI_C (missing:  MPI_C_LIBRARIES MPI_C_INCLUDE_PATH)
-- Could NOT find MPI_CXX (missing:  MPI_CXX_LIBRARIES MPI_CXX_INCLUDE_PATH)
CMake Error at CMakeLists.txt:19 (message):
  Could not find any MPI installation, check $PATH


-- Configuring incomplete, errors occurred!
See also "/Users/bjornlin/src/cmake/find_packages/build/CMakeFiles/CMakeOutput.log".
$

```

Configuration on a system with MPI installed:
```shell
$ mkdir build
$ cd build
$ FC=mpif90 CC=mpicc CXX=mpicxx cmake -DENABLE_MPI=ON ..
$ make [-jN]
```

---

## Find-modules that ship with CMake

```shell
$ cd /usr/share/cmake-3.6/Modules/
$ ls Find*

FindALSA.cmake	     FindGTest.cmake	    FindOpenGL.cmake			 FindTclStub.cmake
FindASPELL.cmake     FindGettext.cmake	    FindOpenMP.cmake			 FindTclsh.cmake
FindAVIFile.cmake    FindGit.cmake	    FindOpenSSL.cmake			 FindThreads.cmake
FindArmadillo.cmake  FindGnuTLS.cmake	    FindOpenSceneGraph.cmake		 FindUnixCommands.cmake
FindBISON.cmake      FindGnuplot.cmake	    FindOpenThreads.cmake		 FindWget.cmake
FindBLAS.cmake	     FindHDF5.cmake	    FindPHP4.cmake			 FindWish.cmake
FindBZip2.cmake      FindHSPELL.cmake	    FindPNG.cmake			 FindX11.cmake
FindBacktrace.cmake  FindHTMLHelp.cmake     FindPackageHandleStandardArgs.cmake  FindXCTest.cmake
FindBoost.cmake      FindHg.cmake	    FindPackageMessage.cmake		 FindXMLRPC.cmake
FindBullet.cmake     FindIce.cmake	    FindPerl.cmake			 FindXalanC.cmake
FindCABLE.cmake      FindIcotool.cmake	    FindPerlLibs.cmake			 FindXercesC.cmake
FindCUDA.cmake	     FindImageMagick.cmake  FindPhysFS.cmake			 FindZLIB.cmake
FindCURL.cmake	     FindIntl.cmake	    FindPike.cmake			 Findosg.cmake
FindCVS.cmake	     FindJNI.cmake	    FindPkgConfig.cmake			 FindosgAnimation.cmake
FindCoin3D.cmake     FindJPEG.cmake	    FindPostgreSQL.cmake		 FindosgDB.cmake
FindCups.cmake	     FindJasper.cmake	    FindProducer.cmake			 FindosgFX.cmake
FindCurses.cmake     FindJava.cmake	    FindProtobuf.cmake			 FindosgGA.cmake
FindCxxTest.cmake    FindKDE3.cmake	    FindPythonInterp.cmake		 FindosgIntrospection.cmake
FindCygwin.cmake     FindKDE4.cmake	    FindPythonLibs.cmake		 FindosgManipulator.cmake
FindDCMTK.cmake      FindLAPACK.cmake	    FindQt.cmake			 FindosgParticle.cmake
FindDart.cmake	     FindLATEX.cmake	    FindQt3.cmake			 FindosgPresentation.cmake
FindDevIL.cmake      FindLTTngUST.cmake     FindQt4.cmake			 FindosgProducer.cmake
FindDoxygen.cmake    FindLibArchive.cmake   FindQuickTime.cmake			 FindosgQt.cmake
FindEXPAT.cmake      FindLibLZMA.cmake	    FindRTI.cmake			 FindosgShadow.cmake
FindFLEX.cmake	     FindLibXml2.cmake	    FindRuby.cmake			 FindosgSim.cmake
FindFLTK.cmake	     FindLibXslt.cmake	    FindSDL.cmake			 FindosgTerrain.cmake
FindFLTK2.cmake      FindLua.cmake	    FindSDL_image.cmake			 FindosgText.cmake
FindFreetype.cmake   FindLua50.cmake	    FindSDL_mixer.cmake			 FindosgUtil.cmake
FindGCCXML.cmake     FindLua51.cmake	    FindSDL_net.cmake			 FindosgViewer.cmake
FindGDAL.cmake	     FindMFC.cmake	    FindSDL_sound.cmake			 FindosgVolume.cmake
FindGIF.cmake	     FindMPEG.cmake	    FindSDL_ttf.cmake			 FindosgWidget.cmake
FindGLEW.cmake	     FindMPEG2.cmake	    FindSWIG.cmake			 Findosg_functions.cmake
FindGLU.cmake	     FindMPI.cmake	    FindSelfPackers.cmake		 FindwxWidgets.cmake
FindGLUT.cmake	     FindMatlab.cmake	    FindSquish.cmake			 FindwxWindows.cmake
FindGSL.cmake	     FindMotif.cmake	    FindSubversion.cmake
FindGTK.cmake	     FindOpenAL.cmake	    FindTCL.cmake
FindGTK2.cmake	     FindOpenCL.cmake	    FindTIFF.cmake
```
---

## Cheking for system specific constructs

If we are depending upon specific constructs, we can check that these exists:
```cmake
cmake_minimum_required (VERSION 2.8 FATAL_ERROR)

include (CheckFunctionExists)   # Load and execute the module CheckFunctionExits.cmake
include (CheckStructHasMember)  # # Load and execute the module CheckStructHasMember.cmake

CHECK_FUNCTION_EXISTS(vsnprintf VSNPRINTF_EXISTS)
if (NOT VSNPRINTF_EXISTS)
   message (SEND_ERROR "vsnprintf not available on this build machine")
endif ()

CHECK_STRUCT_HAS_MEMBER("struct __sFILE" _lbfsize stdio.h HAS_SxBUF)
if (NOT HAS_SBUF)
   message (SEND_ERROR "__sbuf field not available in stdio.h")
   endif()

CHECK_STRUCT_HAS_MEMBER("struct rusage" ru_stime wait.h HAS_STIME)
if (NOT HAS_STIME)
   message (SEND_ERROR " ru_stime field not available in struct rusage")
endif()
```

The Check for HAS_STIME fails on my laptop:
```shell
$ cmake ..
-- The C compiler identification is AppleClang 7.3.0.7030031
-- The CXX compiler identification is AppleClang 7.3.0.7030031
-- Check for working C compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/cc
-- Check for working C compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++
-- Check for working CXX compiler: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Looking for vsnprintf
-- Looking for vsnprintf - found
-- Performing Test HAS_SBUF
-- Performing Test HAS_SBUF - Success
-- Performing Test HAS_STIME
-- Performing Test HAS_STIME - Failed
CMake Error at CMakeLists.txt:20 (message):
   ru_stime field not available in struct rusage


-- Configuring incomplete, errors occurred!
```

Here is from a Linux system:
```shell
-- The C compiler identification is GNU 4.3.4
-- The CXX compiler identification is GNU 4.3.4
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Looking for vsnprintf
-- Looking for vsnprintf - found
-- Performing Test HAS_SBUF
-- Performing Test HAS_SBUF - Failed
CMake Error at CMakeLists.txt:15 (message):
  __sbuf field not available in stdio.h


-- Performing Test HAS_STIME
-- Performing Test HAS_STIME - Success
-- Configuring incomplete, errors occurred!
```
Since message() use SEND_ERROR, the processing of the CMakeLists.txt continues despite an error.
