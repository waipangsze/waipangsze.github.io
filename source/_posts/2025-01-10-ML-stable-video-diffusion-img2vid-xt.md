---
layout: post
title: ML | stable-video-diffusion-img2vid-xt
categories: [ML]
tags: [ML, AI, pytorch, huggingface, LLM, stable-video-diffusion]
author: wpsze
date: 2025-01-10 11:50:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: 
banner_img: 
---

# stable-video-diffusion-img2vid-xt

Stable Video Diffusion (SVD) Image-to-Video is a diffusion model that takes in a still image as a conditioning frame, and generates a video from it.

Stable Video Diffusion (SVD) 1.1 (image -to-Video)是一种以静止图像作为条件帧，并从中生成视频的扩散模型。

(SVD) Image-to-Video is a latent diffusion model trained to generate short video clips from an image conditioning. This model was trained to **generate 25 frames at resolution 576x1024** given a context frame of the same size, finetuned from SVD Image-to-Video [14 frames]. We also finetune the widely used f8-decoder for temporal consistency. 

All considered potential data sources were included for final training, with none held out as the proposed data filtering methods described in the SVD paper handle the quality control/filtering of the dataset. With regards to safety/NSFW filtering, sources considered were either deemed safe or filtered with the in-house NSFW filters. No explicit human labor is involved in training data preparation. However, human evaluation for model outputs and quality was extensively used to evaluate model quality and performance. 

Not safe for work (NSFW) is Internet slang or shorthand used to mark links to content, videos, or website pages the viewer may not wish to be seen viewing in a public, formal, or controlled environment. NSFW Filter 是一款通过AI 过滤网页上NSFW 成人内容的Firefox、Chrome 插件，可以帮助你过滤掉那些不适时宜出现的内容，主要是图片、视频。

SVD全称Stable Video Diffusion，是StabilityAI最新的开源文本生成视频大模型。这个模型是基于Stable Diffusion 2.1进行初始化，然后通过在图像模型中插入时空卷积和注意力层来构建这个视频生成模型的架构，最终在**1.52亿视频数据集上训练得到**。

SVD系列模型包含2个版本，一个是可以生成 **14帧576x1024图像 的SVD常规模板**，一个是可以生成 **20帧的 SVD-XT 的微调版本**。二者除了生成视频的帧数不一样外，其它都是完全相同的。

| 模型名称                    | 视频帧数 | 开源情况             | 
|-----------------------------|----------|----------------------|
| Stable Video Diffusion      | 14帧     | 允许研究，不允许商用     |
| Stable Video Diffusion - XT | 20帧     | 允许研究，不允许商用 |

It’s entirely possible to run the `img2vid` and `img2vid-xt` models on a GTX 1080 with **8GB of VRAM**!


- <https://huggingface.co/stabilityai/stable-video-diffusion-img2vid-xt>
- <https://github.com/Stability-AI/generative-models>

```console
micromamba env create -n SVD
micromamba activate SVD
micromamba install python==3.10
micromamba install pytorch torchvision torchaudio pytorch-cuda=12.4 -c pytorch -c nvidia
pip install diffusers --upgrade
pip install invisible_watermark transformers accelerate safetensors gradio
micromamba install git-lfs


git clone https://huggingface.co/stabilityai/stable-video-diffusion-img2vid-xt

git clone https://github.com/Stability-AI/generative-models.git
cd generative-models/

(xxx) micromamba install imageio einops fire omegaconf rembg

pip install -r requirements/pt2.txt 
pip install .
pip3 install -e git+https://github.com/Stability-AI/datapipelines.git@main#egg=sdata

# 下载 safetensors 模型 (svd_xt.safetensors) ，放到 checkpoints 这个文件夹里。
mkdir checkpoints
cd checkpoints/
ln -s ../../stable-video-diffusion-img2vid-xt/svd_xt* .
```

Run,

```console
# 输入下面的命令，启动界面。
$ streamlit run scripts/demo/video_sampling.py

# 上述命令会自动打开一个新的网页。如果没有打开，看看 PowerShell 窗口的信息。找到本地可以打开的URL，应该是这样的：
# http://localhost:8501 
```

##

November 21, 2023

We are releasing Stable Video Diffusion, an image-to-video model, for research purposes:

- SVD: This model was trained to generate 14 frames at resolution 576x1024 given a context frame of the same size. We use the standard image encoder from SD 2.1, but replace the decoder with a temporally-aware deflickering decoder.
- SVD-XT: Same architecture as SVD but finetuned for 25 frame generation.
You can run the community-build gradio demo locally by running python -m scripts.demo.gradio_app.
- We provide a **streamlit demo** `scripts/demo/video_sampling.py` and a **standalone python script** `scripts/sampling/simple_video_sample.py` for inference of both models.
- Alongside the model, we release a technical report.

# References

1. [运行Stable Video Diffusion模型实现图生视频img2vid](https://zhuanlan.zhihu.com/p/672699032)