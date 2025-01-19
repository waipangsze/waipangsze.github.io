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
index_img: https://i.imgur.com/lXuIXkO.png
banner_img: https://i.imgur.com/lXuIXkO.png
---

# Conda env

```console
# check CUDA Version:
$ nvidia-smi

micromamba env create -n huggingface
micromamba activate huggingface 
micromamba install python==3.10
micromamba install pytorch torchvision torchaudio pytorch-cuda=12.4 -c pytorch -c nvidia
micromamba install conda-forge::transformers
micromamba install git-lfs
```

# Where does hugging face's transformers save models?

The cache location has changed again, and is now `~/.cache/huggingface/hub/`

```console
$ du -sh ~/.cache/huggingface/hub/*
6.3G	/home/wpsze/.cache/huggingface/hub/models--deepseek-ai--deepseek-vl2-tiny
414M	/home/wpsze/.cache/huggingface/hub/models--dslim--bert-base-NER
961M	/home/wpsze/.cache/huggingface/hub/models--ecmwf--aifs-single
1.7G	/home/wpsze/.cache/huggingface/hub/models--microsoft--DialoGPT-medium
172K	/home/wpsze/.cache/huggingface/hub/models--microsoft--Florence-2-large
6.7G	/home/wpsze/.cache/huggingface/hub/models--stabilityai--stable-diffusion-xl-base-1.0
4.4G	/home/wpsze/.cache/huggingface/hub/models--stabilityai--stable-diffusion-xl-refiner-1.0
4.0K	/home/wpsze/.cache/huggingface/hub/version.txt
4.0K	/home/wpsze/.cache/huggingface/hub/version_diffusers_cache.txt
```

# Example

- <https://waipangsze.github.io/2025/01/09/ML-stable-diffusion/>