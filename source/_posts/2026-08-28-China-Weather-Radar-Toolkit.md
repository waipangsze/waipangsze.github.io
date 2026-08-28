---
layout: post
title: China Weather Radar | 中国天气雷达基数据 
categories: [NWP]
tags: [MPAS, NWP, WRF, DA, Radar]
author: wpsze
date: 2026-08-28 04:27:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/pfRn8xE.png
banner_img: https://i.imgur.com/pfRn8xE.png
---

# pycwr

PyCWR简介

PyCWR 解决的是中国天气雷达基数据“格式多、年代跨度大、业务链路不一致”的现实问题。
它把 reader、体扫对象、绘图、QC、HID、风场反演、插值和标准格式导出连成一条 连贯的 Python 工作流，让你不必为每一种输入格式单独重写处理逻辑。

- <https://pycwr.readthedocs.io/en/latest/PyCWR_intro.html>
- <https://github.com/YvZheng/pycwr>
- `pycwr` 是一个面向中国天气雷达业务流程的 Python 工具库，覆盖雷达基数据读取、 几何计算、绘图、质量控制、水凝物分类、单雷达风场反演、多雷达组网插值和导出。
- The China Weather Radar Toolkit, support most of China's radar formats(**WSR98D, CINRAD/SA/SB/CB, CINRAD/CC/CCJ, CINRAD/SC/CD**)

```sh
# Create and activate a new Conda environment (Python ≥ 3.9 is required, ≥ 3.10 is recommended for full features):
conda create -n pycwr_env python=3.10
conda activate pycwr_env

# 全功能安装
python -m pip install -r requirements-full.txt
python -m pip install ".[full]"

# Verify your installation by running this command in your terminal:
python -c "import pycwr; print(pycwr.__version__)"
```

# Example data

## Example Case Data of Forward Radar Operator ZJU-AERO Release V0.6.0

- [Xie, H., Bi, L., Wang, Z., & Han, W. (2024). **Modeling of melting layer in cross-platforms radar observation operator ZJU-AERO: Multi-stage melting particle model, scattering computation, and bulk parameterization**. Journal of Geophysical Research: Atmospheres, 129, e2024JD040725. https://doi.org/10.1029/2024JD040725](https://agupubs.onlinelibrary.wiley.com/doi/10.1029/2024JD040725)
  - ZJU-AERO V0.6.0 是由浙江大學發展的雷達正向觀測算子（Accurate and Efficient Radar Operator）的更新版本。
  - V0.6.0 版本引入並評估了多階段融化粒子模型與散射計算，能更精確地模擬融化層場景中的雷達觀測。
  - 體積採樣方法：提出了全新的體積採樣（volume-sampling）方法以提升模擬效率與準確度。
  - 應用範圍：用於介接數值天氣預報（NWP）模型（如中國氣象局 CMA-GFS/MESO），支援地面雷達與星載雷達的極化雷達變數模擬。
- <https://zenodo.org/records/11307206>
- ./case-04-Doksuri-2023-07-28.zip is for a demonstration forward simulation case of **typhoon** observed by ground based radar (as required by one of the reviewers of the GMD paper, new).
-  Z_RADR_I_Z9592_20230728020437_**O_DOR_SAD_CAP_FMT.bin.bz2**

# 数据格式

- [**QX/T 653-2022《天气雷达基数据和单站产品格式》**](http://www.cmastd.cn/standardView.jspx?id=4131)
- [**QX/T 653-2022 天气雷达基数据和单站产品格式**](http://gx.cma.gov.cn/zfxxgk/zwgk/flfgbz/dfbz/202307/P020230712582565380137.pdf#16#1)
- [安徽省天气雷达观测业务管理规定（试行）.pdf | 2024 年 03 月](https://www.mengcheng.gov.cn/upload_bz/download?siteId=4&id=810200#8#5)
- [如何判断文件类型？](https://pycinrad.cn/io/ftype.html)

目前，最权威的现行标准是气象行业标准 `QX/T 653-2022` ——《天气雷达基数据和单站产品格式》。该标准自2023年2月1日起实施，明确规定了新一代多普勒天气雷达基数据和单站产品的数据类型与格式，适用于业务中的传输、存储和交换。

* **格式统一进程**：中国气象局近年来大力推行数据格式标准化，通过发布《天气雷达基数据和单站产品格式》等行业标准，解决了过去不同厂家、不同型号雷达数据格式各异的问题。文件名中带 `FMT` 标识的，就是这种统一后的标准格式文件。
* **数据字段差异**：随着技术发展，新一代双偏振雷达能获取的数据也更多了。除了传统的反射率因子、径向速度和速度谱宽，还增加了**差分反射率因子、差分传播相移、相关系数**等双偏振参数。
* 所以，在处理雷达数据时，首先看文件名里是否有 `FMT`，如果有，说明它遵循的是统一的“标准格式”规范，数据结构和读取方式比较通用。对于新型的X波段相控阵雷达，则需要留意它可能使用了专门的格式。

下面这个表格整理了主流雷达的数据格式情况：

| 雷达类型 | 当前数据格式 | 说明 |
| :--- | :--- | :--- |
| **新一代天气雷达** (S/C波段) | **标准格式 (含 `FMT` 标识)** | 根据2022年发布的行业标准，所有在业务运行的S、C波段雷达基数据和产品都统一为此格式。该格式规范了文件结构、命名规则和数据字段。 |
| **X波段天气雷达** | **参照/等同于标准格式** | X波段天气雷达的基数据格式与新一代天气雷达（S/C波段）**相同**，遵循同一套标准。其产品格式则另有规定。 |
| **X波段相控阵雷达** | **专用格式** | 作为近年来的新型设备，其基数据格式参照单独的[《相控阵 X 波段雷达基数据格式（试用）》](https://www.mengcheng.gov.cn/upload_bz/download?siteId=4&id=810200#8#5)执行，与普通X波段雷达格式不同。 |

## S波段与C波段雷达详解

中国新一代天气雷达网主要根据探测波段的不同，分为S波段和C波段两大系列，功能上各有侧重。

| 波段 | 具体型号 | 主要特点与用途 |
| :--- | :--- | :--- |
| **S波段**<br>(波长约10cm) | CINRAD/SA, CINRAD/SB, CINRAD/SC | 堪称雷达家族里的“大块头”，球罩直径可达**12米**。电磁波穿透力强，最大探测半径可达**460公里**，是追踪**台风、大范围暴雨**的绝对主力，因此主要布防在**沿海及大江大河流域**。 |
| **C波段**<br>(波长约5cm) | CINRAD/CB, CINRAD/CC, CINRAD/CD, CINRAD/CCJ | 个头相对紧凑（球罩直径约**7米**），探测半径约**200公里**。探测精度更高，特别擅长捕捉**冰雹、短时强降雨**等局地强对流天气，主要分布在**内陆地区**，作为S波段雷达网的补充。 |

> 值得一提的是，中国新一代天气雷达的起步，源于对美国先进多普勒雷达技术（NEXRAD）的引进和国产化，CINRAD/SA型号与美国的WSR-88D雷达有着紧密的技术渊源。

除了这些主流的S、C波段固定式雷达，近年来，**X波段**雷达和**相控阵**雷达也作为重要补充被广泛使用。它们更轻便灵活，能有效填补大城市或复杂地形的近地面观测盲区。

## 根据文件名中的特定标识

### `O_DOR_SAD_CAP_FMT`

根据文件名中的特定标识，`O_DOR_SAD_CAP_FMT` 表明这是一个**采用中国气象局标准格式的S波段雷达基数据文件**。这个字段是文件命名规则的一部分，拆解开来，各部分含义如下：

| 字段部分 | 含义 | 说明 |
| :--- | :--- | :--- |
| **O** | 数据级别（Data Level） | 代表观测数据（Observation），表明这是雷达直接探测得到的原始基数据，而非经过二次加工的产品。 |
| **DOR** | 数据类型（Data Type） | 是“Doppler Radar”的缩写，指明数据来源为多普勒天气雷达。 |
| **SAD** | 雷达型号（Radar Model） | 代表雷达具体型号，这里的 **SAD** 很可能指代 **CINRAD/SA** 或同系列 **S波段** 雷达的某种命名变体。 |
| **CAP** | 扫描方式/策略（Scan Strategy） | 代表 **体扫模式（Volume Scan）**，通常指雷达以多个仰角完成一次完整的“体积扫描”，这也是最主要的业务扫描方式。 |
| **FMT** | 格式标识（Format Flag） | 这是 **“标准格式”（Format）** 的明确标识，用于区分于旧的、非标准的数据格式。 |

### `O_DOR_CD_CAP_FMT`

`O_DOR_CD_CAP_FMT` 中的 `CD` 指的是雷达型号为 **CINRAD/CD**。

可以将这个文件名拆解为以下几个部分来理解：

| 字段 | 含义 | 说明 |
| :--- | :--- | :--- |
| **O** | 数据级别 | 代表这是一份观测数据（Observation），即雷达直接探测得到的原始基数据。 |
| **DOR** | 数据类型 | 是“Doppler Radar”的缩写，表明数据来自多普勒天气雷达。 |
| **CD** | 雷达型号 | 代表雷达具体型号为 **CINRAD/CD**，这是一款**C波段**的新一代天气雷达。 |
| **CAP** | 扫描方式 | 代表这是**体扫（Volume Scan）** 数据，即雷达在多个仰角完成的一次完整扫描，这是最主要的业务扫描模式。 |
| **FMT** | 格式标识 | 是“标准格式”（Format）的明确标识。这个标识表明该文件遵循中国气象局自2021年起推行的**统一标准格式**，不同型号雷达的数据可以更规范地被读取和处理。 |

### 补充背景：关于“标准格式”

文件名中的 `FMT` 并非随意标记。自2021年起，中国天气雷达基数据已全面切换为这种**统一的标准格式**。这套格式统一了此前因不同厂家、不同型号雷达（如SA、SB、CB、CC等）而产生的7种格式，让数据读取和处理更加规范和高效。标准格式的数据在存储方式上也与旧格式不同，例如，数据可能按径向流式存储，而不是按体扫文件存储，这提升了数据传输时效。

# Trial

```python
from pycwr.io import read_auto

radar = read_auto("Z_RADR_I_Z9592_20230728020437_O_DOR_SAD_CAP_FMT.bin.bz2")
print(radar.summary())
print(radar.available_fields())
print(radar.sweep_summary()[0])

# extract level 0
dBZ0 = radar.get_sweep_field(0, "dBZ")
velocity0 = radar.get_sweep_field(0, "V")

# plot with map
from pycwr.draw import plot_ppi_map
plot_ppi_map(radar, field="dBZ", sweep=0, show=True)

# plot vertical
from pycwr.draw import plot_section
plot_section(radar, start=(-50, 0), end=(50, 0), field="dBZ", show=True)
```

