---
layout: post
title: ML | NVIDIA Modulus
categories: [ML]
tags: [MPAS, NWP, WRF, ML, PINN, DeepONet, AI]
author: wpsze
date: 2025-02-21 17:57:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: 
banner_img: 
---

# NVIDIA Modulus 

Modulus is an open source deep-learning framework for building, training, and fine-tuning deep learning models using state-of-the-art **Physics-ML methods**.

Whether you are exploring the use of Neural operators like **Fourier Neural Operators** or interested in **Physics informed Neural Networks** or a hybrid approach in between, Modulus provides you with the optimized stack that will enable you to train your models at real world scale.

Modulus code is open source and packaged in a modular fashion into two repos - **Modulus Core** and **Modulus Sym**.

- **Modulus Core** provides a pytorch like experience for those proficient with python for AI. It includes all the core algorithms, network architectures and utilities to cover the broad spectrum of physics-constrained and data-driven workflows to suit the diversity of use cases in the science and engineering disciplines.
- **Modulus Symbolic (Modulus Sym)** provides pythonic APIs, algorithms and utilities to be used with Modulus core, to explicitly physics inform the model training. This includes **symbolic APIs for PDEs, domain sampling and PDE-based residuals**. It also provides higher level abstraction to compose a training loop from specification of the **geometry, PDEs and constraints like boundary conditions using simple symbolic APIs**.

We also package the entire Modulus source in a single container image to simplify the ease of use and is freely available on NGC. The container does not include the reference applications due to their size and we recommend you to download the examples directly from Github source.

# Modulus with Docker Image - Singularity

Once you have installed `Singularity`, to build and **run Modulus Docker image** with `Singularity`:

```console
singularity build Modulus.sif docker://nvcr.io/nvidia/modulus/modulus:<tag>
singularity run --writable --nv Modulus.sif
```

- <https://github.com/NVIDIA/modulus/releases>
- **tag** 
  - <https://catalog.ngc.nvidia.com/orgs/nvidia/teams/modulus/containers/modulus/tags>
  - up to 24.12 (2025-02-21)
  - consider `singularity build Modulus.sif docker://nvcr.io/nvidia/modulus/modulus:23.08` 
    - 10/04/2023 2:35 AM, 11.24 GB, 1 Architecture
    - `9.5G Feb 21 17:19 Modulus.sif`

But,

```
 $ singularity run --writable --nv Modulus.sif
WARNING: Could not find any Nvidia libraries on this host!
/usr/bin/which: no nvidia-smi in (/bin:/usr/bin:/usr/local/bin:/sbin:/usr/sbin:/usr/local/sbin)
WARNING: Could not find the Nvidia SMI binary to bind into container
ERROR  : Unable to open squashfs image in read-write mode: Read-only file system
ABORT  : Retval = 255
```

- <https://github.com/sylabs/singularity/issues/776>

## Running Examples

```console
223M	./modulus
9.5G	./Modulus.sif
926M	./modulus-sym
```

### Modulus Examples

Clone the Modulus repository in your working directory using:

```console
git clone https://github.com/NVIDIA/modulus.git
```

To verify the examples run correctly, run these commands:

```console
cd ./modulus/examples/cfd/darcy_fno/
pip install warp-lang
python train_fno_darcy.py
```

If you see the `outputs/` directory created after the execution of the command (~5 min), the installation is successful.

### Modulus Sym examples

- The modulus-sym repository has **Git LFS** enabled. You will need to have **Git LFS** installed for the clone to work correctly.

Clone the Modulus Sym repository in your working directory using:

```
git clone https://github.com/NVIDIA/modulus-sym.git
```

To verify the examples run correctly, run these commands:

```
cd ./modulus-sym/examples/helmholtz/
python helmholtz.py
```

If you see the `outputs/` directory created after the execution of the command (~5 min), the installation is successful.


# References

1. [NVIDIA Modulus Getting Started](https://docs.nvidia.com/deeplearning/modulus/getting-started/index.html)