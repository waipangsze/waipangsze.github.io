---
layout: article
title: ecFlow -- Installation by Spack
categories: [MPAS]
tags: [ecFlow, Spack, Installation]
author: wpsze
mathjax: true
mathjax_autoNumber: true
---

# Using Spack

Spack is a package management tool designed to support multiple versions and configurations of software on a wide variety of platforms and environments. It was designed for large supercomputing centers, where many users and application teams share common installations of software on clusters with exotic architectures, using libraries that do not have a standard ABI. Spack is non-destructive: installing a new version does not break existing installations, so many configurations can coexist on the same system.
Most importantly, Spack is simple. It offers a simple spec syntax so that users can specify versions and configuration options concisely. Spack is also simple for package authors: package files are written in pure Python, and specs allow package authors to maintain a single file for many different builds of the same package.

<https://spack.readthedocs.io/en/latest/>

## Search packages online

> <https://packages.spack.io/>
> <https://packages.spack.io/package.html?name=ecflow>

# Install 

```sh
$ spack env create ecFlow 
$ spack env activate ecFlow
$ spack info --all ecflow
```

shows,

```sh
 $ spack info --all ecflow
CMakePackage:   ecflow

Description:
    ecFlow is a work flow package that enables users to run a large number
    of programs (with dependencies on each other and on time) in a
    controlled environment. It provides tolerance for hardware and software
    failures, combined with good restart capabilities.

Homepage: https://confluence.ecmwf.int/display/ECFLOW/

Maintainers: @AlexanderRichert-NOAA @climbfuji

Externally Detectable: 
    False

Tags: 
    None

Preferred version:  
    5.11.4    https://confluence.ecmwf.int/download/attachments/8650755/ecFlow-5.11.4-Source.tar.gz

Safe versions:  
    5.11.4    https://confluence.ecmwf.int/download/attachments/8650755/ecFlow-5.11.4-Source.tar.gz
    5.8.4     https://confluence.ecmwf.int/download/attachments/8650755/ecFlow-5.8.4-Source.tar.gz
    5.8.3     https://confluence.ecmwf.int/download/attachments/8650755/ecFlow-5.8.3-Source.tar.gz
    4.13.0    https://confluence.ecmwf.int/download/attachments/8650755/ecFlow-4.13.0-Source.tar.gz
    4.12.0    https://confluence.ecmwf.int/download/attachments/8650755/ecFlow-4.12.0-Source.tar.gz
    4.11.1    https://confluence.ecmwf.int/download/attachments/8650755/ecFlow-4.11.1-Source.tar.gz
```

then, install,

```sh
$ spack install --add ecflow@5.11.4
```

check version,

```sh
$ ecflow_client --version
```

To uninstall a package, type spack uninstall package. This will ask the user for confirmation before completely removing the directory in which the package was installed.

```sh
spack uninstall --dependents ecflow@5.11.4
```

# ecFlow

在该目录中创建以下几个子目录：

def：保存 ecFlow 的工作流定义文件和任务的 ecf 脚本文件

ecfout：ecFlow 服务运行的目录，ecFlow 服务的日志会保存在该目录下

program：保存模式程序、配置文件和脚本

workdir：模式运行的目录

```sh
mkdir -p def
mkdir -p ecfout
mkdir -p program
mkdir -p workdir
```