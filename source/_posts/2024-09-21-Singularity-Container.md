---
layout: post
title: VSCode with Markdown editor
categories: [Linux]
tags: [HPC, NWP]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
---

# What is Singularity

Singularity is a container platform to run complex applications on HPC clusters in a simple, portable, and reproducible way. It supports Docker images and is an alternative to Docker, whose security model and execution mode are not well-suited in an HPC environment.

Major features:

- Verifiable reproducibility and security, using cryptographic signatures, an immutable container image format, and in-memory decryption.
- Integration over isolation by default. Easily make use of GPUs, high speed networks, parallel filesystems on a cluster or server by default.
- Mobility of compute. The single file SIF container format is easy to transport and share.
- A simple, effective security model. You are the same user inside a container as outside, and cannot gain additional privilege on the host system by default.

容器是一種在隔離環境中運行應用程式的技術。與傳統虛擬機器 （VM） 相比，它們具有多項優勢，例如 更快的部署時間和可重複性。容器還允許開發人員建置和部署軟體，無需擔心底層基礎設施或作業系統詳細資訊。在生物資訊分析中，使用Singularity 容器技術具有以下優點：

- 環境一致性和可重複性：專案常常依賴特定版本的軟體和函式庫。 Singularity 容器可以將軟體及其相依性打包在一起，確保分析環境的一致性，無論是在本機、伺服器或雲端平台。這對於實驗的可重複性至關重要，因為它保證了其他研究者可以準確地複製實驗條件，驗證和復現研究結果。
- 高效能運算（HPC）友善：Singularity 特別設計來支援高效能運算環境。**它允許使用者以非特權方式運行容器，與 HPC 系統的安全模型相容。這意味著使用者可以在不需要管理員權限的情況下部署和執行容器，這對於多使用者的HPC環境是非常重要的。**
- 移植性：Singularity 容器可以在任何支援 Singularity 的系統上運行，不受作業系統版本限制。這種高度的移植性確保了生物資訊分析可以輕鬆地從一個運算環境遷移到另一個，無論是從個人電腦到雲端平台，還是在不同的研究機構之間共享。
- 資料和資源存取：Singularity 容器可以直接存取宿主機上的檔案系統和網絡，這簡化了資料的管理和傳輸過程。對於需要處理大量資料的生物資訊分析來說，能夠無縫存取外部資料儲存非常重要。
- 安全性：與其他容器技術相比，Singularity 提供了更強的安全保障。它防止了容器內的使用者取得宿主機的 root 權限，減少了安全風險。這一點對於確保生物資訊學資料的安全性尤其重要，特別是在處理敏感或受保護的資料時。

# Installing Singularity platform

<https://docs.sylabs.io/guides/3.8/user-guide/quick_start.html>

{% note primary %}
**Docker and Singularity server installation requires root privileges**, so you can choose to install Docker on your local personal computer to create an image, and then upload it to the server to run it with Singularity.
{% endnote %}

## install development libraries

SingularityCE is a container platform. It allows you to create and run containers that package up pieces of software in a way that is portable and reproducible. You can build a container using SingularityCE on your laptop, and then run it on many of the largest HPC clusters in the world, local university or company clusters, a single server, in the cloud, or on a workstation down the hall. Your container is a single file, and you don’t have to worry about how to install all the software you need on each different operating system.

SingularityCE的使用不需要管理員權限,但安裝需要管理員權限

```sh
$ sudo apt-get update && sudo apt-get install -y \
    build-essential \
    libssl-dev \
    uuid-dev \
    libgpgme11-dev \
    squashfs-tools \
    libseccomp-dev \
    wget \
    pkg-config \
    git \
    cryptsetup
```

## Install Go

```sh
$ export VERSION=1.16.4 OS=linux ARCH=amd64 && \  # Replace the values as needed
  wget https://dl.google.com/go/go$VERSION.$OS-$ARCH.tar.gz && \ # Downloads the required Go package
  sudo tar -C /usr/local -xzvf go$VERSION.$OS-$ARCH.tar.gz && \ # Extracts the archive
  rm go$VERSION.$OS-$ARCH.tar.gz    # Deletes the ``tar`` file

$ echo 'export PATH=/usr/local/go/bin:$PATH' >> ~/.bashrc && \
  source ~/.bashrc
```
## Download SingularityCE from a release

```sh
$ export VERSION=3.8.1 && # adjust this as necessary \
    wget https://github.com/sylabs/singularity/releases/download/v${VERSION}/singularity-ce-${VERSION}.tar.gz && \
    tar -xzf singularity-ce-${VERSION}.tar.gz && \
    cd singularity-ce-${VERSION}
```

then, Compile

```sh
$ ./mconfig && \
    make -C builddir && \
    sudo make -C builddir install
```

Finally, check

```sh
$ singularity help
```

it has shown,

```
Linux container platform optimized for High Performance Computing (HPC) and
Enterprise Performance Computing (EPC)

Usage:
  singularity [global options...]

Description:
......
```

# Building Singularity images

{% note primary %}
當鏡像庫平台沒有找到適用的現成鏡像，使用者可以在本地將軟體打包成鏡像，上傳到超算上運行。**請注意，使用者只能在自己的電腦上製作鏡像，不能在超算上製作鏡像。**
{% endnote %}

## Example: LAMMPS

LAMMPS 是由桑迪亞國家實驗室開發的一套分子動力學類比的開源程式包。 LAMMPS使用MPI實現多機器平行計算，在新的版本中，支援基於CUDA和OpenCL的GPU計算。其以GNU通用公眾授權條款釋出，因而開源自由。 LAMMPS最初為一美國政府與私人機構合作專案，由美國能源部與另外三所私有企業實驗室合作開發。

LAMMPS is a classical molecular dynamics code with a focus on materials modeling. It's an acronym for Large-scale Atomic/Molecular Massively Parallel Simulator.

From <http://docs.hpc.whu.edu.cn/files/whuhpcdocs.wiki/sbatch/singularity.html>

```
# 创建sandbox；
# 这里将创建的sandbox命名为ubuntu20_lammps,并使用docker hub上现有的容器 ubuntu:20.04 作为基础镜像。
singularity build --sandbox ./ubuntu20_lammps docker://ubuntu:20.04

# 进入创建好的sandbox，并进行修改；
# 其中-w表示可写。进入后singularity会自动挂载的HOME目录，如果是用root用户进入，则会挂载/root目录
singularity shell -w ./ubuntu20_lammps

# Ubuntu下安装LAMMPS并行版需要安装必要的依赖包
apt update && apt upgrade -y
apt install openmpi-bin openmpi-doc libopenmpi-dev -y
apt install gcc g++ gfortran make wget vim -y

# 安装fftw
wget http://www.fftw.org/fftw-3.3.9.tar.gz
tar zxvf fftw-3.3.9.tar.gz
cd fftw-3.3.9
./configure --prefix=/opt/software/fftw_3.3.9 --enable-shared --enable-static --enable-fma
make -j && make install

# 设置临时fftw环境变量
export PATH=/opt/software/fftw_3.3.9/bin:$PATH
export LD_LIBRARY_PATH=/opt/software/fftw_3.3.9/lib:$LD_LIBRARY_PATH

# 安装lammps
wget https://lammps.sandia.gov/tars/lammps-10Feb21.tar.gz
tar zxvf lammps-10Feb21.tar.gz
cd lammps-10Feb21
cd src
vim MAKE/OPTIONS/Makefile.g++_openmpi # 修改如下行
  FFT_INC = -DFFT_FFTW -I/opt/software/fftw_3.3.9/include
  FFT_PATH = -L/opt/software/fftw_3.3.9/lib
  FFT_LIB = -lfftw3

make yes-std
make no-lib
make -j g++_openmpi

mkdir /opt/software/lammps
cp ./lmp_g++_openmpi /opt/software/lammps/

# 设置临时lammps环境变量
export PATH=/opt/software/lammps:$PATH

# 验证(容器内)
cd /root
cp /opt/lammps-10Feb21/bench/in.lj .
mpirun --allow-run-as-root -np 2 --mca btl ^openib lmp_g++_openmpi -in in.lj


# 退出容器，设置永久环境变量(宿主机)
vim ./ubuntu20_lammps/environment
# 加入下面两行
export PATH=/opt/software/fftw_3.3.9/bin:/opt/software/lammps:$PATH
export LD_LIBRARY_PATH=/opt/software/fftw_3.3.9/lib:$LD_LIBRARY_PATH

# 验证(宿主机)
singularity exec ./ubuntu20_lammps mpirun --allow-run-as-root -np 2 --mca btl ^openib lmp_g++_openmpi -in in.lj

# 把修改好的sandbox打包成sif格式；
# 删除不必要的安装包, 如 fftw-3.3.9.tar.gz lammps-10Feb21.tar.gz
# 使用前面创建的sandbox目录生成singularity image file格式镜像。
singularity build ubuntu20_lammps.sif ./ubuntu20_lammps
```

then, run it on HPC,

```sh
#!/bin/bash

#SBATCH --partition=hpxg
#SBATCH --nodes=1
#SBATCH --ntasks-per-node=2
#SBATCH --time=1:00:00

# 加载超算平台预安装的 singularity
module load singularity
# or load by micromamba

# 测试in.lj算例
singularity exec ubuntu20_lammps.sif mpirun -np 2 --mca btl ^openib lmp_g++_openmpi -in in.lj
```

# Running Singularity images


# References

1. [Singularity and Docker](https://docs.sylabs.io/guides/2.6/user-guide/singularity_and_docker.html)
2. [HKU Singularity Container](https://hpc.hku.hk/hpc/software/singularity-container/)
3. [NVIDIA NGC](https://ngc.nvidia.com/)
4. [Docker Hub](https://hub.docker.com/)