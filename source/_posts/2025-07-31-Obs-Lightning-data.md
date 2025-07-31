---
layout: post
title: Obs | Lightning data (閃電位置資訊)
categories: [WRF]
tags: [NWP, WRF-ELEC, MPAS, Lightning, HKO, GBA]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://upload.wikimedia.org/wikipedia/commons/thumb/9/9f/Lighting_barrage.jpg/1280px-Lighting_barrage.jpg
banner_img: https://upload.wikimedia.org/wikipedia/commons/thumb/c/c2/Port_and_lighthouse_overnight_storm_with_lightning_in_Port-la-Nouvelle.jpg/1920px-Port_and_lighthouse_overnight_storm_with_lightning_in_Port-la-Nouvelle.jpg
---

# HKO

## hourly_count

- <https://www.hko.gov.hk/tc/wxinfo/llis/gm/llis_area_counts.htm>

![](https://i.imgur.com/omL6Q2y.png){width=500}

- <https://www.hko.gov.hk/wxinfo/llis/gm/hourly_count>

```sh
#!/bin/bash
tmp_dir=$(date +'%Y-%m')
mkdir -p ${tmp_dir}
wget -O "${tmp_dir}/$(date +'%Y-%m-%d').csv" https://www.hko.gov.hk/wxinfo/llis/gm/hourly_count
```

setup **crontab -e**,

```sh
# For HK region's lightning, 10am everyday,
00 10 * * * cd /home/wpsze/hk_data/hko-lightning; sh run_wget.sh 
```

### data

- 雲對地閃電					雲間閃電				
  - 全港總次數	新界西	新界東	港島及九龍	大嶼山	全港總次數	新界西	新界東	港島及九龍	大嶼山

```txt
2025072510 0 0 0 0 0 0 0 0 0 0
2025072511 0 0 0 0 0 0 0 0 0 0
2025072512 0 0 0 0 0 0 0 0 0 0
2025072513 2 2 0 0 0 3 3 0 0 0
2025072514 5 2 3 0 0 24 6 14 4 0
2025072515 1 0 1 0 0 8 1 7 0 0
2025072516 0 0 0 0 0 0 0 0 0 0
2025072517 21 0 21 0 0 26 0 26 0 0
2025072518 1525 0 1521 4 0 2346 1 2340 5 0
2025072519 1217 137 788 141 151 2878 376 2075 254 173
2025072520 159 85 19 6 49 1609 668 135 152 654
2025072521 1 0 0 0 1 39 0 0 0 39
2025072522 0 0 0 0 0 0 0 0 0 0
2025072523 0 0 0 0 0 0 0 0 0 0
2025072600 0 0 0 0 0 0 0 0 0 0
```

## 閃電位置資訊

### HKO webpage

- <https://www.hko.gov.hk/tc/wxinfo/llis/gm_index.htm>

![](https://i.imgur.com/wiT0HXr.png){width=500}

- <https://www.hko.gov.hk/wxinfo/llis/gm/gislatest_c2g>
- <https://www.hko.gov.hk/wxinfo/llis/gm/gislatest_c2c>
- <https://www.hko.gov.hk/wxinfo/llis/gm/4region_running_60.txt>
- <https://www.hko.gov.hk/wxinfo/llis/gm/lightning_count_4region_status.txt>
- <https://www.hko.gov.hk/wxinfo/llis/gm/latestReadings_AWS1>

where `c2g` is cloud-to-ground, `c2c` is clould-to-cloud.

```sh
#!/bin/bash
tmp_dir=$(date +'%Y-%m-%d')
mkdir -p ${tmp_dir}/c2g/
mkdir -p ${tmp_dir}/c2c/
wget -O "${tmp_dir}/c2g/c2g_$(date +'%Y-%m-%d_%H-%M-%S').csv" https://www.hko.gov.hk/wxinfo/llis/gm/gislatest_c2g
wget -O "${tmp_dir}/c2c/c2c_$(date +'%Y-%m-%d_%H-%M-%S').csv" https://www.hko.gov.hk/wxinfo/llis/gm/gislatest_c2c
```

setup **crontab -e**,

```sh
# For GBA region's lightning,
0,30 * * * * cd /home/wpsze/hk_data/hko-lightning/GBA; sh run_wget.sh
```

### 粵.港.澳大灣區天氣網站

- <https://www.gbaweather.net/tc/>
  
![](https://i.imgur.com/N3M56AY.png){width=500}

{% note primary %}
seems same as HKO webpage above.
{% endnote %}

