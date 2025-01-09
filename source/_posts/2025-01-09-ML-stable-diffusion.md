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
