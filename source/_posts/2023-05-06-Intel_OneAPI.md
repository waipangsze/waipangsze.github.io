---
layout: post
title: "Intel OneAPI"
categories: [Linux]
tags: [WRF,HPC]
author: wpsze
date: 2023-05-06 10:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/j0YSOSe.png
banner_img: https://i.imgur.com/j0YSOSe.png
---

# Intel OneAPI

The Intel oneAPI HPC Toolkit is a comprehensive suite of development tools that make it fast and easy to build modern code that gets maximum performance out of the newest Intel® processors. This toolkit enables high performance computing on clusters or individual nodes with flexible options including optimal performance on a CPU or GPU.

Intel oneAPI 是一種統一的跨架構的程式設計模型，提供了CPU、GPU、FPGA、專用加速器的產品。

2020年12月，英特爾發布了oneAPI軟體開發套裝，免費、並且取代了之前需要許可(購買)的Intel Parallel Studio。

Intel oneAPI toolkits包含了六大工具包：

- Intel® oneAPI Base Toolkit(包含了Intel Parallel Studio中常用的軟體以及 icc、MPI、DPCPP、MKL等)
- Intel® oneAPI HPC Toolkit(提供可擴充的快速C ++、Fortran、OpenMP和MPI應用程式)
- Intel® oneAPI IoT Toolkit
- Intel® oneAPI Rendering Toolkit
- Intel® AI Analytics Toolkit
- Intel® Distribution of OpenVINO™ Toolkit

{% note primary %}
**對我們來說 Intel® oneAPI Base Toolkit + Intel® oneAPI HPC Toolkit 基本上就包含Intel Parallel Studio XE的功能了，關鍵還免費。**
{% endnote %}

Intel OneAPI Toolkit 使用一流的編譯器, 效能庫, 框架以及分析和偵錯工具在CPU 和XPU 上分析和優化高效能, 跨架構應用程式. 我們的伺服器上安裝了Base Toolkit 和HPC Toolkit, 包含ic​​c compiler, debugger, mkl 數學庫, intel MPI, vtune 等軟體, 請參閱官網查看完整軟體清單及使用方法。

Intel® oneAPI Base Toolkit: 「Intel® oneAPI基本工具包」（Base Kit）是一套核心工具和函式庫，用於跨各種體系結構(CPU、GPU和FPGA)開發高效能、以資料為中心的應用程式. 它採用了業界領先的C++編譯器，實作了SYCL\*，這是C++在異質運算上的發展。可以使用其他工具包來補充基本工具包,比如Intel® HPC Toolkit(包括Intel® Fortran編譯器， OpenMP\* GPU offload和Intel® MPI庫)，Intel® Rendering Toolkit(渲染和光線跟踪，用於高保真可視化應用程式)

Intel® HPC Toolkit：Build, analyze, and scale applications across shared- and distributed-memory computing systems. 高效能運算（HPC）是人工智慧、機器學習和深度學習應用程式的核心。 「Intel® HPC Toolkit」（HPC工具包）利用向量化、多執行緒、多節點並行化和記憶體最佳化的最新技術， 為開發人員提供建置、分析、最佳化和擴展HPC應用程式所需的功能。 此工具包是「Intel® oneAPI Base Toolkit」的附加元件，是實現完整功能所必需的。它包括功能強大的以數據為中心的庫、高級分析工具和Intel® Python*發行版，以實現核心Python數值、科學和機器學習套件的近乎本地程式碼效能。

原來用來編譯intel程式的「Intel全家桶」指的是Intel Parallel Studio XE，裡面整合了C/C++/Fortran語言的編譯器（分別是icc、icpc、ifort）、MKL數學函式庫及各種高效調試工具，甚至還有Intel MPI， 可以說是編譯量化軟體最常用的一套「裝備」。然而從2021年開始Intel不再提供舊版Parallel Studio XE下載頁面，而是改成了（或稱升級成了）oneAPI， oneapi包括上述6個套件。

但Base Toolkit套件裡含icc、icpc編譯器和MKL函式庫，卻不含ifort。而HPC Toolkit包裡含icc、icpc、ifort，卻不含MKL庫。顯然下載任一個包都無法完全取代以前的「全家桶」。 因此平台採用安裝Intel Base Toolkit和HPC Toolkit方法，來取代先前的Parallel Studio XE。

Intel® VTune™ Profiler:發現並最佳化CPU、GPU和FPGA系統的效能瓶頸。

## Command Line Download
Command Line Installation Parameters

Get latest version <https://www.intel.com/content/www/us/en/developer/tools/oneapi/hpc-toolkit-download.html?operatingsystem=linux&distributions=offline>

- 下載兩個 Linux 的 sh 檔：
  - [Get the Intel® oneAPI Base Toolkit](https://www.intel.com/content/www/us/en/developer/tools/oneapi/base-toolkit-download.html?packages=oneapi-toolkit&oneapi-toolkit-os=linux&oneapi-lin=offline)
  - [Get Intel® oneAPI HPC Toolkit](https://www.intel.com/content/www/us/en/developer/tools/oneapi/hpc-toolkit-download.html?packages=hpc-toolkit&hpc-toolkit-os=linux&hpc-toolkit-lin=offline)

```sh
wget https://registrationcenter-download.intel.com/akdlm/IRC_NAS/7deeaac4-f605-4bcf-a81b-ea7531577c61/l_BaseKit_p_2023.1.0.46401_offline.sh
wget https://registrationcenter-download.intel.com/akdlm/IRC_NAS/1ff1b38a-8218-4c53-9956-f0b264de35a4/l_HPCKit_p_2023.1.0.46346_offline.sh

sh ./l_BaseKit_p_2023.1.0.46401_offline.sh (**Install dependence**)
sh ./l_HPCKit_p_2023.1.0.46346_offline.sh
```

For BaseKit, there is no dependence needs **if doesn't install Intel Advisor and Intel VTune**,

- [Dependencies (10)](https://archlinux.org/packages/extra/x86_64/intel-oneapi-basekit/)
- Intel Advisor，它可以幫助開發者對程式進行並行處理的優化，提高程式的性能 [link](https://www.toolify.ai/tw/hardwaretw/intel-advisor%E7%A8%8B%E5%BC%8F%E5%84%AA%E5%8C%96%E7%9A%84%E5%BC%B7%E5%A4%A7%E5%B7%A5%E5%85%B7-3170948)
- Intel VTune 是intel公司開發的一款強大的效能瓶頸分析軟體，能幫助開發者找出效能影響因素

![](https://i.imgur.com/5bQ4Nch.png){width=400}

or,

download list: <https://get.hpc.dev/vault/intel/>

or,

Direct download,

- intel-oneapi-base-toolkit-2025.0.0.885_offline.sh (2.4GB)
- intel-oneapi-hpc-toolkit-2025.0.0.825_offline.sh (2.4GB)

### Installation Instructions
The initial download is for the installer application files only. The installer will acquire all the tools during the installation process.

1. Step 1: From the console, locate the downloaded install file.
2. Step 2: 
   1. Use $ sudo sh ./<installer>.sh to launch the GUI Installer as root.
   2. Optionally, use $ sh ./<installer>.sh to launch the GUI Installer as current user.
3. Step 3: Follow the instructions in the installer.
4. Step 4: Explore the Get [Started Guide](https://www.intel.com/content/www/us/en/develop/documentation/get-started-with-intel-oneapi-hpc-linux/top.html).

### (Option) Get Started with the Intel oneAPI HPC Toolkit for Linux

Although the `CMake` and `pkg-config` build tools are not required by the oneAPI tools and toolkits, many oneAPI samples are provided as `CMake` projects and require `CMake` to build them. In some cases `pkg-config` is necessary to locate libraries needed to complete a build of the application.

The Intel compilers utilize the existing GNU build toolchains to provide a complete C/C++ development environment. If your distribution of Linux does not include the complete suite of GNU development tools, you need to install these tools.

To install `CMake`, `pkg-config`, and the GNU development tools on your Linux system, open a terminal session and enter the following commands:

```sh
sudo apt update
sudo apt -y install cmake pkg-config build-essential
```

Verify the installation by displaying the installation location with this command:
```sh
which cmake pkg-config make gcc g++
```

### Install on cluster (no sudo)

Online/Offline Installer:

- [Online/Offline Installer](https://www.intel.com/content/www/us/en/docs/oneapi/installation-guide-linux/2025-0/online-offline-installer-003.html#HPC-ONLINE-OFFLINE)
- If you do not have root (administrative or sudo) permissions, you can install the toolkit only in **your home directory** or **a user-specific location**. Refer to user installation commands in this document.
  - `--silent` (Run the installer in non-interactive (silent) mode.)
  - `--install-dir` (Silent, default installation directory, Supported in silent mode. Customize the installation directory.)
- [Command Line Options](https://www.intel.com/content/www/us/en/docs/oneapi/installation-guide-linux/2025-0/command-line-options.html#BASE-COMMAND-LINE-OPTIONS)

{% note primary %}
/home/wpsze/intel/oneapi/
{% endnote %}

```sh
 $ sh ./l_BaseKit_p_2023.1.0.46401_offline.sh 
Extract l_BaseKit_p_2023.1.0.46401_offline to /home/wpsze/intel/l_BaseKit_p_2023.1.0.46401_offline...
[######################################################################################################################################################################]
Extract l_BaseKit_p_2023.1.0.46401_offline completed!
Remove extracted files: /home/wpsze/intel/l_BaseKit_p_2023.1.0.46401_offline...
```

{% gi 2 2 %}
![1](https://i.imgur.com/ppnqwDo.png)
![2](https://i.imgur.com/3C7FkYU.png)
{% endgi %}

```sh
 $ bash l_HPCKit_p_2023.1.0.46346_offline.sh 
Extract l_HPCKit_p_2023.1.0.46346_offline to /home/wpsze/intel/l_HPCKit_p_2023.1.0.46346_offline...
[#####################################################################################################################################################################]
Extract l_HPCKit_p_2023.1.0.46346_offline completed!
Remove extracted files: /home/wpsze/intel/l_HPCKit_p_2023.1.0.46346_offline...
```

{% gi 7 3-3-1 %}
![1](https://i.imgur.com/DDb4wOZ.png)
![2](https://i.imgur.com/6JzxBe4.png)
![3](https://i.imgur.com/i1WGV00.png)
![4](https://i.imgur.com/UvVfnH8.png)
![5](https://i.imgur.com/EFY2UFk.png)
![6](https://i.imgur.com/DfGTm66.png)
![7](https://i.imgur.com/j0YSOSe.png)
{% endgi %}

### Compiler 對照表

- [Compiler 對照表](https://man.twcc.ai/@TWCC-III-manual/SkgZUHhi_)

|             | **GNU**      | **Intel** | **MVAPICH2**          | **OpenMPI**           | **IntelMPI** |
|-------------|--------------|-----------|-----------------------|-----------------------|--------------|
| **C**       | gcc          | icc/icx   | mpicc                 | mpicc                 | mpiicc       |
| **C++**     | g++          | icpc/icpx | mpic++/ mpicxx        | mpiCC/mpic++/mpicxx   | mpiicpc      |
| **Fortran** | g77/gfortran | ifort/ifx | mpif77/mpif90/mpifort | mpifort/mpif77/mpif90 | mpiifort     |

### Check

In my caes, it is installed in /home/wpsze/intel/oneapi/

> remark #10441: The Intel(R) C++ Compiler Classic (ICC) is deprecated and will be removed from product release in the second half of 2023. The Intel(R) oneAPI DPC++/C++ Compiler (ICX) is the recommended compiler moving forward. Please transition to use this compiler. Use '-diag-disable=10441' to disable this message.

{% note primary %}
Intel oneAPI's `icc` and `icpc` have been replaced by `icx`. We do not have `icc` and `icpc` installed on our servers, so please use `icx` for C and C++ compilation. The usage is similar to the original compiler, for example: `icx` your_code.cpp -o output.
{% endnote %}

ICC (Linux), ICL (Windows) are classic Intel C/C++ Compilers. Whereas, ICX is Intel nextgen compiler based on Clang /LLVM technology plus Intel proprietary optimizations and code generation.

1. You may use ICC for performance on CPU targets.
2. ICX enables OpenMP TARGET offload to Intel GPU targets.

The following are the guiding principles for ICX:

> 1. ICX and ICC Classic use different compiler drivers. The Intel® C++ Compiler Classic compiler drivers are icc, icpc, and icl.  The Intel® oneAPI DPC++/C++ Compiler drivers are icx and icpx. Use icx to compile and link C programs, and icpx for C++ programs.

> 2. Unlike the icc driver, icx does not use the file extension to determine whether to compile as C or C+. Users must invoke icpx to compile C+ files. . In addition to providing a core C++ Compiler, ICX/ICPX is also used to compile SYCL/DPC++ codes for the Intel® oneAPI Data Parallel C++ Compiler when we pass an additional flag “-fsycl”. 


```sh
source /home/wpsze/intel/oneapi/setvars.sh intel64

which icc
which ifort 
which icpc
icc --version
ifort --version
icpc --version
which icx
icx --version
which icpx
icpx --version

echo "======================"

which mpiicc
which mpiicpc
which mpiifort
mpicc -v
mpiicpc -v
mpiifort -v

# print as

:: initializing oneAPI environment ...
   test.sh: BASH_VERSION = 4.4.20(1)-release
   args: Using "$@" for setvars.sh arguments: 
:: ccl -- latest
:: clck -- latest
:: compiler -- latest
:: dal -- latest
:: debugger -- latest
:: dev-utilities -- latest
:: dnnl -- latest
:: dpcpp-ct -- latest
:: dpl -- latest
:: inspector -- latest
:: ipp -- latest
:: ippcp -- latest
:: ipp -- latest
:: itac -- latest
:: mkl -- latest
:: mpi -- latest
:: tbb -- latest
:: oneAPI environment initialized ::

#/home/wpsze/intel/oneapi/compiler/2023.1.0/linux/bin/intel64/icc
#/home/wpsze/intel/oneapi/compiler/2023.1.0/linux/bin/intel64/ifort
#/home/wpsze/intel/oneapi/compiler/2023.1.0/linux/bin/intel64/icpc

$ icc --version
icc: remark #10441: The Intel(R) C++ Compiler Classic (ICC) is deprecated and will be removed from product release in the second half of 2023. The Intel(R) oneAPI DPC++/C++ Compiler (ICX) is the recommended compiler moving forward. Please transition to use this compiler. Use '-diag-disable=10441' to disable this message.
icc (ICC) 2021.9.0 20230302
Copyright (C) 1985-2023 Intel Corporation.  All rights reserved.

$ ifort --version
ifort (IFORT) 2021.9.0 20230302
Copyright (C) 1985-2023 Intel Corporation.  All rights reserved.

$ icpc --version
icpc: remark #10441: The Intel(R) C++ Compiler Classic (ICC) is deprecated and will be removed from product release in the second half of 2023. The Intel(R) oneAPI DPC++/C++ Compiler (ICX) is the recommended compiler moving forward. Please transition to use this compiler. Use '-diag-disable=10441' to disable this message.
icpc (ICC) 2021.9.0 20230302
Copyright (C) 1985-2023 Intel Corporation.  All rights reserved.

$ which icx
# /home/wpsze/intel/oneapi/compiler/2023.1.0/linux/bin/icx

$ icx --version
Intel(R) oneAPI DPC++/C++ Compiler 2023.1.0 (2023.1.0.20230320)
Target: x86_64-unknown-linux-gnu
Thread model: posix
InstalledDir: /home/wpsze/intel/oneapi/compiler/2023.1.0/linux/bin-llvm
Configuration file: /home/wpsze/intel/oneapi/compiler/2023.1.0/linux/bin-llvm/../bin/icx.cfg

$ which icpx
# /home/wpsze/intel/oneapi/compiler/2023.1.0/linux/bin/icpx

$ icpx --version
Intel(R) oneAPI DPC++/C++ Compiler 2023.1.0 (2023.1.0.20230320)
Target: x86_64-unknown-linux-gnu
Thread model: posix
InstalledDir: /home/wpsze/intel/oneapi/compiler/2023.1.0/linux/bin-llvm
Configuration file: /home/wpsze/intel/oneapi/compiler/2023.1.0/linux/bin-llvm/../bin/icpx.cfg

======================
/home/wpsze/intel/oneapi/mpi/2021.9.0/bin/mpiicc
/home/wpsze/intel/oneapi/mpi/2021.9.0/bin/mpiicpc
/home/wpsze/intel/oneapi/mpi/2021.9.0/bin/mpiifort
```
Remark

1. The icc and icpc commands resemble the GNU equivalents gcc and g++. This is a highly optimizing C (icc) and C++ (icpc) compiler.

## Set Environment Variables for CLI Development

Option 1: Source setvars.sh once per session

Source setvars.sh every time you open a new terminal window:

You can find the setvars.sh script in the root folder of your oneAPI installation, which is typically /opt/intel/oneapi/ for system wide installation and ~/intel/oneapi/ when installed as a private installation. Note that system wide installation requires root or sudo privileges.

For system wide installations:
```sh
source /opt/intel/oneapi/setvars.sh intel64
```

For private installations:
```sh
source ~/intel/oneapi/setvars.sh intel64
```

## Build and Run a Sample Project Using the Command Line
To compile and run a sample:

1. Download the sample using the oneAPI CLI Samples Browser. (Command line interface (CLI))

```sh
$ oneapi-cli 
```

2. Compile and run the sample with Make*

```sh
$ cd /home/wpsze/intel/oneapi/matrix_mul 
$ make
$ sh run.sh
```
Output:
> start: 23/05/04 10:59:07.161
> 
> ./matrix_mul_dpc
> Device:        Intel(R) xxxx(xx) ix-xxxxS CPU @ x.xxGHz
> Problem size: c(150,600) = a(150,300) * b(300,600)
> Result of matrix multiplication using SYCL: Success - The results are correct!
>
> end: 23/05/04 10:59:14.409

## Compile WRFv440 with Intel OneAPI

Ref: [WRF installation on a Linux-based machine]

```sh
source /home/wpsze/intel/oneapi/setvars.sh
export PATH=/home/wpsze/intel/oneapi/compiler/2023.1.0/linux/bin/intel64/:$PATH
export LD_LIBRARY_PATH=/home/wpsze/intel/oneapi/compiler/2023.1.0/linux/lib/intel64/:$LD_LIBRARY_PATH

gcc --version
gfortran --version

icx --version
ifort --version

# Intel (serial or dmpar)
```
If it is sucessful, the following exe will be gnerated,
```sh
==========================================================================
--->                  Executables successfully built                  <---
 main/ndown.exe
 main/real.exe
 main/tc.exe
 main/wrf.exe
==========================================================================
wrf_install/WPS-4.4/geogrid.exe -> geogrid/src/geogrid.exe*
wrf_install/WPS-4.4/ungrib.exe -> ungrib/src/ungrib.exe*
==========================================================================
```

# References

1. [How to install Intel OneAPI Base and HPC Toolkits](https://kb.brightcomputing.com/knowledge-base/how-to-install-intel-oneapi-base-and-hpc-toolkits/#installing-the-intel-oneapi-hpc-toolkit)
2. [Ubuntu20.04 安装 INTEL 编译器和 Gcc 编译器](https://blog.qiql.net/archives/intelgcc)
   1. 先安装Base Toolkit，再安装 HPC Toolkit
   2. 安装gcc5.4编译器
   3. 安装module
   4. 编写 modulefile
3. [Intel oneAPI toolkits 介绍和安装](https://cloud.tencent.com/developer/article/2148549)
   1. 对于气象中的应用，主流数值模式基本都是以Fortran编写的，为了追求更高的计算速度，我们尽量使用的intel编译器
   2. ifort编译的程序运行速度比gfortran要高一些。尽管高的不是非常多，但对于模式长时间的积分，节省的时间还是非常可观的
   3. 对于我们的使用需求，只需安装Intel® oneAPI Base Toolkit 和 Intel® oneAPI HPC Toolkit 就满足了
4. [Install Intel oneAPI | lenovo](https://hpc.lenovo.com/lico/downloads/7.1/Install_Intel_oneAPI.html)
5. [oneAPI资源和安装](https://www.xiaoledeng.com/2021/07/19/oneapi-resource-install/)
6. [Intel学生许可过期后，安装 Intel® oneAPI Base Toolkit 和 Intel® oneAPI HPC来替代](https://zhuanlan.zhihu.com/p/358928837)
   1. 之前的Intel Parallel Studio XE用邮箱申请了学生许可，已经过期，发现不能再续期，官网竟然出了免费版本！
7. [A Historic Moment for The Intel® Fortran Compiler Classic (ifort)](https://community.intel.com/t5/Blogs/Tech-Innovation/Tools/A-Historic-Moment-for-The-Intel-Fortran-Compiler-Classic-ifort/post/1614625)

| oneAPI Package Version |  ifx version | ifort version |
|------------------------|--------------|---------------|
| 2024.0.x               | 2024.0.x     | 2021.11.x     |
| 2024.1.0               | 2024.1.0     | 2021.12.0     |
| 2024.2.0               | 2024.2.0     | 2021.13.0     |
| 2025.0.0 (future)      | 2025.0.0     | not included  |