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
INFO:    Could not find any nv files on this host!
WARNING: Could not find any nv libraries on this host!
WARNING: You may need to manually edit /home/wpsze/micromamba/envs/singularity/etc/singularity/nvliblist.conf
INFO:    Converting SIF file to temporary sandbox...
WARNING: Skipping mount /etc/localtime [binds]: /etc/localtime doesn't exist in container
WARNING: By using --writable, Singularity can't create /HOME destination automatically without overlay or underlay
INFO:    Cleaning up image...
FATAL:   container creation failed: mount /home/wpsze/micromamba/envs/singularity/var/singularity/mnt/session/HOME->/HOME error: while mounting /home/wpsze/micromamba/envs/singularity/var/singularity/mnt/session/HOME: destination /HOME doesn't exist in container
```

- [Running a Singularity Container Image on Pleiades | NASA](https://www.nas.nasa.gov/hecc/support/kb/running-a-singularity-container-image-on-pleiades_638.html)
  - `WARNING: By using --writable, Singularity can't create /homeX destination ......`
  - **Note**: If you encounter the following warning message, `cd` to `your_image_sandbox` and use the `mkdir` command to create `homeX` before you retry the commands above. (The X in `homeX` represents the actual value (X=1-7) of your $HOME directory on Pleiades.)

## Can Modulus run on CPU? 

- Modulus is built on top of PyTorch, which supports both CPU and GPU execution. By default, Modulus will attempt to use a GPU if available, but it can fall back to CPU execution if no GPU is detected or if explicitly configured.
- Running on a CPU is feasible for smaller models or prototyping, but large-scale training or complex simulations may be impractical due to performance constraints.

By default, the Modulus container is optimized for GPU use and assumes CUDA availability. To run it on CPU:

- Avoid passing GPU resources to Singularity (e.g., no `--nv` flag).
- Launch the container interactively or with a script.

```
$ singularity run Modulus.sif
```

and, **Importantly**, add on `conf/config.ymal

```yml
device: cpu
cuda_graphs: False
```

like, on `three_fin_2d/heat_sink` case,

-  $ cat /home/wpsze/ML/NVIDIA-Modulus/modulus-sym/examples/three_fin_2d/conf/config.yaml
  
```yml 
# SPDX-FileCopyrightText: Copyright (c) 2023 - 2024 NVIDIA CORPORATION & AFFILIATES.
# SPDX-FileCopyrightText: All rights reserved.
# SPDX-License-Identifier: Apache-2.0
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

defaults :
  - modulus_default
  - arch:
      - fully_connected
  - optimizer : adam
  - scheduler : tf_exponential_lr
  - loss : sum
  - _self_

jit: false

device: cpu
cuda_graphs: False

scheduler:
  decay_rate: 0.95
  decay_steps: 5000

training: 
  rec_validation_freq: 1000
  rec_inference_freq: 1000
  rec_monitor_freq: 1000
  rec_constraint_freq: 2000
  max_steps: 500000

batch_size:
  inlet: 64
  outlet: 64
  hs_wall: 500
  channel_wall: 2500
  interior_flow: 4800
  interior_heat: 4800
  integral_continuity: 128
  num_integral_continuity: 4
```

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
2. [AI FOR SCIENCE FRAMEWORK: MODULUS](https://indico.ihep.ac.cn/event/17357/sessions/3811/attachments/62339/72133/AI%20For%20Science%20Framework%20modulus.pdf)