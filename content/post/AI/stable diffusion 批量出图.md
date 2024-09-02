---
title: "stable diffusion 批量出图 & 速度优化"
categories: ["AI"]
tags: []
draft: false
slug: "stable diffusion prompt from text file"
date: "2023-08-03 00:35:00"
images: "https://img.bi-bo.cn/2023/08/stable-diffusion-feature-1160x680.jpg" 
---

### 使用文件脚本

可以使用命令行参数构建一个提示符，并可以随心所欲地配置各种参数。举个例子

`--prompt "A happy Khajiit resting in a bed" --negative_prompt "deformed, ugly, creepy, mutation" --steps 10 --cfg_scale 9.5 --sampler_name "DDIM" --seed 347520046 --width 512 --height 512`

stable diffusion webui 接受的参数

```
prompt_tags = {
    "sd_model": None,
    "outpath_samples": process_string_tag,
    "outpath_grids": process_string_tag,
    "prompt_for_display": process_string_tag,
    "prompt": process_string_tag,
    "negative_prompt": process_string_tag,
    "styles": process_string_tag,
    "seed": process_int_tag,
    "subseed_strength": process_float_tag,
    "subseed": process_int_tag,
    "seed_resize_from_h": process_int_tag,
    "seed_resize_from_w": process_int_tag,
    "sampler_index": process_int_tag,
    "sampler_name": process_string_tag,
    "batch_size": process_int_tag,
    "n_iter": process_int_tag,
    "steps": process_int_tag,
    "cfg_scale": process_float_tag,
    "width": process_int_tag,
    "height": process_int_tag,
    "restore_faces": process_boolean_tag,
    "tiling": process_boolean_tag,
    "do_not_save_samples": process_boolean_tag,
    "do_not_save_grid": process_boolean_tag,
    "denoising_strength": process_float_tag,  # <-- add this line
    "clip_skip": process_int_tag  # <-- add this line
}
```

注意带有process_string_tag的变量。必须包含在 **“”** 中。也可以添加更多这样的提示标记，只需确保遵循如上所示的语法即可。例如，采样器名称被识别为字符串，因此您可以从web UI中键入文字名称（例如“DPM++SDE Karras”）。

对于每个提示，制作一行新行，然后复制/粘贴另一个命令参数块。

## Batch Count 与 Batch Size 提高出图速度
![](https://techtactician.com/wp-content/uploads/2023/07/Batch-Size-vs-Batch-Count-In-Stable-Diffusion-What-Is-The-Difference-Quick-Answer.jpg)


批次中的项目是并行处理的。批次大小是每个批次中有多少个并行图像。批次计数将一个接一个地运行多个批次。

理论上，并行处理图像会稍微快一点，但它也会使用更多的内存——速度会快多少取决于您的GPU。


"Batch count = 8" with "batch size = 1", 78 seconds

"Batch count = 1" with "batch size = 8", 27 seconds

#### CUDA核心使用对比

- 使用 batch count
  ![mJc2CJl](https://img.bi-bo.cn/2023/11/mJc2CJl.png) 
- 使用 batch size
  ![pmbsDXK](https://img.bi-bo.cn/2023/11/pmbsDXK.png)
