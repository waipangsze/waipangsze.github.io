---
layout: post
title: "Intel OneAPI"
categories: [Linux]
tags: [WRF,HPC]
mathjax: true
author: wpsze
---

# Intel OneAPI

The Intel oneAPI HPC Toolkit is a comprehensive suite of development tools that make it fast and easy to build modern code that gets maximum performance out of the newest Intel® processors. This toolkit enables high performance computing on clusters or individual nodes with flexible options including optimal performance on a CPU or GPU.

## Command Line Download
Command Line Installation Parameters

Get latest version <https://www.intel.com/content/www/us/en/developer/tools/oneapi/hpc-toolkit-download.html?operatingsystem=linux&distributions=offline>

```sh
wget https://registrationcenter-download.intel.com/akdlm/IRC_NAS/1ff1b38a-8218-4c53-9956-f0b264de35a4/l_HPCKit_p_2023.1.0.46346_offline.sh

sudo sh ./l_HPCKit_p_2023.1.0.46346_offline.sh
```

### Installation Instructions
The initial download is for the installer application files only. The installer will acquire all the tools during the installation process.

1. Step 1: From the console, locate the downloaded install file.
2. Step 2: Use $ sudo sh ./<installer>.sh to launch the GUI Installer as root.
             Optionally, use $ sh ./<installer>.sh to launch the GUI Installer as current user.
3. Step 3: Follow the instructions in the installer.
4. Step 4: Explore the Get [Started Guide](https://www.intel.com/content/www/us/en/develop/documentation/get-started-with-intel-oneapi-hpc-linux/top.html).

## Get Started with the Intel oneAPI HPC Toolkit for Linux

Although the CMake and pkg-config build tools are not required by the oneAPI tools and toolkits, many oneAPI samples are provided as CMake projects and require CMake to build them. In some cases pkg-config is necessary to locate libraries needed to complete a build of the application.

The Intel compilers utilize the existing GNU build toolchains to provide a complete C/C++ development environment. If your distribution of Linux does not include the complete suite of GNU development tools, you need to install these tools.

To install CMake, pkg-config, and the GNU development tools on your Linux system, open a terminal session and enter the following commands:

```sh
sudo apt update
sudo apt -y install cmake pkg-config build-essential
```

Verify the installation by displaying the installation location with this command:
```sh
which cmake pkg-config make gcc g++
```

In my caes, it is installed in /home/wpsze/intel/oneapi/

> remark #10441: The Intel(R) C++ Compiler Classic (ICC) is deprecated and will be removed from product release in the second half of 2023. The Intel(R) oneAPI DPC++/C++ Compiler (ICX) is the recommended compiler moving forward. Please transition to use this compiler. Use '-diag-disable=10441' to disable this message.

ICC (Linux), ICL (Windows) are classic Intel C/C++ Compilers. Whereas, ICX is Intel nextgen compiler based on Clang /LLVM technology plus Intel proprietary optimizations and code generation.

1. You may use ICC for performance on CPU targets.
2. ICX enables OpenMP TARGET offload to Intel GPU targets.

The following are the guiding principles for ICX:

> 1. ICX and ICC Classic use different compiler drivers. The Intel® C++ Compiler Classic compiler drivers are icc, icpc, and icl.  The Intel® oneAPI DPC++/C++ Compiler drivers are icx and icpx. Use icx to compile and link C programs, and icpx for C++ programs.

> 2. Unlike the icc driver, icx does not use the file extension to determine whether to compile as C or C+. Users must invoke icpx to compile C+ files. . In addition to providing a core C++ Compiler, ICX/ICPX is also used to compile SYCL/DPC++ codes for the Intel® oneAPI Data Parallel C++ Compiler when we pass an additional flag “-fsycl”. 


```sh
$ source /home/wpsze/intel/oneapi/setvars.sh

$ which icc
#/home/wpsze/intel/oneapi/compiler/2023.1.0/linux/bin/intel64/icc
$ which ifort 
#/home/wpsze/intel/oneapi/compiler/2023.1.0/linux/bin/intel64/ifort
$ which icpc
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
```
Remark

1. The icc and icpc commands resemble the GNU equivalents gcc and g++. This is a highly optimizing C (icc) and C++ (icpc) compiler.

## Set Environment Variables for CLI Development

Option 1: Source setvars.sh once per session

Source setvars.sh every time you open a new terminal window:

You can find the setvars.sh script in the root folder of your oneAPI installation, which is typically /opt/intel/oneapi/ for system wide installation and ~/intel/oneapi/ when installed as a private installation. Note that system wide installation requires root or sudo privileges.

For system wide installations:
```sh
source /opt/intel/oneapi/setvars.sh
```

For private installations:
```sh
source ~/intel/oneapi/setvars.sh
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


