---
layout: episode
title: "Find command family"
teaching: 5
exercises: 0
questions:
  - How can we find programs, libraries, files, and paths with CMake?
---

## CMake find functions

As you develop code and your code base grow, you will most certainly depend
upon external packages or libraries.

CMake provides several find functions, which you can use to search for files,
libraries, paths, header files, or entire packages:

- [find_file](https://cmake.org/cmake/help/latest/command/find_file.html)
- [find_library](https://cmake.org/cmake/help/latest/command/find_library.html)
- [find_path](https://cmake.org/cmake/help/latest/command/find_path.html)
- [find_program](https://cmake.org/cmake/help/latest/command/find_program.html)
- [find_package](https://cmake.org/cmake/help/latest/command/find_package.html)

---

## Find-modules that ship with CMake

- [https://github.com/Kitware/CMake/tree/master/Modules](https://github.com/Kitware/CMake/tree/master/Modules)

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

## Example: [FindMPI.cmake](https://github.com/Kitware/CMake/blob/master/Modules/FindMPI.cmake)

[FindMPI.cmake](https://github.com/Kitware/CMake/blob/master/Modules/FindMPI.cmake) is a relatively
advanced example. Search for "find\_" in that module.

In this example we try to find MPI provided we enable MPI.

If we enable it and cannot find it, we ask CMake to abort the configuration:

```cmake
cmake_minimum_required(VERSION 2.8)

option(ENABLE_MPI "Enable MPI" OFF)

if(ENABLE_MPI)
   find_package(MPI)
   if (MPI_FOUND)
      include_directories(${MPI_INCLUDE_PATH})
      add_definitions(-DENABLE_MPI)
   else()
	message(FATAL_ERROR "Could not find any MPI installation, check $PATH")  # CMake Error, stop processing and generation
   endif()
endif()
```

Configuration on a system with MPI installed:

```shell
$ mkdir build
$ cd build
$ FC=mpif90 CC=mpicc CXX=mpicxx cmake -DENABLE_MPI=ON ..
$ make [-jN]
```
