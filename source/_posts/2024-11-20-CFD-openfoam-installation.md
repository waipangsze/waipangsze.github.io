---
layout: post
title: "CFD | OpenFOAM Installation"
categories: [CFD]
tags: [OpenFOAM,HPC]
author: wpsze
date: 2024-11-20 16:21:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/fFT42Yk.png
banner_img: https://i.imgur.com/fFT42Yk.png
---

# OpenFOAM


OpenFOAM是一個CFD軟體。它包括一系列應用程序，這些應用程式在CFD中執行一系列任務。應用程式主要分為兩類：[link](http://www.dyfluid.com/openfoam11-1.html#id1)

- 求解器，用於執行CFD計算；
- 以及實現上述任務的其他程序；

在OpenFOAM-11之前，每個流動類型都需要呼叫特定的求解器。由於流動類型非常多，OpenFOAM層包含了近100個解算器。像simpleFoam和pimpleFoam這樣的求解器自上世紀90年代初以來一直是OpenFOAM的主力。

然而**OpenFOAM-11引進了模組化求解器，其為一個重大的改進**。應用程式解算器被單一foamRun求解器取代，foamRun是一個框架性求解器。 foamRun可以載入一個求解器模組，該模組繼續呼叫對應的流動類型。例如之前simpleFoam以及pisoFoam的算例，都採用foamRun求解器來運作。在foamRun求解器程式碼中，會依據對應的演算法進行調整。這在程式碼層次的改動是相當大的。 OpenFOAM-11可以認為是一次徹底的改變。官方將這次改動的原因解釋為更加的貼近多物理場的特性，對於多物理場的耦合更加的成熟。當然這裡面也存在商業上的原因。

特別的，OpenFOAM-11還有一個foamMultiRun求解器，它可以接受兩個或更多域區域，並為每個區域應用不同的求解器模組。例如，它可以將一個或多個流體和固體的模組耦合起來，典型的引用就是多區域傳熱流動。

- [OpenFOAM Binary/Source Package Repository](https://dl.openfoam.org/)
  - source/	Source Code Packs for Compiling OpenFOAM (v1.7.0-12) on Linux
  - third-party/	Source Code Packs of Third Party Software supporting OpenFOAM (v1.7.0-12) on Linux

# [Software for Compilation](https://openfoam.org/download/source/software-for-compilation/)

Repository and compilation software:

- `Git` distributed version control software used for the OpenFOAM source repositories.
- `Compiler`: either GCC version 5.5 or above; or LLVM Clang version 16 or above (possibly earlier); or the Intel oneAPI DPC++/C++ Compiler version 2021.3.0 or above. GCC is most commonly available and the version can be checked by typing
- `FLEX` fast lexical analyser, used by OpenFOAM for reading files of third-party format
- `cmake` build software for compiling ParaView, the third-party visualisation toolkit.

Software for `OpenFOAM` (and `ParaView`):

- `OpenMPI` message passing interface for parallel computation.
- `ParaView` can be installed as a system package, in which case its dependencies do not need to be installed. However, in order to compile its reader modules within OpenFOAM, all its dependencies need to be installed including the development environments of QT, graphics (GL), data formats, etc

```sh
 $ micromamba env create -n cfd_env
Empty environment created at prefix: /EM/wpsze/micromamba/envs/cfd_env
 $ micromamba install conda-forge::zlib
 $ micromamba install anaconda::cmake
 $ micromamba install conda-forge::flex
```

For OpenFOAM-11/`etc/bashrc`,

```sh
 59 #- Compiler location:
 60 #    WM_COMPILER_TYPE= system | ThirdParty (OpenFOAM)
 61 export WM_COMPILER_TYPE=system

 63 #- Compiler:
 64 #    WM_COMPILER = Gcc | Gcc48 ... Gcc62 | Clang | Icx
 65 export WM_COMPILER=Icx
 66 unset WM_COMPILER_ARCH WM_COMPILER_LIB_ARCH

 86 #- MPI implementation:
 87 #    WM_MPLIB = SYSTEMOPENMPI | OPENMPI | SYSTEMMPI | MPICH | MPICH-GM | HPMPI
 88 #               | MPI | FJMPI | QSMPI | SGIMPI | INTELMPI
 89 export WM_MPLIB=INTELMPI 
```

and,

```sh
source /home/wpsze/intel/oneapi/setvars.sh intel64
export MPI_ROOT=/home/wpsze/intel/oneapi/mpi/2021.9.0

source /home/wpsze/openfoam/openfoam11/OpenFOAM-11/etc/bashrc
cd /home/wpsze/openfoam/openfoam11/OpenFOAM-11

./Allwmake -j 16 | tee Allwmake.log
```