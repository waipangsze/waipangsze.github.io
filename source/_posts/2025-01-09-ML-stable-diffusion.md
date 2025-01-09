---
layout: post
title: ML | stable-diffusion
categories: [ML]
tags: [ML, AI, pytorch, huggingface, LLM, stable-diffusion]
author: wpsze
date: 2025-01-09 14:04:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/imchHVz.png
banner_img: https://i.imgur.com/imchHVz.png
---

# stable-diffusion-xl-base-1.0

- <https://huggingface.co/stabilityai/stable-diffusion-xl-base-1.0>

![](https://huggingface.co/stabilityai/stable-diffusion-xl-base-1.0/resolve/main/pipeline.png){width=500}

The `stable-diffusion-xl-base-1.0` is a text-to-image generative model developed by Stability AI.

- Inputs
  - Text prompt: A description of the desired image, such as "a beautiful sunset over a mountain landscape".
- Outputs
  - Generated image: An image that corresponds to the input text prompt, generated using the model's diffusion-based text-to-image capabilities.

```console
micromamba env create -n SD
micromamba activate SD
micromamba install python==3.10
pip install diffusers --upgrade
pip install invisible_watermark transformers accelerate safetensors
```

- When using torch >= 2.0, you can improve the inference speed by 20-30% with torch.compile. Simple wrap the unet with torch compile before running the pipeline:
  - `pipe.unet = torch.compile(pipe.unet, mode="reduce-overhead", fullgraph=True)`
- If you are limited by GPU VRAM, you can enable cpu offloading by calling pipe.enable_model_cpu_offload instead of .to("cuda"):
  - `- pipe.to("cuda")`
  - `+ pipe.enable_model_cpu_offload()`

The models are saved at,

```console
6.7G	/home/wpsze/.cache/huggingface/hub/models--stabilityai--stable-diffusion-xl-base-1.0
4.4G	/home/wpsze/.cache/huggingface/hub/models--stabilityai--stable-diffusion-xl-refiner-1.0
```

## base model

To just use the base model, you can run:

```python
#!/bin/python

from diffusers import DiffusionPipeline
import torch

pipe = DiffusionPipeline.from_pretrained("stabilityai/stable-diffusion-xl-base-1.0", torch_dtype=torch.float16, use_safetensors=True, variant="fp16")
pipe.to("cuda")

# if using torch < 2.0
# pipe.enable_xformers_memory_efficient_attention()

prompt = "An astronaut riding a green horse"

image = pipe(prompt=prompt).images[0]

image.save("astronaut_rides_horse.png") # 1024 x 1024
```

GPU: ~ 10GB 



## base + refiner pipeline

To use the whole base + refiner pipeline as an ensemble of experts you can run:

```python
from diffusers import DiffusionPipeline
import torch

# load both base & refiner
base = DiffusionPipeline.from_pretrained(
    "stabilityai/stable-diffusion-xl-base-1.0", torch_dtype=torch.float16, variant="fp16", use_safetensors=True
)
base.to("cuda")
refiner = DiffusionPipeline.from_pretrained(
    "stabilityai/stable-diffusion-xl-refiner-1.0",
    text_encoder_2=base.text_encoder_2,
    vae=base.vae,
    torch_dtype=torch.float16,
    use_safetensors=True,
    variant="fp16",
)
refiner.to("cuda")

# Define how many steps and what % of steps to be run on each experts (80/20) here
n_steps = 40
high_noise_frac = 0.8

prompt = "A majestic lion jumping from a big stone at night"

# run both experts
image = base(
    prompt=prompt,
    num_inference_steps=n_steps,
    denoising_end=high_noise_frac,
    output_type="latent",
).images
image = refiner(
    prompt=prompt,
    num_inference_steps=n_steps,
    denoising_start=high_noise_frac,
    image=image,
).images[0]

image.save("refiner.png") 
```

GPU: ~ 13GB 

## Demo

- **Left**: Base model,    **Right**: Base+refiner model

  - **prompt = "An astronaut is doing Numerical Weather Prediction simulation on Space."**

{% gi 2 2 %}
![](https://i.imgur.com/imchHVz.png)
![](https://i.imgur.com/KKiwfNI.png)
{% endgi %}

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


# References

1. [使用 Docker 快速上手 Stability AI 的 SDXL 1.0 正式版](https://soulteary.com/2023/07/29/get-started-with-stability-ai-sdxl-1-0-release-using-docker.html)
2. [在搭载 M1 及 M2 芯片 MacBook设备上玩 Stable Diffusion 模型](https://soulteary.com/2022/12/10/play-the-stable-diffusion-model-on-macbook-devices-with-m1-and-m2-chips.html)