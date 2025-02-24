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
pip install invisible_watermark transformers accelerate safetensors gradio
micromamba install git-lfs
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

GPU: ~ 14GB 

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

# web page

- [GPU-基于Diffusers和Gradio搭建SDXL推理应用](https://www.volcengine.com/docs/6419/1149863)
- [麻瓜走入魔法世界 I — Gradio](https://medium.com/@yvontarbajo/%E9%BA%BB%E7%93%9C%E8%B5%B0%E5%85%A5%E9%AD%94%E6%B3%95%E4%B8%96%E7%95%8C-i-gradio-2e9705f2410e)
- <http://localhost:8000/>

{% fold info @python script %}

git clone,

```console
git clone https://github.com/AUTOMATIC1111/TorchDeepDanbooru.git
cd TorchDeepDanbooru
wget https://github.com/AUTOMATIC1111/TorchDeepDanbooru/releases/download/v1/model-resnet_custom_v3.pt
```

and web.py,

```python
#!/bin/python

import numpy as np
import gradio as gr
from diffusers import DiffusionPipeline,StableDiffusionXLImg2ImgPipeline
import torch
import tqdm
from datetime import datetime

from TorchDeepDanbooru import deep_danbooru_model

MODEL_BASE =  "stabilityai/stable-diffusion-xl-base-1.0"
MODEL_REFINER = "stabilityai/stable-diffusion-xl-refiner-1.0"

print("Loading model",MODEL_BASE)
base = DiffusionPipeline.from_pretrained(MODEL_BASE, torch_dtype=torch.float16, use_safetensors=True, variant="fp16")
base.to("cuda")
print("Loading model",MODEL_REFINER)
refiner = StableDiffusionXLImg2ImgPipeline.from_pretrained(MODEL_REFINER, text_encoder_2=base.text_encoder_2,vae=base.vae, torch_dtype=torch.float16, use_safetensors=True, variant="fp16",)
refiner.to("cuda")

# Define how many steps and what % of steps to be run on each experts (80/20) here
# base-high noise, refiner-low noise
# the base model steps = default_n_steps*default_high_noise_frac
default_n_steps = 40
default_high_noise_frac = 0.8
default_num_images =2

def predit_txt2img(prompt,negative_prompt,model_selected,num_images,n_steps, high_noise_frac,cfg_scale):
    # run both experts
    start = datetime.now()

    num_images=int(num_images)
    n_steps=int(n_steps)
    prompt, negative_prompt = [prompt] * num_images, [negative_prompt] * num_images
    images_list = []
    model_selected = model_selected
    high_noise_frac=float(high_noise_frac)
    cfg_scale=float(cfg_scale)

    g = torch.Generator(device="cuda")

    if model_selected == "sd-xl-base-1.0" or model_selected == "sd-xl-base-refiner-1.0":
        images = base(
            prompt=prompt,
            negative_prompt=negative_prompt,
            num_inference_steps=n_steps,
            denoising_end=high_noise_frac,
            guidance_scale=cfg_scale,
            output_type="latent" if model_selected == "sd-xl-base-refiner-1.0" else "pil",
            generator=g
        ).images
    
    if model_selected == "sd-xl-base-refiner-1.0":
        images = refiner(
            prompt=prompt,
            negative_prompt=negative_prompt,
            num_inference_steps=n_steps,
            denoising_start=high_noise_frac,
            guidance_scale=cfg_scale,
            image=images,
        ).images

    for image in images:
        images_list.append(image)

    torch.cuda.empty_cache()
    
    cost_time=(datetime.now()-start).seconds
    print(f"cost time={cost_time},{datetime.now()}")
    
    return images_list

def predit_img2img(prompt, negative_prompt,init_image, model_selected,n_steps, high_noise_frac,cfg_scale,strength):
    
    start = datetime.now()
    prompt = prompt
    negative_prompt =negative_prompt

    model_selected = model_selected
    init_image = init_image
    n_steps=int(n_steps)
    high_noise_frac=float(high_noise_frac)
    cfg_scale=float(cfg_scale)
    strength=float(strength)

    if model_selected == "sd-xl-refiner-1.0":
        images = refiner(
            prompt=prompt,
            negative_prompt=negative_prompt,
            num_inference_steps=n_steps,
            denoising_start=high_noise_frac,
            guidance_scale=cfg_scale,
            strength = strength,
            image=init_image,
            # target_size = (1024, 1024)
        ).images

    torch.cuda.empty_cache()
    
    cost_time=(datetime.now()-start).seconds

    print(f"cost time={cost_time},{datetime.now()}")

    return images[0]

def interrogate_deepbooru(pil_image, threshold):
    threshold =0.5
    model = deep_danbooru_model.DeepDanbooruModel()
    model.load_state_dict(torch.load('/home/wpsze/ML/huggingface_hub/stable-diffusion-xl-base-1.0/TorchDeepDanbooru/model-resnet_custom_v3.pt'))
    model.eval().half().cuda()

    pic = pil_image.convert("RGB").resize((512, 512))
    a = np.expand_dims(np.array(pic, dtype=np.float32), 0) / 255

    with torch.no_grad(), torch.autocast("cuda"):
        x = torch.from_numpy(a).cuda()

        # first run
        y = model(x)[0].detach().cpu().numpy()

        # measure performance
        for n in tqdm.tqdm(range(10)):
            model(x)

    result_tags_out = []
    for i, p in enumerate(y):
        if p >= threshold:
            result_tags_out.append(model.tags[i])
            print(model.tags[i], p)

    prompt = ', '.join(result_tags_out).replace('_', ' ').replace(':', ' ')
    print(f"prompt={prompt}")
    return prompt

def clear_txt2img(prompt, negative_prompt):
    prompt = ""
    negative_prompt = ""
    return prompt, negative_prompt

def clear_img2img(prompt, negative_prompt, image_input,image_output):
    prompt = ""
    negative_prompt = ""
    image_input = None
    image_output = None

    return prompt, negative_prompt,image_input,image_output

with gr.Blocks(title="Stable Diffusion",theme=gr.themes.Default(primary_hue=gr.themes.colors.blue))as demo:
    
    with gr.Tab("Text-to-Image"): 
        # gr.Markdown("Stable Diffusion XL Base + Refiner.")
        model_selected = gr.Radio(["sd-xl-base-refiner-1.0","sd-xl-base-1.0"],show_label=False, value="sd-xl-base-refiner-1.0")       
        with gr.Row():
            with gr.Column(scale=4):
                prompt = gr.Textbox(label= "Prompt",lines=3)
                negative_prompt = gr.Textbox(label= "Negative Prompt",lines=1)
                with gr.Row():
                    with gr.Column():                        
                        n_steps=gr.Slider(20, 60, value=default_n_steps, label="Steps", info="Choose between 20 and 60")
                        high_noise_frac=gr.Slider(0, 1, value=0.8, label="Denoising Start at")
                    with gr.Column():     
                        num_images=gr.Slider(1, 3, value=default_num_images, label="Gernerated Images", info="Choose between 1 and 3") #num images=4,A10报显存溢出
                        cfg_scale=gr.Slider(1, 20, value=7.5, label="CFG Scale")
            with gr.Column(scale=1):
                with gr.Row():
                    txt2img_button = gr.Button("Generate",size="sm")
                    clear_button = gr.Button("Clear",size="sm")
        gallery = gr.Gallery(label="Generated images", show_label=False, elem_id="gallery",columns=int(num_images.value), height=800,object_fit='fill')
        
        txt2img_button.click(predit_txt2img, inputs=[prompt, negative_prompt, model_selected,num_images,n_steps, high_noise_frac,cfg_scale], outputs=[gallery])
        clear_button.click(clear_txt2img, inputs=[prompt, negative_prompt], outputs=[prompt, negative_prompt])
    
    with gr.Tab("Image-to-Image"):  
        model_selected = gr.Radio(["sd-xl-refiner-1.0"],value="sd-xl-refiner-1.0",show_label=False)
        with gr.Row():
            with gr.Column(scale=1):
                prompt = gr.Textbox(label= "Prompt",lines=2)
            with gr.Column(scale=1):            
                negative_prompt = gr.Textbox(label= "Negative Prompt",lines=2)
        with gr.Row():
            with gr.Column(scale=3):
                image_input = gr.Image(type="pil",height=512)
            with gr.Column(scale=3):
                image_output = gr.Image(height=512)
            with gr.Column(scale=1):
                img2img_deepbooru = gr.Button("Interrogate DeepBooru",size="sm")
                # img2img_clip = gr.Button("Interrogate CLIP",size="sm")
                img2img_button = gr.Button("Generate",size="lg")
                clear_button = gr.Button("Clear",size="sm") 
                                
                n_steps=gr.Slider(20, 60, value=40, step=10,label="Steps")
                high_noise_frac=gr.Slider(0, 1, value=0.8, step=0.1,label="Denoising Start at")
                cfg_scale=gr.Slider(1, 20, value=7.5, step=0.1,label="CFG Scale")
                strength=gr.Slider(0, 1, value=0.3,step=0.1,label="Denoising strength")  
       
        img2img_deepbooru.click(fn=interrogate_deepbooru, inputs=image_input,outputs=[prompt])
        img2img_button.click(predit_img2img, inputs=[prompt, negative_prompt, image_input, model_selected, n_steps, high_noise_frac,cfg_scale,strength], outputs=image_output)
        clear_button.click(clear_img2img, inputs=[prompt, negative_prompt, image_input], outputs=[prompt, negative_prompt, image_input,image_output]) 

if __name__ == "__main__":
    demo.launch(server_name="0.0.0.0", server_port=8000)
```
{% endfold %}

![](https://i.imgur.com/0AR2q5n.png){width=500}

![](https://i.imgur.com/5SRHOZj.png)

# Access to model is restricted (not yet)

```console
Cannot access gated repo for url https://huggingface.co/stabilityai/stable-diffusion-3.5-large/resolve/main/model_index.json.
Access to model stabilityai/stable-diffusion-3.5-large is restricted. You must have access to it and be authenticated to access it. Please log in.
```

{% note success %}
huggingface -- sign up -- Access Tokens -- generate token key
{% endnote %}

You need to sign in to huggingface, accept the license agreement on sd 3.5 page, go to settings in your hf profile, find your token and input it in sd next config file.

```console
$ huggingface-cli login

    _|    _|  _|    _|    _|_|_|    _|_|_|  _|_|_|  _|      _|    _|_|_|      _|_|_|_|    _|_|      _|_|_|  _|_|_|_|
    _|    _|  _|    _|  _|        _|          _|    _|_|    _|  _|            _|        _|    _|  _|        _|
    _|_|_|_|  _|    _|  _|  _|_|  _|  _|_|    _|    _|  _|  _|  _|  _|_|      _|_|_|    _|_|_|_|  _|        _|_|_|
    _|    _|  _|    _|  _|    _|  _|    _|    _|    _|    _|_|  _|    _|      _|        _|    _|  _|        _|
    _|    _|    _|_|      _|_|_|    _|_|_|  _|_|_|  _|      _|    _|_|_|      _|        _|    _|    _|_|_|  _|_|_|_|

    To log in, `huggingface_hub` requires a token generated from https://huggingface.co/settings/tokens .
Enter your token (input will not be visible): 
Add token as git credential? (Y/n) n
Token is valid (permission: fineGrained).
The token `sd` has been saved to /home/wpsze/.cache/huggingface/stored_tokens
Your token has been saved to /home/wpsze/.cache/huggingface/token
Login successful.
The current active token is: `sd`
```

Then,

```console
403 Forbidden: Please enable access to public gated repositories in your fine-grained token settings to view this repository..
Cannot access content at: https://huggingface.co/stabilityai/stable-diffusion-3.5-large/resolve/main/model_index.json.
Make sure your token has the correct permissions.
```

# References

1. [使用 Docker 快速上手 Stability AI 的 SDXL 1.0 正式版](https://soulteary.com/2023/07/29/get-started-with-stability-ai-sdxl-1-0-release-using-docker.html)
2. [在搭载 M1 及 M2 芯片 MacBook设备上玩 Stable Diffusion 模型](https://soulteary.com/2022/12/10/play-the-stable-diffusion-model-on-macbook-devices-with-m1-and-m2-chips.html)
3. [GPU-基于Diffusers和Gradio搭建SDXL推理应用](https://www.volcengine.com/docs/6419/1149863)
4. [麻瓜走入魔法世界 I — Gradio](https://medium.com/@yvontarbajo/%E9%BA%BB%E7%93%9C%E8%B5%B0%E5%85%A5%E9%AD%94%E6%B3%95%E4%B8%96%E7%95%8C-i-gradio-2e9705f2410e)