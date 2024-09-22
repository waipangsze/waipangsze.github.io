---
layout: post
title: Singularity Container
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

From [北京大學高效能運算平台](https://hpc.pku.edu.cn/_book/guide/soft/singularity.html), [北京大學高效能運算校級公共平台使用者文檔](https://hpc.pku.edu.cn/ug/guide/soft/singularity/)

容器技術是一種以應用軟體為中心的虛擬化技術。以應用軟體為單元，將軟體及所有的依賴性打包成容器鏡像，打包後的容器鏡像可直接拷貝到不同的Linux主機上運作。透過容器技術，可以很好的解決安裝軟體時，依賴庫的安裝問題、軟體環境的隔離以及軟體環境的移植問題。

- Singularity為勞倫斯伯克利國家實驗室開發專門用於高效能運算場景的容器技術，Singularity完全基於可移植性進行虛擬化，更加輕量級，部署更快，Singularity目前被廣泛地各高效能運算中心。
- 透過Singularity來滿足作業運行的軟體環境，首先是創建或取得軟體鏡像，再將創建好的軟體鏡像上傳到叢集上運行；
- **透過Singularity建立軟體映像，需要在有root權限的Linux主機上，或是在配置好fakeroot的Linux主機上以「fakeroot」的身份進行。**

使用容器部署和使用軟體環境的步驟如下：
建構容器鏡像，建構容器鏡像的方式有三種：

- 在本機擁有 root 權限的 Linux 主機上安裝 Singularity， 並使用 Singularity 部署容器鏡像；
- 在支援 fakeroot 的伺服器上，使用 Singularity 部署容器鏡像；
- 直接從倉庫中拉取已經建置好的容器鏡像；
- 將容器鏡像上傳到集群，透過 singularity 指令使用建構好的容器鏡像。

容器的優點和缺點(相較於編譯安裝以及 conda 安裝管理軟體，容器有幾個十分明顯的優勢)

容器的優勢

- 可移植性，透過容器部署好運算環境後，可以直接上傳到社會上高效能運算叢集上使用，也可以直接將容器鏡像分享給課題組的其他成員直接使用，大幅減少了部署環境的時間。
- 可複現性，尤其是一些生信軟體，依賴不同的計算工具，換個人安裝，雖然安裝的軟體版本相同，但是依賴的計算工具版本不同，都可能導致計算結果存在差異，而容器的使用可以很好的避免這個問題。
- **易管理，不同容器相互隔絕，不存在依賴衝突等問題，例如平台上使用conda 打包了ncl、nco 和ncview，因為這些工具在安裝的時候同時也打包了netcdf 庫，如果用戶在編譯如cesm 和wrf等依賴netcdf 環境的軟體，可能會導致編譯和使用過程出現衝突。**

容器有著這麼多的優點，效能損耗還低，有時候甚至還優於裸機效能，是否就代表所用運算軟體環境都適合用容器部署？答案是否定的，容器其實也有一些缺點需要使用者留意：

容器的缺點

- 使用難度高，相較於裸機上安裝和使用軟體，容器概念的理解、接受還有使用還是需要一定門檻的
- **跨節點應用由於要呼叫宿主機的 mpi ，相容性有一定的要求，可移植性需要驗證。** 當然，平台也在致力於跨節點應用容器化的測試與打包
- **最佳化問題，例如為了讓軟體運行更快，Intel 編譯軟體過程中可能會使用-Host 參數，讓編譯器根據主機CPU 型號進行指令優化，或者編譯過程中加入AVX512 的支持，這時候如果將鏡像移植到舊款架構的CPU 主機上，可能會導致鏡像無法正常運作軟體環境**

# SIF（Singularity Image File）& Sandbox

兩種建立客製化 image 的方法。Singularity 的 image 支持兩種模式 sif 及 sandbox，兩者最大的差異就是 sandbox 會保留用戶在環境中，安裝的軟體及設定的參數。而 sif 則比較接近唯獨模式，僅提供環境運行所需要的套件，但當運行結束後不會保留在過程中安裝的套件。

- SIF（Singularity Image File）：壓縮的讀（read-only）的Singularity鏡件，是⽣產使⽤的主形式。
- Sandbox 可 (writable)的器在形式，是件統中的⼀⽬錄，常⽤於開發或創建⾃⼰的容器，是開發使⽤的主形式。

{% note primary %}
SIF and sandbox images can be converted to each other.
{% endnote %}

```sh
# 1. Convert the SIF format container to sandbox;
singularity build --sandbox centos76 centos76.sif

# 2. Convert the sandbox container image to SIF format;
singularity build centos76.sif centos76
```

## Singularity Container Image

<https://www.cuhk.edu.hk/itsc/hpc/singularity.html>

{% note primary %}
Note that Singularity versions prior to 3.0 used a slightly different image format, characterised by the extension .simg. You can still find these around in the web; newer Singularity versions are still able to run them.
{% endnote %}

By default, Singularity containers are **read-only image file** that usually end in **.simg**. You can copy the singuarity image file directly from others pre-build container with any copy file command like cp or scp. You can also pull the container from Docker Hub or Sinularity Hub repositories. Base on the container on the repositories, you may custom your own container desire for your job nature.

```sh
#From Docker Hub
singularity pull docker://ubuntu:latest
singularity build centos7_python35.simg docker://centos/python-35-centos7
            
#From Singularity Hub
singularity pull shub://singularityhub/centos
singularity build tensorflow.simg shub://opensciencegrid/osgvo-tensorflow
```

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
$ singularity --version
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

## Example: Build JEDI environment with Singularity

From <https://wiki.ucar.edu/display/JEDI/Build+JEDI+environment+with+Singularity>

### JCSDA-singularity-latest

From <https://singularityhub.github.io/singularityhub-archive/containers/JCSDA-singularity-latest/>

From <https://singularityhub.github.io/singularityhub-archive/containers/MBlaschek-singularity-jupyter-jedi/>

```sh
$ singularity --version

$ singularity pull shub://MBlaschek/singularity-jupyter:jedi
$ ll
963M Sep 22 14:10 singularity-jupyter_jedi.sif

$ singularity exec singularity-jupyter_jedi.sif cat /etc/os-release
NAME="Ubuntu"
VERSION="16.04.6 LTS (Xenial Xerus)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 16.04.6 LTS"
VERSION_ID="16.04"
HOME_URL="http://www.ubuntu.com/"
SUPPORT_URL="http://help.ubuntu.com/"
BUG_REPORT_URL="http://bugs.launchpad.net/ubuntu/"
VERSION_CODENAME=xenial
UBUNTU_CODENAME=xenial

$ singularity inspect singularity-jupyter_jedi.sif
MAINTAINER: Mark Miesch
SPECIES: JEDI
org.label-schema.build-date: Thursday_5_December_2019_10:5:35_UTC
org.label-schema.schema-version: 1.0
org.label-schema.usage.singularity.deffile.bootstrap: docker
org.label-schema.usage.singularity.deffile.from: jcsda/docker_base-gnu-openmpi-dev:latest
org.label-schema.usage.singularity.version: 3.4.2

$ singularity shell -e singularity-jupyter_jedi.sif
```

What's inside the JEDI docker image?
(note: seems not full set)

```
When you start a Singularity container instance from the xinzhang8noaa-singularity-master.simg, we already prepapre the gnu version 7.2 compilers and most of the necessary libraiestools for GSI, OOPS, WRF etc., all libraries are installed under /usr/local, which include

git
git flow
emacs
open-mpi v2.1.0
zlib v1.2.11
szip v2.1.1
jpeg v9b
png v1.4.19
jasper v1.900.2
hdf5 v1.8.17
freetype v2.5.5
netcdf-c v4.4.11
netcdf-fortran v4.4.4
lapack v3.7.0
parallel-netcdf v1.8.1
xerces-c v3.1.4
esmf v7.0.0
udunites-2 v2.2.24
nco v4.6.6
grib_api v1.21.0
cdo v1.8.2
boost v1.65.1
eigen3 v3.3.4
pio 1.7.1
ecbuild
eckit
fckit

The major NCEP libraries are also installed at :

/nwprod/lib/bacio/v2.0.1/libbacio_v2.0.1_4.a
/nwprod/lib/bacio/v2.0.1/libbacio_v2.0.1_8.a
/nwprod/lib/ip/v2.0.0/libip_v2.0.0_4.a
/nwprod/lib/ip/v2.0.0/libip_v2.0.0_8.a
/nwprod/lib/ip/v2.0.0/libip_v2.0.0_d.a
/nwprod/lib/sigio/v2.0.1/lib/libsigio_v2.0.1_4.a
/nwprod/lib/sigio/v2.0.1/libsigio_v2.0.1_4.a
/nwprod/lib/sp/v2.0.2/libsp_v2.0.2_4.a
/nwprod/lib/sp/v2.0.2/libsp_v2.0.2_8.a
/nwprod/lib/sp/v2.0.2/libsp_v2.0.2_d.a
/nwprod/lib/w3emc/v2.2.0/libw3emc_v2.2.0_4.a
/nwprod/lib/w3emc/v2.2.0/libw3emc_v2.2.0_8.a
/nwprod/lib/w3emc/v2.2.0/libw3emc_v2.2.0_d.a
/nwprod/lib/w3nco/v2.0.6/libw3nco_v2.0.6_4.a
/nwprod/lib/w3nco/v2.0.6/libw3nco_v2.0.6_8.a
/nwprod/lib/w3nco/v2.0.6/libw3nco_v2.0.6_d.a
```

- Download WRF and WPS 
- Build WRF and WPS code
- Download MPAS code
- Build MPAS code
- Test MPAS

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

## Customize .def

```sh
Bootstrap: library
From: debian:7
```

# Conda install singularity

```sh
$ micromamba env create -n singularity
$ micromamba activate singularity
$ micromamba install conda-forge::singularity
```

version =  linux-ppc64le/singularity-3.8.7-hff88825_0.tar.bz2?

# Running Singularity images

## build or pull

```sh
$ singularity build ./jason-tensorflow.sif docker://tensorflow/tensorflow:latest-gpu
$ singularity pull ./jason-tensorflow.sif docker://tensorflow/tensorflow:latestgpu
$ singularity pull library://lolcow
$ ls
lolcow_latest.sif
```

{% note primary %}
- Inspect will show you labels, environment variables, and scripts associated with the image determined by the flags you pass.
{% endnote %}

```sh
$ singularity inspect my-container.sif
```

## run, exec, shell

{% note primary %}
singularity run 
Run the user-defined default command within a container
{% endnote %}

```sh
$ singularity run my-container.sif

$ singularity run lolcow_latest.sif 
 ______________________________
< Sun Sep 22 00:17:43 HKT 2024 >
 ------------------------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```

{% note primary %}
singularity exec
Run a command within a container
{% endnote %}

```sh
$ singularity exec my-container.sif my-command

$ singularity exec lolcow_latest.sif cowsay WRF-MPAS
 __________
< WRF-MPAS >
 ----------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
```

{% note primary %}
singularity shell
Run a shell within a container
{% endnote %}

```sh
$ singularity shell my-container.sif

$ singularity shell lolcow_latest.sif 
Singularity> cowsay here is shell
 _______________
< here is shell >
 ---------------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
Singularity> cowsay going 
 _______
< going >
 -------
        \   ^__^
         \  (oo)\_______
            (__)\       )\/\
                ||----w |
                ||     ||
Singularity> exit
exit
```

# Specifying bind paths

開發容器的目的之一主要是為了解決依賴函式庫的安裝、軟體環境的隔離、以及軟體環境的移植問題。因此，**容器的核心特性之一就是它們的檔案系統與宿主機相隔離。** 如果我們查看一個 singularity 容器的根目錄，也會看到與 Linux 主機根目錄相似的結構。這意味著容器內的應用程式只能看到（並與之互動）這個封閉環境中的檔案和目錄。與 Linux 下掛載（mount）外接硬碟設備類似，容器同樣需要透過掛載 (bind) 的操作與我們宿主機上的檔案系統互動。[link](https://juejin.cn/post/7345293582365868041)

當呼叫容器時，Singularity 會將主機作業系統 “swap” 為容器中的作業系統，導致我們無法存取主機檔案系統。**然而，在日常使用場景中，已安裝至容器中的應用程式的輸入和輸出檔案往往儲存在主機檔案系統中，因此，我們需要從容器內讀取和寫入主機系統上的檔案。**

- singularity 使用 --bind/-B 宿主機目錄:容器內目錄 選項將主機系統上的目錄對應到容器內的目錄。這允許我們從容器內部存取主機上的文件，從而在主機系統上讀寫資料。

如需要將多個目錄綁定到 Singularity 容器中，並且長期保持不變，我們可以進一步將這個變數寫入 .bashrc 檔案中。

Here’s an example of using the --bind option and binding /data on the host to /mnt in the container (/mnt does not need to already exist in the container):

```sh
$ ls /data
bar  foo

$ singularity exec --bind /data:/mnt my_container.sif ls /mnt
bar  foo
```

You can bind multiple directories in a single command with this syntax:

```sh
$ singularity shell --bind /opt,/data:/mnt my_container.sif
```

or 

```sh
$ export SINGULARITY_BIND="/opt,/data:/mnt"

$ singularity shell my_container.sif
```

# References

1. [Singularity and Docker](https://docs.sylabs.io/guides/2.6/user-guide/singularity_and_docker.html)
2. [HKU Singularity Container](https://hpc.hku.hk/hpc/software/singularity-container/)
3. [Sylabs Cloud](https://cloud.sylabs.io/library)
   1. singularity pull ubuntu.sif library://library/default/ubuntu:21.04
4. [NVIDIA NGC](https://ngc.nvidia.com/)
   1. NVIDIA官方自己构建的pytorch和TensorFlow的容器镜像，每个里面包含了cuda、显卡驱动以及cudnn，据说比自己装的速度要快，使用时singularity需要 --nv 选项。
5. [Docker Hub](https://hub.docker.com/)
   1. singularity pull ubuntu.sif docker://ubuntu:latest
6. [Singularity Hub Archive](https://singularityhub.github.io/singularityhub-archive/)
7. [从零开始制作PyTorch的Singularity容器镜像](https://www.cnblogs.com/dechinphy/p/pytorch.html)
8. [建立台灣杉二號的容器](https://man.twcc.ai/@twccdocs/howto-twnia2-create-sglrt-container-zh)