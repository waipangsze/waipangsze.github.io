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
- set `export LIBRARY_PATH=$LIBRARY_PATH:/EM/wpsze/intel/oneapi/mpi/2021.9.0/lib/release`

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

