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

原理：OpenFOAM編譯需要一些軟體和函式庫，像是 `flex`, `zlib` 之類

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
 $ micromamba install conda-forge::bison
 $ micromamba install conda-forge::cgal
 $ micromamba install anaconda::metis
```

## 載入環境

安裝需要載入 Intel 編譯環境和 gcc 環境，且 gcc 環境不應高於 Intel 編譯器環境

## Environment variables

### WM options

```sh
WM_THIRD_PARTY_DIR=$HOME/OpenFOAM/ThirdParty-1.6.x
WM_PROJECT_INST_DIR=$HOME/OpenFOAM
WM_PROJECT_DIR=$HOME/OpenFOAM/OpenFOAM-1.6.x
WM_DIR=$HOME/OpenFOAM/OpenFOAM-1.6.x/wmake
WM_PROJECT_USER_DIR=$HOME/OpenFOAM/$USER-1.6.x

WM_PRECISION_OPTION=DP
WM_ARCH_OPTION=64
WM_LINK_LANGUAGE=c++
WM_ARCH=linux64
WM_CXXFLAGS=-m64 -fPIC
WM_CFLAGS=-m64 -fPIC
WM_PROJECT_VERSION=1.6.x
WM_COMPILER_LIB_ARCH=64
WM_CXX=g++
WM_COMPILER_ARCH=
WM_PROJECT=OpenFOAM
WM_LDFLAGS=-m64
WM_COMPILER=Gcc
WM_MPLIB=OPENMPI
WM_CC=gcc
WM_COMPILE_OPTION=Opt
WM_OPTIONS=linux64GccDPOpt
WM_PRECISION_OPTION=DP
WM_ARCH_OPTION=64
```

### FOAM options

```sh
FOAM_SOLVERS=$HOME/OpenFOAM/OpenFOAM-1.6.x/applications/solvers
FOAM_APPBIN=$HOME/OpenFOAM/OpenFOAM-1.6.x/applications/bin/linux64GccDPOpt
FOAM_TUTORIALS=$HOME/OpenFOAM/OpenFOAM-1.6.x/tutorials
FOAM_JOB_DIR=$HOME/OpenFOAM/jobControl
FOAM_LIB=$HOME/OpenFOAM/OpenFOAM-1.6.x/lib
FOAM_SITE_APPBIN=$HOME/OpenFOAM/site/1.6.x/bin/linux64GccDPOpt
FOAM_MPI_LIBBIN=$HOME/OpenFOAM/OpenFOAM-1.6.x/lib/linux64GccDPOpt/openmpi-1.3.3
FOAM_APP=$HOME/OpenFOAM/OpenFOAM-1.6.x/applications
FOAM_SITE_LIBBIN=$HOME/OpenFOAM/site/1.6.x/lib/linux64GccDPOpt
FOAM_SRC=$HOME/OpenFOAM/OpenFOAM-1.6.x/src
FOAM_SIGFPE=
FOAM_UTILITIES=$HOME/OpenFOAM/OpenFOAM-1.6.x/applications/utilities
FOAM_USER_LIBBIN=WM_PROJECT_USER_DIR/lib/linux64GccDPOpt
FOAM_INST_DIR=$HOME/OpenFOAM
FOAM_LIBBIN=$HOME/OpenFOAM/OpenFOAM-1.6.x/lib/linux64GccDPOpt
FOAM_RUN=WM_PROJECT_USER_DIR/run
FOAM_USER_APPBIN=WM_PROJECT_USER_DIR/applications/bin/linux64GccDPOpt
```

### Other options

```sh
MPI_ARCH_PATH=$HOME/OpenFOAM/ThirdParty-1.6.x/openmpi-1.3.3/platforms/linux64GccDPOpt
ParaView_INST_DIR=$HOME/OpenFOAM/ThirdParty-1.6.x/paraview-3.6.1
OPAL_PREFIX=$HOME/OpenFOAM/ThirdParty-1.6.x/openmpi-1.3.3/platforms/linux64GccDPOpt
ParaView_DIR=$HOME/OpenFOAM/ThirdParty-1.6.x/paraview-3.6.1/platforms/linux64Gcc
PARAVIEW_DIR=/opt/packages/Paraview3
ParaView_VERSION=3.6.1
MPI_HOME=$HOME/OpenFOAM/ThirdParty-1.6.x/openmpi-1.3.3
```

## 修改OpenFOAM的配置

### For OpenFOAM-11/`etc/bashrc`,

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

### For **cannot find -lz** issude,

#### check -lz

使用以下命令查詢gcc能否搜尋到指定的庫檔:

```sh
 $ gcc -lz --verbose
Using built-in specs.
COLLECT_GCC=gcc
COLLECT_LTO_WRAPPER=/usr/libexec/gcc/x86_64-redhat-linux/8/lto-wrapper
...
/usr/bin/ld: cannot find -lz
collect2: error: ld returned 1 exit status
```
若安裝了軟體，找到了庫檔案的路徑。但依然會提示上述錯誤。則表示gcc的搜尋路徑不包含該庫檔案所在的路徑。

- 修改環境變數
  - `$ export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/biosoft/hdf5-1.8.15-patch1/lib/`
  - 修改環境變數 `LD_LIBRARY_PATH`，加入函式庫檔案所在路徑。使用 `export` 指令使修改生效。
  - `$ echo 'export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/biosoft/hdf5-1.8.15-patch1/lib/' >> ~/.bashrc`
  - `$ source ~/.bashrc`
  - 將上述 `export` 指令加入設定檔 `~/.bashrc`，使其永久生效。
  - `$ export LIBRARY_PATH=/opt/biosoft/hdf5-1.8.15-patch1/lib/:$LIBRARY_PATH`
  - **若修改變數 `LD_LIBRARY_PATH` 不奏效，則修改變數 `LIBRARY_PATH` 。**

1. **cannot find -lz**
   1. `-lz`  - is zlib, <http://zlib.net/>
      1. You may need `-L/opt/lib` to specify a path. This should give you another linker error, **looking for zlib**. Then add `-lz`.
   2. `-lm`  - is the math library as you've worked out (implementation defined AFAIK)
   3. `-lrt` - provides POSIX realtime extensions: <http://www.s-gms.ms.edus.si/cgi-bin/man-cgi?librt+3LIB>
2. vim `ThirdParty-11/scotch_6.0.9/src/Makefile.inc`

```sh
  6 AR      = icx

  9 CCS     = icx
 10 CCP     = mpiicc
 11 CCD     = mpiicc
```

## setup env

- **openfoam_env.sh**

```sh
## Openfoam needs
source /home/wpsze/micromamba/etc/profile.d/micromamba.sh
micromamba activate cfd_env

source /home/wpsze/intel/oneapi/setvars.sh intel64
export MPI_ROOT=/home/wpsze/intel/oneapi/mpi/2021.9.0
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/wpsze/micromamba/envs/cfd_env/lib
export C_INCLUDE_PATH=/home/wpsze/micromamba/envs/cfd_env/include
export CPLUS_INCLUDE_PATH=/home/wpsze/micromamba/envs/cfd_env/include

export LIBRARY_PATH=/home/wpsze/micromamba/envs/cfd_env/lib
export PATH=/home/wpsze/intel/oneapi/mpi/2021.9.0/bin:$PATH

make --version
cmake --version
gcc --version
g++ --version
gfortran --version
which mpiifort
mpiifort --version
which mpiicc
mpiicc --version

mpirun -np 4 echo "hello"
```

## 編譯安裝 ThirdParty

進入到 ThirdParty 的解壓縮目錄

在執行編譯之前，**先輸入指令 echo $MPI_ROOT 查看該變數是否有值！** 這點十分重要，如果MPI_ROOT為空，那麼編譯 ThirdParty 不會報錯，**但是會有一個warning資訊說缺少MPI_ROOT，會缺少 ptscotchf.h** 。這就導致在編譯 OpenFOAM的時候，**會報錯說缺少 ptscotchf.h 導致編譯失敗！**一般來說，載入intel編譯環境之後會自動 `export MPI_ROOT` 這個變量，但是也可以手動設置，輸入 `which mpicc` ，然後複製該目錄，複製到 mpi 層即可！

然後執行 `./Allclean` 進行清理，再執行 `./Allwmake` 進行編譯

如果發生報錯：本目錄不是 WM_THIRD_PARTY_DIR 目錄。說明 OpenFOAM 的 bashrc 檔案配置出現了問題，請確保 WM_THIRD_PARTY_DIR 所指定的目錄確定是ThirdParty目錄的上層目錄。

編譯完成之後，在同一會話視窗下回到OpenFOAM的安裝目錄，然後開始編譯安裝OpenFOAM


```sh
source /EM/wpsze/openfoam/openfoam11/openfoam_env.sh

source /home/wpsze/openfoam/openfoam11/OpenFOAM-11/etc/bashrc
cd /home/wpsze/openfoam/openfoam11/ThirdParty-11

pwd
echo $PATH
echo $MPI_ROOT

./Allclean
./Allwmake | tee Allwmake.log
```

output:

```sh
========================================
Build Metis decomposition
    optional component Metis was not found
========================================
Build Zoltan decomposition
    optional component Zoltan was not found

========================================
Done ThirdParty Allwmake
========================================
```

## 編譯安裝 OpenFOAM

進入到OpenFOAM 的安裝目錄，執行 `./Allwmake -j` 以開始編譯，`-j` 這一參數的意思是調用起所有的CPU核心來並行編譯，當然，如果不希望調用起這麼多核，也可以指定核心數，例如 .`/Allwmake -j 8` ，只使用8 個核心

編譯過程十分漫長，如果編譯中途報錯說無法建立xx目錄，手動建立即可，然後重新執行 `./Allwmake -j` 會從中斷處開始編譯而不是重新從頭開始編譯。

```sh
source /home/wpsze/openfoam/openfoam11/openfoam_env.sh

source /home/wpsze/openfoam/openfoam11/OpenFOAM-11/etc/bashrc
cd /home/wpsze/openfoam/openfoam11/OpenFOAM-11

pwd
echo $MPI_ROOT

./Allclean
./Allwmake -j 16 | tee Allwmake.log
```


# References

1. [使用Intel编译器编译OpenFOAM](https://blog.csdn.net/weixin_49181094/article/details/107345305)
2. [使用OpenFOAM软件进行流体力学仿真计算](https://www.ctyun.cn/document/20661708/10239799)
3. [Linux 平台 OpenFOAM 安装及调试指南【附安装脚本】](https://blog.qiql.net/archives/openfoam)
4. [天河 2 号超算安装 OpenFOAM 7 (不含CGAL)](https://cfd-china.com/topic/3465/%E5%A4%A9%E6%B2%B3-2-%E5%8F%B7%E8%B6%85%E7%AE%97%E5%AE%89%E8%A3%85-openfoam-7-%E4%B8%8D%E5%90%ABcgal)
5. [y.tab.c:270:6: error: conflicting types for ‘scotchyyerror’](https://community.intel.com/t5/Intel-MPI-Library/Parallel-CFD-simulations-with-OpenFOAM/m-p/1466576)
   1. It means that the scotch library from ThirdParty-10 did not compile successfully. Do you have any ideas about how to solve it? I changed the line 65 in etc/bashrc to use the Intel compiler (because I think that it was possible to compile scotch with icx), however the scotch compilation during the execution of ./Allwmake still used gcc despite my changes.
6. [/usr/bin/ld: cannot find -lxxx 的解决办法](https://www.cnblogs.com/zhming26/p/6164131.html)
7. [LIBRARY_PATH和LD_LIBRARY_PATH环境变量的区别](https://www.cnblogs.com/panfeng412/archive/2011/10/20/library_path-and-ld_library_path.html)
   1. `LIBRARY_PATH` 环境变量用于在**程序编译期间**查找动态链接库时指定查找共享库的路径
   2. `LD_LIBRARY_PATH` 环境变量用于在**程序加载运行期间**查找动态链接库时指定除了系统默认路径之外的其他路径