---
layout: post
title: Intel Instruction Set Extensions Technology 指令集擴充技術
categories: [Intel]
tags: [Intel]
author: wpsze
mathjax: true
mathjax_autoNumber: true
---

# Intel Instruction Set Extensions Technology

[Intel® Instruction Set Extensions (指令集擴充技術)](https://www.intel.com.tw/content/www/tw/zh/support/articles/000005779/processors.html) are additional instructions that **can increase performance** when the same operations are performed on multiple data objects.

Instruction Set Extensions are additional instructions which can increase performance when the same operations are performed on multiple data objects. These can include **SSE (Streaming SIMD Extensions) and AVX (Advanced Vector Extensions).**

Detailed instructions are listed in Intel® Architecture Instruction Set Extensions Programming Reference.

Instruction Set Extensions can include:
- Single Instruction Multiple Data (SIMD)
- Intel® Streaming SIMD Extensions (Intel® SSE,Intel® SSE2,Intel® SSE3, and Intel® SSE4)
- Intel® Advanced Vector Extensions (Intel® AVX, Intel® AVX2, and Intel® AVX-512)

## Identify My Intel Processor

Linux
Type the following command:
```sh
$ lscpu | grep "Model name"
Model name:                           Intel(R) Core(TM) i5-3470S CPU @ 2.90GHz
```

## Product Specifications

- Go to https://ark.intel.com/content/www/us/en/ark.html#@Processors
- enter your CPU name
  - like https://ark.intel.com/content/www/us/en/ark/products/68315/intel-core-i5-3470s-processor-6m-cache-up-to-3-60-ghz.html
  - It has shown **Instruction Set Extensions = Intel® SSE4.1, Intel® SSE4.2, Intel® AVX**

## Streaming SIMD Extensions (SSE)
SSE is a process or technology that enables single instruction multiple data. Older processors only process a single data element per instruction. SSE enables the instruction to handle multiple data elements. It's used in intensive applications, such as 3D graphics, for faster processing. SSE is designed to replace MMX™ Technology. It expanded over the generations of Intel® Processors to include SSE2, SSE3/SSE3S, and SSE4. Each iteration has brought new instructions and increased performance.

## Intel Advanced Vector Extensions (Intel® AVX and AVX2)
**Intel® AVX is a 256-bit instruction set extension to Intel® SSE** designed for applications that are Floating Point (FP) intensive. Intel AVX improves performance due to wider vectors, new extensible syntax, and rich functionality. ***Intel AVX2 was released in 2013***, extending vector processing capability across floating-point and integer data domains. This results in higher performance and more efficient data management across a wide range of applications. Examples are image and audio/video processing, scientific simulations, financial analytics, and 3D modeling and analysis.

## Intel® Advanced Vector Extensions 512 (Intel® AVX-512)
The Intel® AVX-512 enables processing of twice the number of data elements that Intel AVX/AVX2 can process with a single instruction and four times the capabilities of Intel SSE. Intel AVX-512 instructions are important because they open up higher performance capabilities for the most demanding computational tasks. Intel AVX-512 instructions offer the highest degree of compiler support in the design of the instruction capabilities.

## SPEC Flag Description for the GNU C/C++/Fortran Compiler

Go to https://www.spec.org/omp2012/flags/gcc-linux64.html

Optimization Flags
- -Ofast
- -fopenmp
- -fopenmp-simd
- -m64
- -mavx512f
- -mavx512cd
- -mavx512er
- -mavx512pf
- -march=ivybridge
- -march=native

