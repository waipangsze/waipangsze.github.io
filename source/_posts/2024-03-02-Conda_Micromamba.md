---
layout: post
title: Conda/Micromamba
categories: [Linux]
tags: [conda, Micromamba]
author: wpsze
index_img: 
banner_img: 
---

The easiest way to export a .yml file from a conda environment is to first activate your environment by running:
```sh
conda activate myvenv
```
And then run the following command that generated your .yml file:
```sh
conda env export > list.yml

## win-64 and linux-64 have different label on version/number
## Export Conda Environment with minimized requirements
micromamba env export --from-history > list.yml

## like
name: uxarray
channels:
- anaconda
- conda-forge
dependencies:
- conda-forge::basemap
- conda-forge::cartopy
- anaconda::ipykernel
- conda-forge::netcdf4
- scipy
- conda-forge::uxarray==2024.11.1
- conda-forge::wrf-python
```
Finally, you can create a new environment using the .yml file, like:
```sh
conda env create --name myvenv --file=list.yml
```

or 
```sh
conda env export | grep -v "^prefix: " > environment.yml
conda env create -f environment.yml
```

Remove environment,
```sh
conda remove -n ENV_NAME --all
```

# Micromamba
Micromamba is a standalone version of Mamba which is an alternative to Conda. This stack delivers a custom anaconda/miniconda type deployment based on the communities conda-forge channel with micromamba as the package manager. For more information visit [micromamba documentation](https://mamba.readthedocs.io/en/latest/user_guide/micromamba.html).

micromamba 是 mamba-org 基於 mamba 所構建的小型 docker 鏡像， mamba 是建立在 conda 上的升級版包管理工具，最主要的性能優化是可以多線程運行 conda，可以同時在選定的所有 conda channel 搜尋並下載安裝包，解決了 conda 下載緩慢的問題。

## Installation
```sh
wget micro.mamba.pm/install.sh
chmod +x install.sh
./install.sh
```

```sh
## Parsing arguments
if [ -t 0 ] ; then
  printf "Micromamba binary folder? [~/.local/bin] "
  read BIN_FOLDER
  printf "Init shell ($shell)? [Y/n] "
  read INIT_YES
  printf "Configure conda-forge? [Y/n] "
  read CONDA_FORGE_YES
fi
```

- /home/wpsze/.local/bin
- init shell? Y
- configure-forge? Y

Finally, it will initalize micromambs config on .bashrc

### Export the contents of the existing environment to a YAML file.

```sh
micromamba env export -n oldenv > oldenv.yaml
```

### Create env by given yaml

```sh
micromamba env create --name newenv --file oldenv.yaml
```

### update: Update packages in active environment

- **in active environment**
  
```sh
micromamba update --file new.yaml
```

### pip requirements file contain "@file"

- [Why does the pip requirements file contain @file instead of version number?](https://stackoverflow.com/questions/62586878/why-does-the-pip-requirements-file-contain-file-instead-of-version-number)
- <https://peps.python.org/pep-0440/#direct-references>
- 

### Source and activate env
```sh
$ source /home/wpsze/micromamba/etc/profile.d/micromamba.sh
$ micromamba activate venv

## For everyone to activate my env
$ micromamba activate /home/wpsze/micromamba/envs/mpich
```

#### crontab -e won't work

- `cd /home/wpsze/micromamba/bin`  
- create `activate` 

```sh
#!/bin/bash

export MAMBA_EXE="/home/wpsze/.local/bin/micromamba"
export MAMBA_ROOT_PREFIX="/home/wpsze/micromamba"

source /home/wpsze/micromamba/etc/profile.d/micromamba.sh

#eval "$("/home/wpsze/micromamba/bin/micromamba" shell hook -s bash)"
#eval "$(micromamba shell hook --shell=bash)"

micromamba activate "$@"
```

- `source /home/wpsze/micromamba/bin/activate venv` 

### activate or execute
Transaction finished

To activate this environment, use:

    micromamba activate venv

Or to execute a single command in this environment, use:

    micromamba run -n venv mycommand

## clone an environment,
As stated in the answer to [Cloning environment with micromamba](https://stackoverflow.com/questions/76884520/cloning-environment-with-micromamba), **--clone** is not an option in micromamba.

Instead, export the environment yaml and create a new environment using the --file flag. Then remove the old environment.

1. export the env dependencies: micromamba env export -n oldenv > oldenv.yaml
2. create a new env with a new name: micromamba create -n newenv --file oldenv.yaml
3. remove the old env: micromamba env remove -n oldenv
4. delete the yaml file: rm oldenv.yaml

## check/add channel

```sh
$ micromamba info

# or

$ micromamba config list --sources

channels:
  - conda-forge  # '~/.condarc'

$ micromamba config append channels conda-forge
```

##  Cloning an environment

```console
micromamba env create --name my_clone --clone new_venv
```

# conda的環境遷移 (打包conda環境)

當我們使用另外的Linux系統時，重新搭建環境未免過於耗時，因此可以將conda環境整體打包並在新機器上部署

- 安裝打包conda環境的軟體：

```console
conda install -c conda-forge conda-pack
```

- 使用conda-pack打包環境：

```console
conda pack -n 虛擬環境名稱 -o output.tar.gz
```

- 將output.tar.gz轉移到需要建造的新機器的對應Miniconda/envs的路徑下
  - 解壓縮

```console
tar -xzvf output.tar.gz
```

- 查看目前環境,出現之前環境的名稱

```console
conda env list
```

# To access the remote machine with a browser

To access the remote machine with a browser the notebook must listen on an external facing port **(not localhost)**. You will need the same invocation if want to run the Jupyter notebook on a container. In that case it is something like this:

```console
jupyter-notebook --no-browser --port 11013 --ip=0.0.0.0
```

To listen **only in localhost** then you can omit the IP

```console
jupyter notebook --no-browser --port=8080
```

# Why does creating my conda environment use so much memory?

I am using conda (installed using Miniconda3) and I want to create a Python 3.7 from an environment file (using `conda env create -f environment.yaml`). While conda solves the environment, the memory usage goes up by about 4 GB for a few minutes until the environment is succesfully installed. Is it possible to reduce this memory usage?

Some discussion here:

- <https://github.com/conda/conda/issues/5003>

I think someone mentioned this:

- **For each channel, conda loads the full repodata information into memory**. When you uncompress it, it's a lot. In the future we'd like to get to a place where that's not necessary. But it's going to take quite a bit of work to get there.

# References

1. [北京大学高性能计算校级公共平台用户文档](https://hpc.pku.edu.cn/ug/guide/soft/conda/#_3)
2. [Python | 基于Linux系统下conda环境的部署及其迁移](https://mp.weixin.qq.com/s/SfYOaafgyUmQvCvytMrIWQ)