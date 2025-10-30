---
layout: post
title: Linux | /lib64/libstdc++.so.6 | version `GLIBCXX_3.4.30' not found
categories: [Linux]
tags: [python, libstdc++.so.6, GLIBCXX]
author: wpsze
date: 2025-10-30 06:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/wAUeGjH.png
banner_img: https://i.imgur.com/wAUeGjH.png
---

# `/lib64/libstdc++.so.6`

The error message `/lib64/libstdc++.so.6`: **version 'GLIBCXX_3.4.30' not found** indicates that the program you are trying to run requires a version of the GNU Standard C++ Library (libstdc++) that is newer than the one available on your system or in your current environment. Specifically, it needs `GLIBCXX_3.4.30`, but the `libstdc++.so.6` file being used does not provide this version.

## `libstdc++.so`

`libstdc++.so` refers to the **GNU Standard C++ Library**, which is a crucial component for running C++ applications compiled with the GNU Compiler Collection (GCC).

* **C++ Standard Library Implementation:** `libstdc++` is GCC's implementation of the **ISO C++ Standard Library**. This library provides a massive collection of classes and functions essential for C++ programming.
* **Core C++ Features:** It contains the compiled code for many parts of the C++ Standard Library, including:
    * The **Standard Template Library (STL)** components (like `std::vector`, `std::map`, `std::string`, and various algorithms).
    * **Input/Output streams** (`std::cin`, `std::cout`, etc.).
    * C++ **exception handling** and **Run-Time Type Information (RTTI)** support.
    * Other core language support functions that are not provided as source code in header files.
* **Shared Library:** The `.so` extension stands for **Shared Object** (a dynamic link library on Linux). This means that a C++ program compiled with GCC doesn't include a copy of the library's code inside its executable. Instead, it relies on the dynamic linker to load the `libstdc++.so` file from the system at runtime, saving disk space and memory.

### Versioning

You will commonly see it with a major version number, like **`libstdc++.so.6`**.

* The number (e.g., `6`) signifies the **ABI (Application Binary Interface) version**. A change in this number typically means that programs compiled against an older version of the library might not be able to run with the newer version, and vice-versa, due to fundamental changes in how the code interacts at a binary level.
* Problems often arise when a C++ program requires a specific, newer version of the library (indicated by an internal **`GLIBCXX_x.y.z`** version symbol) that is not present on the system, leading to an error like "version `GLIBCXX_x.y.z` not found."

## libstdc++.so.6

- **What it is**: A **symbolic link** that acts as a pointer to a specific version of the `libstdc++.so` library.
- **Usage**: It provides a stable name that older programs can link against. When the system updates to a newer version of the library (e.g., `libstdc++.so.6.0.35`), the symbolic link `libstdc++.so.6` is updated to point to the new file, ensuring backward compatibility.
- **Example**: If `libstdc++.so.6` is pointing to `libstdc++.so.6.0.34`, any program that needs `libstdc++.so.6` **will find and load the** `libstdc++.so.6.0.34` file. 

### Check

```sh
strings /usr/lib64/libstdc++.so.6|grep GLIBCXX
# or on the conda env
strings /home/wpsze/micromamba/envs/track-tc/lib/libstdc++.so.6 | grep GLIBCXX
```

```console
GLIBC_2.2.5
......
GLIBC_2.22
GLIBC_2.23
GLIBC_2.24
GLIBC_2.25
GLIBC_2.26
GLIBC_2.27
GLIBC_2.28
```

# Solutions

- Solution 1. Link to the system's libstdc++.so.6 (if newer)
   1. `cd /home/wpsze/micromamba/envs/track-tc/lib/`
   2. `ln -s libstdc++.so.6.0.34 libstdc++.so.6`
- Solution 2. `sudo apt update && sudo apt upgrade`
- Solution 3. `conda install -c conda-forge gcc`
   1. `conda install -c conda-forge libstdcxx-ng` 