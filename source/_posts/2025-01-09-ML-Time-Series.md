---
layout: post
title: ML | Time Series
categories: [ML]
tags: [AI, ML, pytorch]
author: wpsze
date: 2025-01-13 21:42:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: 
banner_img: 
---

Conda env is built as,

```console
micromamba env create -n ML
micromamba activate ML
micromamba install python==3.10
micromamba install pytorch torchvision torchaudio pytorch-cuda=12.4 -c pytorch -c nvidia
micromamba install yfinance matplotlib pandas numpy scipy
micromamba install scikit-learn
micromamba install ipykernel
python -m ipykernel install --user --name=ML --display-name='Environment(ML)'
micromamba install plotly
micromamba install  pytorch_geometric
```