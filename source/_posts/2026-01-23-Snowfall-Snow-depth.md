---
layout: post
title: Snowfall/Snow depth | 降雪量/降雪深度
categories: [Meteorology]
tags: [NWP, MAPS, WRF, Snowfall, Snow depth]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2026-01-23 06:15:00
index_img: https://i.imgur.com/mker4Mc.png
banner_img: https://i.imgur.com/mker4Mc.png
---

- [MPAS | WRF | precipitation calculation](https://waipangsze.github.io/2025/03/10/MPAS-WRF-precipitation-calculation/)

---

# 中国气象局

- [降雪量与降雪深度 | 中国气象报社](https://www.cma.gov.cn/2011xzt/2012zhuant/20120302/2012030205/201203020502/201203/t20120306_163750.html)
- [降雪的形成条件及等级划分 | 中国气象报社](https://www.cma.gov.cn/2011xzt/kpbd/SnowStorm/2018050902/201811/t20181106_482641.html)
- [科普 | 降雪量≠积雪深度？](https://mp.weixin.qq.com/s/XSAuyfz4QGVu5pFLLXJKCg)

**降雪量**与**降雪深度**是两个不同的概念，**降雪量并不是指积雪深度**。

- 在气象观测上，降水量是指某一时段内的未经蒸发、渗透、流失的降水，在水平面上积累的深度，以毫米为单位，取一位小数。如果是降雪量，则指溶化后的水，在水平面上积累的深度，对于降雪量的大小需要用统一规定的标准进行界定，它是气象观测员用标准的降水观测容器（俗称雨量器），将收集到的雪水融化后测量出的量度，**以毫米为单位**，亦称**降水量**。目前气象部门安装有固态降水自动观测站,可适时自动观测降雪量。
- 而**积雪深度**是指从积雪表面到地面的积雪垂直深度，是指气象员使用量雪尺在观测场内或附近平坦、开阔的地方，当落地雪尚未融化时，**测得的积雪的深度（厚度），以厘米为单位**。

当然，降雪量与降雪深度之间存在一定的关系，下雪大，积雪必定深厚，此外积雪厚度还与气温、风等气象要素相连，温度高，积雪很快就会融化，而温度低，积雪就不易融化甚至维持一段较长时间了。 

- 按照降水量强度，降雪分为小雪、中雪、大雪和暴雪四个等级。
  - **小雪**：是指下雪时水平能见距离等于或大于1000米，地面积雪深度在3厘米以下，降水量级为24小时降雪量在0.1～2.4毫米之间。
  - **中雪**：是指下雪时水平能见距离在500～1000米之间，地面积雪深度为3～5厘米，24小时降雪量达2.5～4.9毫米。
  - **大雪**：是指下雪时能见度很差，水平能见距离小于500米，地面积雪深度等于或大于5厘米，24小时降雪量达5.0～9.9毫米。
  - 如果有降雪而没有形成积雪，一般称之为“零星小雪”。
  - 当24小时降雪量达到10.0～19.9毫米时为**暴雪**，20.0～29.9毫米为**大暴雪**，超过30.0毫米为特**大暴雪**。

{% note primary %}
当降雪落地后无融化时，一般而言，**在北方地区 1毫米降雪可形成的积雪深度有8～10毫米**，**在南方地区积雪深度有6～8毫米**。
{% endnote %}

日常生活中流传的“1毫米降雪量对应1厘米积雪深度”，其实是一个非常粗略的经验值，并非科学意义上的固定换算公式。两者的实际比例，核心取决于雪的“蓬松度”，而雪的蓬松度又由雪花的类型和形成环境决定。

雪花的本质是冰晶，不同条件下形成的雪花，密度差异极大。我们可以把雪花分为**干雪**和**湿雪**两类：干雪通常形成于气温较低的环境（一般在-10℃以下），这类雪花晶体结构疏松，内部空隙多，含水量低，蓬松度很高。此时，1毫米的降雪量对应的积雪深度可能达到0.9厘米甚至1.2厘米；而湿雪则形成于气温接近0℃的环境，雪花在降落过程中会部分融化，又重新冻结，晶体结构紧密，含水量高，蓬松度低。这种情况下，1毫米的降雪量对应的积雪深度可能只有0.6厘米到0.8厘米左右。

- 干雪（气温低、雪粒松散）：密度小，1毫米降雪量大概能积1-1.2厘米积雪，手感蓬松，适合堆雪人；
- 湿雪（气温接近0℃、含水分多）：密度大，1毫米降雪量可能只积0.5-0.8厘米积雪，手感沉重，容易压断树枝。

{% gi 3 3 %}
![](https://i.imgur.com/5SF8hRn.png)
![](https://i.imgur.com/WnhXkfd.png)
![](https://i.imgur.com/MoIVKgr.png)
{% endgi %}

## Figures

### **降水量** 

- `mm of water equivalent`

![<https://www.nmc.cn/publish/precipitation/1-day.html>](https://i.imgur.com/rVe9gY3.png)

![<https://www.nmc.cn/publish/precipitation/1-day.html>](https://i.imgur.com/mker4Mc.png)

## colormap

```log
#ffffff, #cccccc, #a2a2a2, #707070, #474446, #753eee, #4e006f
0.01, 2.5, 5.0, 10.0, 20.0, 30.0
```

# NWP

In NWP models like WRF and MPAS, these three variables represent different physical aspects of snow. Understanding the difference between a **state variable** (what is there now/**instantaneous**) and an **accumulated variable** (what has fallen over time) is key.


| Variable | Full Name | Units | Category | Description |
| --- | --- | --- | --- | --- |
| **`SNOWC`** | Snow Cover | Fraction ($0–1$) | **State** | The horizontal area of the grid cell covered by snow. |
| **`SNOWH`** | Snow Depth | Meters ($m$) | **State** | The actual physical height (thickness) of the snowpack on the ground. |
| **`ACSNOW`** | Accumulated Snow |  $kg/m^2$ (or $mm$) | **Accumulated** | The total amount of snow that has fallen since the model start (Water Equivalent). |

---

## 1. `SNOWC` (Snow Cover)

* **What it represents:** The "horizontal" extent. If `SNOWC` is $0.5$ , it means $50\%$ of that grid box is covered in snow and $50\%$ is bare soil or vegetation.
* **Why it matters:** It is used by the Land Surface Model (LSM) to calculate the **Grid Albedo**. If the ground is only half-covered, the model will blend the high albedo of snow with the lower albedo of the soil.
* **Common trap:** Even if you have a very deep snowpack ($1m$), `SNOWC` can never exceed $1$.

## 2. `SNOWH` (Snow Depth)

* **What it represents:** The "vertical" extent. This is what you would measure with a ruler in your backyard.
* **Dynamic Nature:** Unlike the variables that track water mass, `SNOWH` changes due to **compaction**. Over time, the model (especially Noah-MP) simulates the snow settling under its own weight, so the depth may decrease even if no melting occurs.
* **Note:** If you are comparing model data to weather station observations, this is the variable you want.

## 3. `ACSNOW` (Accumulated Snowfall)

* **What it represents:** The "history" of the run. This is a bucket that collects every snowflake that hits the ground.
* **The Key Difference:**
* **`SNOW` (SWE)** tells you how much snow is on the ground **right now**. If the snow melts, `SNOW` goes down.
* **`ACSNOW`** tells you how much has fallen **in total**. If the snow melts, `ACSNOW` **does not** go down. It only increases.


* **Units:** While it's called "snow," it is almost always stored as **Liquid Water Equivalent**. To get the "accumulated depth" in inches or cm, you would need to divide by a snow-to-liquid ratio (e.g., $10:1$).

## How they work together

Imagine a storm where $10\text{ mm}$ of water equivalent falls as snow.

1. **`ACSNOW`** jumps from  $0$ to $10$
2. **`SNOWH`** jumps from  $0$ to $0.1\text{ m}$ (assuming a $10:1$ ratio).
3. **`SNOWC`** jumps from  $0$ to $1.0$.

Six hours later, if half of that snow melts:

1. **`ACSNOW`** stays at  $10$ (it records the event, not the current state).
2. **`SNOWH`** drops to  $0.05\text{ m}$.
3. **`SNOWC`** might stay at $1.0$ (if the ground is still fully covered) or drop slightly if the melting is patchy.

# WRF

- [How to get hourly snowfall? | 2023](https://forum.mmm.ucar.edu/threads/how-to-get-hourly-snowfall.12370/)
  - If you're looking at a case that **only had snowfall (no liquid precipitation)**, then you can use the value of `RAINC + RAINNC` because these equal total convective and non-convective precipitation (including snow and ice). Otherwise, yes, I believe ACSNOW would be the way to go. It is initialized to zero at the model initial time and then accumulates over time. **Regarding melting, this variable is handled differently in various surface physics schemes.** For whichever surface scheme you're using, you can take a look at specific code in the `WRF/phys/module_sf_<scheme>.F` file (for e.g., if you're using Noah, look in module_sf_noahdrv.F). Another option would be the SNOWH variable, which is the physical snow depth. You would need to subtract the depth at the previous hour so determine the hourly rate.
- [How to calculate ACSNOW | 2021](https://forum.mmm.ucar.edu/threads/how-to-calculate-acsnow.10360/)
  - This is true in general for WRF. `ACSNOW` is **always initialized to be zero at the beginning of the model run**, It is then calculated in various surface schemes . You can find related codes in `phys/module_sf_clm.F`, `phys/module_sf_noahdrv.F`. etc.
  - ![](https://i.imgur.com/xxpXdIR.png){width=600}
- [question about snow in WRF | 2018](https://forum.mmm.ucar.edu/threads/question-about-snow-in-wrf.366/)
  - You can probably use the variable `ACSNOW`, **but you will need to turn it on,** as the registry file does not have it automatically output. There are 2 ways to turn it on