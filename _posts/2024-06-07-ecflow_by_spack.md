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

# Error

Not successful. 

- [Installation issue: ecflow@5.11.4 with boost@1.85.0 #44116](https://github.com/spack/spack/issues/44116)
- [Bug fix for ecflow: versions up to 5.11.4 require boost 1.84 or earlier #44181](https://github.com/spack/spack/pull/44181)
- [Merge spack develop as of 2024/05/09 into spack-stack-dev #432](https://github.com/JCSDA/spack/pull/432)

## Try

```sh
 $ spack install --add ecflow%gcc ^boost@1.84.0
==> Concretized ecflow%gcc ^boost@1.84.0
......
==> Installing boost-1.84.0-7awv37536onadsx3urg2sfra7hippfov [29/33]
==> No binary for boost-1.84.0-7awv37536onadsx3urg2sfra7hippfov found: installing from source
==> Using cached archive: //wpsze/spack/var/spack/cache/_source-cache/archive/cc/cc4b893acf645c9d4b698e9a0f08ca8846aa5d6c68275c14c3e7949c24109454.tar.bz2
==> Applied patch //wpsze/spack/var/spack/repos/builtin/packages/boost/python_jam-1_77.patch
==> Applied patch //wpsze/spack/var/spack/repos/builtin/packages/boost/bootstrap-compiler.patch
==> Ran patch() for boost
==> boost: Executing phase: 'install'
==> boost: Successfully installed boost-1.84.0-7awv37536onadsx3urg2sfra7hippfov
  Stage: 29.98s.  Install: 2m 23.30s.  Post-install: 1m 8.91s.  Total: 4m 6.00s
[+] //wpsze/spack/opt/spack/linux-rocky8-haswell/gcc-8.5.0/boost-1.84.0-7awv37536onadsx3urg2sfra7hippfov
[+] //wpsze/spack/opt/spack/linux-rocky8-haswell/gcc-8.5.0/py-pip-23.1.2-7d22lvklf5zxutex5ggyhxp3oxpqhzai
[+] //wpsze/spack/opt/spack/linux-rocky8-haswell/gcc-8.5.0/py-setuptools-69.2.0-axw5axor273y224weubpqloritokktqx
[+] //wpsze/spack/opt/spack/linux-rocky8-haswell/gcc-8.5.0/py-numpy-1.26.4-3vq4eb6gkjjmc3i2pzn4y4pjqscg5bys
==> Installing ecflow-5.11.4-4chvjzr5j33mtu5m3effo7lugzwskirx [33/33]
==> No binary for ecflow-5.11.4-4chvjzr5j33mtu5m3effo7lugzwskirx found: installing from source
==> Using cached archive: //wpsze/spack/var/spack/cache/_source-cache/archive/48/4836a876277c9a65a47a3dc87cae116c3009699f8a25bab4e3afabf160bcf212.tar.gz
==> Applied patch //wpsze/spack/var/spack/repos/builtin/packages/ecflow/ctsapi_cassert.patch
==> ecflow: Executing phase: 'cmake'
==> ecflow: Executing phase: 'build'
==> ecflow: Executing phase: 'install'
==> ecflow: Successfully installed ecflow-5.11.4-4chvjzr5j33mtu5m3effo7lugzwskirx
  Stage: 1.25s.  Cmake: 19.16s.  Build: 3m 47.02s.  Install: 4.49s.  Post-install: 1.60s.  Total: 4m 20.25s
[+] //wpsze/spack/opt/spack/linux-rocky8-haswell/gcc-8.5.0/ecflow-5.11.4-4chvjzr5j33mtu5m3effo7lugzwskirx
==> Updating view at //wpsze/spack/var/spack/environments/ecFlow/.spack-env/view
```

**done** and check,

```sh
 $ ecflow_client --version
Ecflow version(5.11.4) boost(1.84.0) compiler(gcc 8.5.0) protocol(JSON cereal 1.3.0) openssl(enabled) Compiled on Jun 10 2024 00:11:14
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