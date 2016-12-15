---
layout: episode
title: "Find command family"
teaching: 5
exercises: 0
questions:
  - "How can I find programs, libraries, files, and paths with CMake?"
---

## The Find command family

### High-level module finding mechanism

- `find_package`

### Lower-level finding commands

- `find_program` - find an executable program
- `find_library` - find a library
- `find_file` - find any kind of file
- `find_path` - find a path where some files reside

---

## MPI and CMake

```cmake
option(ENABLE_MPI "Enable MPI" OFF)

if(ENABLE_MPI)
    find_package(MPI) # uses FindMPI.cmake
    if(MPI_FOUND)
        include_directories(${MPI_INCLUDE_PATH})
        add_definitions(-DENABLE_MPI)
    else()
        message(FATAL_ERROR "-- Could not find any MPI installation, check $PATH")
    endif()
endif()
```

- Build process:

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
