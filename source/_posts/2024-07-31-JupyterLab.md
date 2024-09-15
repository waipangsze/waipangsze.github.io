---
layout: post
title: JupyterLab
categories: [Jupyter]
tags: [Jupyter, python]
author: wpsze
mathjax: true
mathjax_autoNumber: true
---

# JupyterLab

JupyterLab is the latest web-based interactive development environment for notebooks, code, and data. Its flexible interface allows users to configure and arrange workflows in data science, scientific computing, computational journalism, and machine learning. A modular design invites extensions to expand and enrich functionality.

## Install

```sh
micromamba activate venv 
micromamba install python=3.11
micromamba install -c conda-forge jupyterlab
micromamba install anaconda::ipykernel
micromamba install nodejs
micromamba install -c conda-forge jupyter-resource-usage
micromamba install -c conda-forge jupyterlab_execute_time
```

## Execute 
```sh
$ jupyter-lab
```

## Added other kernels to Jupyter
```sh

micromamba activate new-env-1

# install python kernel for Jupyter
# avoid installation of any jupyter related modules
micromamba install anaconda::ipykernel

# added the kernel to Jupyter and name it as "Environment(myproject)"
python -m ipykernel install --user --name=mnew-env-1 --display-name='Environment(new-env-1)'

micromamba deactivate

micromamba acitvate new-env-2

# install python kernel for Jupyter
# avoid installation of any jupyter related modules
micromamba install anaconda::ipykernel

# added the kernel to Jupyter and name it as "Environment(myproject)"
python -m ipykernel install --user --name=mnew-env-2 --display-name='Environment(new-env-2)'
```

## Monitor resource usage with GPU Dashboard in Jupyter
A GPU dashboard is available for JupyterLab for showing the resource usage of GPU in real time, side by side with cells in your notebook. It is particularly useful in identifying any bottleneck or issues during code development.

You may activate your conda or python virtual environment for your kernel and run the following once to install the GPU dashboard.

```sh
pip install jupyterlab-nvdashboard
```

# References
- [HKU HPC](https://hpc.hku.hk/hpc/software/jupyterlab/)
- [jupyter-resource-usage](https://github.com/jupyter-server/jupyter-resource-usage)
- [List of extensions and tools](https://jupyterlab-contrib.github.io/extensions.html)
- [Voyager is a JupyterLab MIME renderer extension to view CSV and JSON data in Voyager 2](https://github.com/altair-viz/jupyterlab_voyager)
- [jupyterlab/debugger](https://github.com/jupyterlab/debugger)
- [JupyterLab extension](https://jupyterlab.readthedocs.io/en/stable/user/extensions.html#id9)


