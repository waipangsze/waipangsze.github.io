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

- [OpenFOAM-11，OpenFOAM-v2306 | Jul 13, 2023 | CFD中文网](https://cfd-china.com/topic/6484/openfoam-11-openfoam-v2306), [openFoam-11 | CFD中文网](https://cfd-china.com/topic/7189/openfoam-11)
  - ESI以及基金会纷纷推出OpenFOAM-v2306以及OpenFOAM-v11。后者改动尤其巨大。求解器模块化，比如之前的icoFoam是个求解器，但目前被处理成了一个库。OpenFOAM-v11比之前，在多物理场耦合方面，更加友好。但更加增加了使用难度。
  - 尤其是对于OpenFOAM-11这面。OpenFOAM这面几个大改动，一个是OpenFOAM-3.0，一个是OpenFOAM-11。这个OpenFOAM-11改的，改的相当大。
  - 是。。。OpeNFOAM11改动很大.. 我开始用了都发蒙
  - 你就用paraFoam -buildin也行，我自己也用这个（如果不看做拉格朗日粒子的话）

# Binary/Source Package

- [OpenFOAM Binary/Source Package Repository](https://dl.openfoam.org/)
  - source/	Source Code Packs for Compiling OpenFOAM (v1.7.0-12) on Linux
  - third-party/	Source Code Packs of Third Party Software supporting OpenFOAM (v1.7.0-12) on Linux

# [Software for Compilation](https://openfoam.org/download/source/software-for-compilation/)


{% note primary %}
[Intel OneAPI installation](https://waipangsze.github.io/2023/05/06/Intel_OneAPI/)
{% endnote %}

{% note primary %}
[Recipe: Compile OpenFOAM* with Intel® Compiler for Intel® Xeon® Scalable Processors](https://www.intel.com/content/www/us/en/developer/articles/technical/recipe-compile-openfoam-with-intel-compiler-for-intel-xeon-scalable-processors.html)
{% endnote %}

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
Empty environment created at prefix: /home/wpsze/micromamba/envs/cfd_env
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
source /home/wpsze/openfoam/openfoam11/openfoam_env.sh

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

## 配置運作環境

設定與編譯時相同的 MPI 編譯環境

- 將 OpenFOAM-x.0 裡面的 bin和platforms/linux64IccDPInt32Opt/bin 加入 PATH
- 將 OpenFOAM-x.0 裡面的 platforms/linux64IccDPInt32Opt/lib 及其子目錄加入 LD_LIBRARY_PATH
- 將 ThirdParty-x.0 裡面的 platforms/linux64IccDPInt32/lib 及其子目錄加入 LD_LIBRARY_PATH

## 測試OpenFOAM

- 新建一個測試目錄，將內容複製進去，然後執行OpenFOAM指令測試，如下：
- cp -r OpenFoam-x.0/tutorials/incompressible/simpleFoam/pitzDaily .
- cd pitzDaily
- blockMesh
- simpleFoam

# Compilation Error

## `error in IOstream "OSHA1stream.sinkFile_" for operation Ostream& operator<<(Ostream&, const word&)`

```log
[10] 
[10] 
[10] --> FOAM FATAL IO ERROR: 
[10] error in IOstream "OSHA1stream.sinkFile_" for operation Ostream& operator<<(Ostream&, const word&)
[2] 
[2] 
[2] --> FOAM FATAL IO ERROR: 
[2] error in IOstream "OSHA1stream.sinkFile_" for operation Ostream& operator<<(Ostream&, const word&)
[2] 
[2] file: OSHA1stream.sinkFile_ at line 0.
[2] 
[2]     From function bool Foam::IOstream::check(const char *) const
[2]     in file db/IOstreams/IOstreams/IOstream.C at line 96.
[2] 
FOAM parallel run exiting
```

- This is a known compiler compatibility issue specific to **OpenFOAM 9 (and sometimes 8)** when built with newer GCC versions (such as GCC 11, 12, or 13). The `OSHA1stream` class used for **hashing dictionary entries was not C++11 compliant**, causing it to fail when writing function object output.
-  `icpc -std=c++11 -fp-trap=common -fp-model precise -Dlinux64 -DWM_ARCH_OPTION=64 -DWM_DP -DWM_LABEL_SIZE=32 -Wall -Wextra -Wnon-virtual-dtor -Wno-unused-parameter -     Wno-invalid-offsetof -diag-disable 327,654,1125,1292,2289,2304,11062,11074,11076 -O3  -DNoRepository -I/EM/wpsze/openfoam/CT/openfoam/Installation/OFv8/OpenFOAM-8/     platforms/linux64IccDPInt32Opt/src/OpenFOAM -IlnInclude -I. -I/EM/wpsze/openfoam/CT/openfoam/Installation/OFv8/OpenFOAM-8/src/OpenFOAM/lnInclude -I/EM/wpsze/openfo     am/CT/openfoam/Installation/OFv8/OpenFOAM-8/src/OSspecific/POSIX/lnInclude   -fPIC -c db/IOstreams/IOstreams/IOstream.C -o /EM/wpsze/openfoam/CT/openfoam/Installat     ion/OFv8/OpenFOAM-8/platforms/linux64IccDPInt32Opt/src/OpenFOAM/db/IOstreams/IOstreams/IOstream.o$`

在 OpenFOAM 中，[OpenFOAM osha1stream Class Reference](https://api.openfoam.com/2212/classFoam_1_1osha1stream.html) 是一個用來計算 SHA1 檢查碼（digests）的基本輸出串流類別。[https://api.openfoam.com](https://api.openfoam.com/2212/classFoam_1_1osha1stream.html) 

### 常見錯誤與原因

* 錯誤訊息：**FOAM FATAL IO ERROR in IOstream "OSHA1stream.sinkFile_"**
* 發生時機：在除錯模式（debug mode）下使用 functionObjects 或在 `controlDict` 內新增函數時，程式常會立刻崩潰。
* 根本原因：較新的 GCC 編譯器版本（如 GCC 11.2.0 或 13.x）與舊版 OpenFOAM（v6 至 v9）存在相容性問題。 
* [https://github.com](https://github.com/OpenFOAM/OpenFOAM-9/issues/12)
* [https://aur.archlinux.org](https://aur.archlinux.org/packages/openfoam-org?O=50&PP=10)
* [https://cfd-china.com](https://cfd-china.com/topic/7679/%E5%9C%A8controldict%E6%96%87%E4%BB%B6%E4%B8%AD%E6%B7%BB%E5%8A%A0function%E6%8A%A5%E9%94%99-%E6%B7%BB%E5%8A%A0%E4%B8%9C%E5%B2%B3%E6%B5%81%E4%BD%93%E4%B8%AD%E7%9A%84%E4%B9%9F%E4%BC%9A%E6%8A%A5%E8%BF%99%E4%B8%AA%E9%94%99)


### 解決方法 ***

- [Error in IOstream "OSHA1stream.sinkFile_"... in debug mode when using functionObjects
 #12](https://github.com/OpenFOAM/OpenFOAM-9/issues/12)
- Commit [b0c15be](https://github.com/OpenFOAM/OpenFOAM-9/commit/b0c15bebd37142f3902901ed5e9a60e33ed456eb) solved this problem. Thank you Henry Weller.
- `src/OpenFOAM/db/IOstreams/hashes/OSHA1stream.H`

## error: C++ header location not resolved; check installed C++ dependencies

```sh
    ln: ./lnInclude
    wmkdep: codeStreamTemplate.C
    Ctoo: codeStreamTemplate.C
icpx: error: C++ header location not resolved; check installed C++ dependencies
```

- The error icpx: error: C++ header location not resolved is a classic issue where the Intel oneAPI C++ compiler (icpx) cannot find the standard C++ headers from the GNU compiler (g++) that it relies on internally.
- <https://docs.discoverer.bg/openfoam.html>

{% note primary %}
It's worth noting that on some HPC systems, the **Intel oneAPI compilers (icx/icpx) are not recommended for OpenFOAM builds**. Testing on OpenFOAM 10 and 11 showed that Intel oneAPI compilers did not provide a performance advantage over GCC or LLVM (Clang) for the produced executables.
{% endnote %}

### Solution

- OpenFOAM-v13,

You can use `WM_COMPILER=Gcc` together with `WM_MPLIB=INTELMPI`.

This is a supported and **fairly common combination in OpenFOAM**:

- `WM_COMPILER=Gcc` → uses the GNU compilers (`g++`, `gcc`, `gfortran`)
- `WM_MPLIB=INTELMPI` → links against Intel MPI (`mpiicpc` / `mpiicc` wrappers or the Intel MPI libraries)

### How to set it

```bash
export WM_COMPILER=Gcc
export WM_MPLIB=INTELMPI
```

This pairing is frequently used in HPC environments for good reason:

*   **Proven Performance**: A study comparing compiler/MPI combinations for a CFD solver found that **GCC coupled with Intel MPI (IMPI) outperformed other configurations by nearly 20%**. Another source notes OpenFOAM is often compiled with GCC and Intel MPI specifically for performance reasons. <https://mediatum.ub.tum.de/doc/1446031/1446031.pdf#22#8>, <https://wiki.anl.gov/wiki_tracc/index.php?title=OpenFOAM&diff=1938&oldid=1775>.
*   **Common Practice**: This setup is standard enough that the OpenFOAM `bashrc` explicitly lists `INTELMPI` as a supported option for `WM_MPLIB`. 

- [6.4_在HPC群集上安装OpenFOAM.html](https://topcfd.cn/Ebook/OF_Primer/OF_Primer/6.4_%e5%9c%a8HPC%e7%be%a4%e9%9b%86%e4%b8%8a%e5%ae%89%e8%a3%85OpenFOAM.html)

# [ParaView（後處理工具）安裝](http://dyfluid.com/install.html#paraview)

安裝OpenFOAM之後，還需要安裝ParaView。主要用於對OpenFOAM算例進行後處理。 Paraview的安裝有倆種方式。

- 一種是安裝原版paraivew，
- 一種是在OpenFOAM環境中編譯paraFoam。

{% note primary %}
注意，原版ParaView不能顯示拉格朗日粒子，但處理大網格算例較快。可按照所需進行選擇。也可兩者同時安裝。
{% endnote %}

原版ParaView：可用透過下面的指令來安裝：
```sh
sudo apt install paraview
```
在使用的過程中，可以在OpenFOAM算例下建立一個空檔案並命名為case.foam，然後在終端機鍵入paraview即可運作。運行後，用ParaView打開case.foam即可。

編譯版paraFoam：一次複製下面所有內容到終端機：
```sh
cd $HOME/OpenFOAM/ThirdParty-11 &&
sudo apt-get install git cmake build-essential libgl1-mesa-dev libxt-dev libqt5x11extras5-dev libqt5help5 qttools5-dev qtxmlpatterns5-dev-tools libqt5svg5-devm-py3335-m​​im-pyon ninja-build qtbase5-dev qtchooser qt5-qmake qtbase5-dev-tools &&
./makeParaView &&
wmRefresh &&
cd $FOAM_UTILITIES/postProcessing/graphics/PVReaders &&
./Allwclean &&
./Allwmake
```

其會自動下載 `ParaView` 並開始編譯，編譯過程較長。然後鍵入 `paraFoam` 即可運行，其會自動建立一個後綴為 `.OpenFOAM` 的文件，並自動掛載。

## Create a empty file as `.foam`

在資料夾中新增一個 `.foam` 的空文件，如

```console
$ touch test.foam
```

使用 `paraview` 開啟 `test.foam`，即可查看執行結果。

## Remote paraview (Recommend)

- [Remote and parallel visualization](https://docs.paraview.org/en/latest/ReferenceManual/parallelDataVisualization.html)
- [High performance computing: How to connect to a remote ParaView server](https://youtu.be/-jFw9jVHJgs)

### Remote side (HPC),

- create a conda env

```console
 $ micromamba env create -n paraview
 $ micromamba activate paraview 
 $ micromamba install paraview
```

- open a server with define port number

```console
$ cd /Case-dir/
$ touch case.foam
$ pvserver --server-port=5566
Waiting for client...
Connection URL: cs://node.HPC.com:5566
Accepting connection(s): node.HPC.com:5566
```

If `pvserver: error while loading shared libraries: libcgns.so.3.4: cannot open shared object file: No such file or directory`,



### Local PC,

- Open a new terminal, `ssh -N -L 5566:localhost:5566 username@remote-ip`
- Download the same version of paraview. [link](https://www.paraview.org/download/)
- Open paraview, file --> connect (setup)
  
![](https://i.imgur.com/9szKnJ7.png){width=600}

{% note primary %}
Remark: Please re-build `$ pvserver --server-port=5566` after disconnect. (pvserver is Exited automatically.)
{% endnote %}

{% gi 9 3-3-3 %}
![1](https://i.imgur.com/hcUDTWH.png)
![2](https://i.imgur.com/4MPm2zG.png)
![3](https://i.imgur.com/r3k8rJn.png)
![4](https://i.imgur.com/UgIhObW.png)
![5](https://i.imgur.com/QNfj62G.png)
![6](https://i.imgur.com/6YJTJWr.png)
![7](https://i.imgur.com/G2WTB6I.png)
![8](https://i.imgur.com/ICAoNxU.png)
![9](https://i.imgur.com/O6Pq9ar.png)
{% endgi %}

{% note primary %}
Remark: "Display is not acessible on the server side. ......" may be login/multiple nodes issue. But, the connection is  **Done**.
{% endnote %}

# OpenFOAM多版本共存
不同大廠的OpenFOAM版本各有特性，因此使用者可能有多版本OpenFOAM共存的需求。多版本OpenFOAM共存非常簡單。舉例說明：如果使用者打算在Ubuntu系統上安裝OpenFOAM-11以及OpenFOAM-8，

```sh
alias of11="source ~/OpenFOAM/OpenFOAM-11/etc/bashrc"
alias of8="source ~/OpenFOAM/OpenFOAM-8/etc/bashrc"
```

# Parallel execution

- [decomposePar(1)](https://www.openfoam.com/documentation/guides/latest/man/decomposePar.html)
- [DecomposePar](https://openfoamwiki.net/index.php/DecomposePar)

# Scalability

[200万网格并行算力测试（OpenFOAM版本）](https://www.cfd-china.com/topic/3988/200%E4%B8%87%E7%BD%91%E6%A0%BC%E5%B9%B6%E8%A1%8C%E7%AE%97%E5%8A%9B%E6%B5%8B%E8%AF%95-openfoam%E7%89%88%E6%9C%AC?_=1603587871660&lang=en-US)

- Model name:            Intel(R) Xeon Phi(TM) CPU 7250 @ 1.40GHz
  - cpu MHz		: 1500.915
  - Socket(s) :  1
  - cpu cores	: 68

```console
# cores   Wall time (s):
------------------------
68 215.05
64 240.74
48 290.68
32 298.58
24 365.3
16 544.95
8 721.1
4 1366.75
2 2382.85
1 4529.38

# cores   Wall time (s):
------------------------
204 248.78
136 206.09
68 212.28

# cores   Wall time (s):
------------------------
204 251.24
136 207.51
68 207.64
```

- model name	: Intel(R) Xeon(R) Gold 6342 CPU @ 2.80GHz
  - cpu MHz		: 3500.000
  - Socket(s) :  2
  - cpu cores	: 24
  - siblings	: 48

```console
# cores   Wall time (s):
------------------------
48 55.82
24 92.98
16 132.98
8 175.14
4 348.56
2 554.62
1 920.68
```

{% note warning %}
[高性能计算最佳实践](http://dyfluid.com/HPC.html)
{% endnote %}

## [性能测试CFD标准算例](http://dyfluid.com/standard.html)

- 200万网格/OpenFOAM
  - 适用于机器内存大于16G的机器
- 2000万网格/OpenFOAM
  - 适用于机器内存大于70G的机器
- 2亿网格/OpenFOAM
  - 适用于机器内存大于512G的机器

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