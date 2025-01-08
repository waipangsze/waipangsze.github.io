---
layout: post
title: ML | huggingface
categories: [ML]
tags: [ML, AI, pytorch, huggingface, LLM]
author: wpsze
date: 2025-01-08 15:56:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: 
banner_img: 
---

# Conda env

```console
# check CUDA Version:
$ nvidia-smi

micromamba env create -n huggingface_hub
micromamba activate huggingface_hub 
micromamba install python==3.10
micromamba env create -n huggingface_hub
python -c "from huggingface_hub import model_info; print(model_info('gpt2'))"
micromamba install pytorch torchvision torchaudio pytorch-cuda=12.4 -c pytorch -c nvidia
micromamba install git-lfs
micromamba install transformers
```

# Example

## deepseek-vl2-tiny

```console
git clone https://github.com/deepseek-ai/DeepSeek-VL2.git

micromamba env create -n DeepSeek-VL2
micromamba activate DeepSeek-VL2 
micromamba install python==3.10
pip install -e .
pip install flash-attn
pip install xformers
```

### Download

- Files and versions

```console
 Make sure you have git-lfs installed (https://git-lfs.com)
git lfs install

git clone https://huggingface.co/deepseek-ai/deepseek-vl2-tiny

# If you want to clone without large files - just their pointers
GIT_LFS_SKIP_SMUDGE=1 git clone https://huggingface.co/deepseek-ai/deepseek-vl2-tiny
```

### 
