---
layout: post
title: "GFS/FNL/RDA upgrade (2024)"
categories: [NWP]
tags: [WRF,WPS,MPAS,IC/BC,GFS,FNL,RDA,NCEP]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2024-08-16 15:24:00
index_img: https://rda.ucar.edu/images/ds_logos/gfs4c_452_432.png
banner_img: https://rda.ucar.edu/images/ds_logos/gfs4c_452_432.png
---

# RDA team

RDA - Research Data Archive

## RDA Data Services Unavailable July 30, 2024
- July 26, 2024 Posted by: RDA Team

Update on July 31, 2024: standard data file download, THREDDS access, and Globus transfer services have been restored. We continue to resolve issues with data search and subsetting services. Thank you for your patience during this maintenance period.

Due to scheduled updates on the NSF NCAR Research Data Archive, all RDA data access services, including downloads, data subsetting, and THREDDS data server access, will be unavailable July 30, 2024 beginning at 9:00 AM MDT.  We anticipate services to be restored near the end of the day July 30. Please follow our Twitter profile (@NCAR_RDA) for updates.

The primary purpose of the downtime is to update the dataset numbering system for data collections archived in the RDA. After the update, **all RDA datasets will be identified by a dataset number in the format 'dnnnnnn', where 'nnnnnn' is the serial number used to identify a dataset. Until now, dataset numbers have been in the format 'dnnn.n'. This format will be converted to the new format 'dnnn00n', in which the first three digits of the old format are placed in the first three positions of the new format, and the last digit of the old format will be placed in the last position of the new format. For example, the dataset ID 'ds094.1' will be converted to 'd094001', and the dataset ID 'ds351.0' will be converted to 'd351000'.** Please contact us at rdahelp@ucar.edu if you have questions regarding this update.

## Changes

Goto <https://waipangsze.github.io/2023/04/27/GFS_FNL/>

- old one: https://rda.ucar.edu/data/ds084.1/ 
- new one: https://data.rda.ucar.edu/d084001/

# 2019年与2020年GFS资料驱动wrf的差异

- [笔记 | 2019年与2020年GFS资料驱动wrf的差异](https://mp.weixin.qq.com/s/ICJ59yUE2D3UnFlPngZRJA)
  - GFS是美国国家环境预报中心 (NCEP) 运行的全球数值天气预报系统。GFS系统会定期进行版本更新，引入新的物理过程、更高的分辨率、更多垂直层次或改进同化方案等，这些更新可能导致输出资料的格式、包含的变量或垂直层次结构发生变化。
  - GFS v15 版本于2019年6月上线，而 GFS v16 版本于2021年3月上线。因此，2019年的GFS资料主要对应 GFS v15 或之前的版本（取决于具体获取时间），而2020年的资料则主要对应 GFS v16（在v16上线前可能是v15）。WRF的前处理系统（WPS）需要正确识别和处理输入气象资料的格式和变量。GFS版本之间的差异很可能影响了WPS处理不同年份GFS资料所需的配置。特别是GFS v16相较于早期版本，在垂直分辨率等方面有所提升。
  - 2019 GFS 资料处理
    - num_metgrid_levels = 32: 指定了metgrid程序需要处理的垂直层次数量。对于2019年的GFS资料，只能设置为32。
    - sfcp_to_sfcp = .true. 和 use_surface = .true.：作者指出，2019年的GFS资料可能缺乏某些地面或近地面变量，或者其格式使得WPS难以自动识别地面信息。设置这两个参数为.true. 可以强制metgrid使用可用的地面气压（PSFC）等信息进行垂直插值和网格映射，确保地面数据的正确处理。
    - 常见错误：
      - 如果在处理2019年GFS资料时没有添加sfcp_to_sfcp = .true. 和 use_surface = .true.，WPS可能会报错
      - 这个错误提示metgrid在需要使用地面气压（PSFC）时发现sfcp_to_sfcp标志为.false.。这也印证了对于2019年数据，需要明确告诉metgrid如何处理地面气压。
      - 网上有帖子也提到，类似错误有时也可能由其他气象资料（如ECMWF数据）的地面和高空资料间隔不一致引起。
  - 2020年 GFS 资料处理
    - num_metgrid_levels = 34: 对于2020年的GFS资料，WPS期望处理的垂直层次数量为34。这可能了2020年使用的GFS版本（ likely GFS v16 data format）在输出资料中提供了更多的垂直层次。
    - sfcp_to_sfcp 和 use_surface: 这两项在处理2020年GFS资料时需要移除或设置为.false.。这说明2020年的GFS资料格式对于WPS（WRF 4.2.2版本）来说，其地面信息的组织方式使得WPS能够更顺畅地自动处理，不再需要额外的强制设置。
    - 常见错误：
      - 如果在处理2020年GFS资料时仍然沿用2019年的设置（特别是num_metgrid_levels = 32 或保留了sfcp_to_sfcp = .true. 和 use_surface = .true.），可能会遇到以下问题：
        - 层次错误： 如果num_metgrid_levels设置小于资料实际包含的层次，或者与WPS对该版本资料的期望不符，会导致错误。将num_metgrid_levels设置为34与2020年GFS资料的特性相符。
        - 边界条件相关错误： 可能出现 "Maybe this is a global domain, but the polar flag was not set in the bdy_control namelist." 这样的错误。虽然这个错误通常与全球模拟域或极区处理有关，但在本例中，可能是由于错误的数据处理参数（如保留了不必要的sfcp_to_sfcp等）干扰了WPS正确识别数据类型或进行内插，间接导致了与边界条件处理相关的逻辑错误。

