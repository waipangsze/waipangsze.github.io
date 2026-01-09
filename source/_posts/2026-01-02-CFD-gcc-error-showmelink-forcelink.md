---
layout: post
title: "CFD | gcc : error: unrecognized command line option ‘--showme:link’; did you mean ‘--force-link’?"
categories: [CFD]
tags: [OpenFOAM, HPC, error, MPI, intelmpi, openmpi]
author: wpsze
date: 2026-01-02 06:08:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/AquZ91E.png
banner_img: https://i.imgur.com/AquZ91E.png
---

# Error

## Error 1

- **gcc: error: unrecognized command line option ‘--showme:link’; did you mean ‘--force-link’?**

The error message you are seeing, gcc: error: unrecognized command line option ‘--showme:link’; did you mean ‘--force-link’?, indicates that the GCC compiler does not have an option with that specific name. 


> vim OpenFOAM-13/etc/bashrc

- `export WM_MPLIB=INTELMPI #SYSTEMOPENMPI`

## Error 2 

- **/usr/bin/ld: cannot find -lmpi**

> gcc  -m64 -fPIC  -m64 ... /openfoam13/ThirdParty-13/Zoltan-3.90/build/src -lzoltan -lmpi  -lm

- under `mpi/intel64/lib/release` path
- set `export LIBRARY_PATH=$LIBRARY_PATH:/home/wpsze/intel/oneapi/mpi/2021.9.0/lib/release`

## Error 3

- **/bin/sh: flex: command not found**
- **gcc: error: lex.yy.c: No such file or directory**

> The error message "flex: command not found" indicates that the flex program is not installed on your system or is not in your shell's search path. 
> **flex (Fast Lexical Analyzer)** is a tool commonly used during the compilation of other software, such as the Linux kernel, to generate lexical analyzers. 

- `conda install conda-forge::flex`
- For source env,
	- **source intel first, and then conda env**
		- `source /home/wpsze/intel/oneapi/setvars.sh intel64`
		- `source /home/wpsze/micromamba/bin/activate cfd_env`

## Error 4

- `undefined reference to '__gmpq_init' / '__gmpq_set'/ '__gmpn_copyi'`
  - ```log
	1858 collect2: error: ld returned 1 exit status
	1859 make[2]: *** [/home/wpsze/openfoam/OpenFOAM-v2412/OpenFOAM-v2412/wmake/makefiles/general:174: /home/wpsze/openfoam/OpenFOAM-v2412/OpenFOAM-v2412/platforms/linux64GccDP     Int32Opt/bin/surfaceBooleanFeatures] Error 1
	1860 make[1]: *** [/home/wpsze/openfoam/OpenFOAM-v2412/OpenFOAM-v2412/wmake/makefiles/apps:28: surfaceBooleanFeatures] Error 2
	1861 make: *** [/home/wpsze/openfoam/OpenFOAM-v2412/OpenFOAM-v2412/wmake/makefiles/apps:28: surface] Error 2
	1862 make: *** Waiting for unfinished jobs....
	```
> The error "undefined reference to __gmpq_init'" is a **linker error** in a **C/C++ OpenFOAM** project, indicating that the linker cannot find the implementation of the **`__gmpq_init`** function. This function belongs to the **GNU Multiple Precision Arithmetic Library (GMP)**, which is often used as a dependency for other libraries like **CGAL** (CGAL（Computational Geometry Algorithms Library）是一個開源的 C++ 程式庫，提供高效且可靠的計算幾何演算法，涵蓋了多邊形、凸包、三角剖分、網格生成等各種幾何資料結構與演算法，廣泛應用於電腦輔助設計、地理資訊系統、電腦圖形、醫學影像、機器人學等領域，並採用雙授權模式 (LGPL/GPL 適用開源，商用需付費)。). 

### disucss

 - [ThirdParty clang without gmp/mpfr](https://develop.openfoam.com/Development/openfoam/blob/develop/doc/BuildIssues.md#thirdparty-clang-without-gmpmpfr)
   - If using ThirdParty `clang` without `gmp/mpfr`, the ThirdParty `makeCGAL` script will need to be run manually and specify that there is no `gmp/mpfr`.
   - Subequent compilation with `Allwmake` will now run largely without any problems, except that the components linking against `CGAL` (**foamyMesh and surfaceBooleanFeatures**) will also try to link against a nonexistent `mpfr` library. As a workaround, the link-dependency will be removed in `wmake/rules/General/cgal` by specifying the `CGAL_FLAVOUR`	when compiling:
     - > `no-cgal | cgal-header | cgal-header-no-mpfr | cgal-no-mpfr | cgal-mpfr`
   - A robuster solution is still being sought.

### Common Solutions

- **Explicitly link the library**: Ensure that your project's `Make/options` file (if you are compiling within **OpenFOAM**'s wmake environment) or your compiler command includes the linker flag for **GMP**.
  - Add `-lgmp` to the linker flags (e.g., `LIBS` variable in the `options` file). The order matters, and dependent libraries should typically appear after the libraries that use them in the linker command.
  - e.g. `OpenFOAM-v2412/wmake/makefiles/general`
    - `EXE_LIBS        = -lgmp`, seems not works.
- **Verify the library path**: If **GMP** is installed in a non-standard location (e.g., in the **OpenFOAM** **ThirdParty** directory or a custom path), you need to specify the library directory using the `-L` flag and the include directory using the `-I` flag in your `Make/options` file.
  - `LDFLAGS = -L$(GMP_LIB_PATH)`
  - `LIBS = -lgmp ...`
- **Check compatibility**: Ensure that the architecture (32-bit vs. 64-bit) of your compiled code matches that of the GMP library you are linking against.
- **Clean and rebuild**: After making changes to your installation or `options` files, perform a clean build to ensure all object files and dependencies are correctly re-linked. Use `wclean` within your **OpenFOAM** project directory before running `wmake` again. 
