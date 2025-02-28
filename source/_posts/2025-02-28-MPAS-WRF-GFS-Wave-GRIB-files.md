---
layout: post
title: MPAS | WRF | GFS-Wave GRIB files
categories: [MPAS]
tags: [MPAS, NWP, WRF, ERA5, GFS, Wave]
author: wpsze
date: 2025-02-28 14:56:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/s7kzxk9.png
banner_img: https://i.imgur.com/s7kzxk9.png
---

- Read [**MPAS | WRF | Extract subset of GFS GRIB files**](https://waipangsze.github.io/2025/02/19/MPAS-WRF-Extract-GFS-GRIB-files/)

# GFS Wave Model Forecast

- These models are based on the **3rd generation wave model WAVEWATCH III** using operational NCEP products as input.
- [WAVEWATCH III® Production Multigrid: July 2013 to present](https://polar.ncep.noaa.gov/waves/validation/)
  - The multigrid production uses the multi-grid spectral wave model WAVEWATCH III® with operational NCEP winds and ice fields as input forcing fields. No wave data assimilation is performed. The model is run four times a day, with all available data. Validation data is available from July 2013 through to the present.
  - Model output
    - Field output in grib2 format, available every 3 hours
      - .wind. = U-component of 10m wind, V-component of 10m wind (m/s)
      - .hs. = significant height of combined wind waves and swell (m)
      - .tp. = primary wave mean period (s)
      - .dp. = primary wave direction (degrees true, i.e. 0 deg => coming from North; 90 deg => coming from East)

## GRiB2 Output Parameters

From <https://polar.ncep.noaa.gov/waves/Model_Description.pdf>,

- Significant Height of Combined Wind Waves and Swell (HTSGW)
- Direction of Combined Wind Waves and Swell (DIRPW)
- Mean Period of Combined Wind Waves and Swell (PERPW)
- Significant Height of Wind Waves (WVHGT)
- Mean Period of Wind Waves (WVPER)
- Direction of Wind Waves (WVDIR)
- Significant wave height of first swell partition (SWELL: 1 in sequence)
- Mean wave period of first swell partition (SWPER: 1 in sequence)
- Mean wave direction of first swell partition (SWDIR: 1 in sequence)
- Significant wave height of second swell partition (SWELL: 2 in sequence)
- Mean wave period of second swell partition (SWPER: 2 in sequence)
- Mean wave direction of second swell partition (SWDIR: 2 in sequence)
- Significant wave height of third swell partition (SWELL: 3 in sequence)
- Mean wave period of third swell partition (SWPER: 3 in sequence)
- Mean wave direction of third swell partition (SWDIR: 3 in sequence)
- Wind Speed (WIND)
- Wind Direction (WDIR)
- U-component of wind (UGRD)
- V-component of wind (VGRD)

The multi_1 deterministic wave model is now unified with the Global Forecast System (GFS). The WAVEWATCH III wave model is one-way coupled with the atmospheric forecast model. The wave model uses three native computational grids:

- Arctic Polar 9km resolution from 50N to 90N,
- Global Core 10min resolution from 15S to 52N and
- Southern Ocean 15min resolution from 10.5S to 79.5S.

There are **4 post-processed grids**: **West Coast, Eastern Pacific, Atlantic Ocean all with 0.16deg resolution** and **a Global grid with 0.25 deg of resolution**.

- **The unification into GFS allows for an increase in frequency of the wind forcing from 3 hours to 30 minutes**. In addition, **ocean** currents from the `RTOFS` model are included as input to the wave model. The system runs four cycles per day (00, 06, 12 and 18Z) and the wave forecast has been extended from 180 hours to 384 hours. The wave model produces **hourly forecasts from 000 to 120 hours and every 3 hours from 120 to 384 hrs**.

# GFS-Wave subset download

## NOMADS Data at NCEP

- <https://nomads.ncep.noaa.gov/>
- <https://nomads.ncep.noaa.gov/gribfilter.php?ds=gfswave>
- Example:
  ```URL
  https://nomads.ncep.noaa.gov/cgi-bin/filter_gfswave.pl?dir=%2Fgfs.20250228%2F00%2Fwave%2Fgridded&file=gfswave.t00z.global.0p16.f048.grib2&var_DIRPW=on&var_HTSGW=on&var_PERPW=on&var_SWDIR=on&var_SWELL=on&var_SWPER=on&var_UGRD=on&var_VGRD=on&var_WDIR=on&var_WIND=on&var_WVDIR=on&var_WVHGT=on&var_WVPER=on&lev_1_in_sequence=on&lev_2_in_sequence=on&lev_3_in_sequence=on&lev_surface=on
  ```
- Dowload script:
    {% fold info @download_gfs_wave.sh %}
    ```sh
    #!/bin/bash
    #
    # define URL
    #
    workdir=/home/wpsze/mpas/GFS/GFS_Wave/
    hr=00
    #date_st=20240920
    date_st=$(date -d "today -1 days" +%Y%m%d)

    #wget 

    for iday in {0..0..1}; do
        
        idate=$(date -d "$date_st + $iday days" +%Y%m%d)
        echo $date_st $idate
    for fhr in {000..120..1}; do
    #for fhr in {076..76..1}; do


    URL="https://nomads.ncep.noaa.gov/cgi-bin/filter_gfswave.pl?\
    dir=%2Fgfs.${idate}%2F${hr}%2Fwave%2Fgridded&\
    file=gfswave.t${hr}z.global.0p16.f${fhr}.grib2\
    &var_DIRPW=on&var_HTSGW=on&var_PERPW=on&var_SWDIR=on&\
    var_SWELL=on&var_SWPER=on&var_UGRD=on&var_VGRD=on&\
    var_WDIR=on&var_WIND=on&var_WVDIR=on&var_WVHGT=on&\
    var_WVPER=on&lev_1_in_sequence=on&lev_2_in_sequence=on\
    &lev_3_in_sequence=on&lev_surface=on"

    yyyymm=${idate:0:6}
    datapath=$workdir/0p25/$yyyymm/$idate/
    mkdir -p $datapath

    # download file
    #curl "$URL" -o download_test$fhr.grb
    wget "$URL" -O $datapath/gfs.t${hr}z.global.0p16.f${fhr}.grib2
    # add a sleep to prevent a denial of service in case of missing file
    sleep 3
    done
    done
    ```
    {% endfold %}

![https://nomads.ncep.noaa.gov/gribfilter.php?ds=gfswave](https://i.imgur.com/dBuFGe4.png){width=600}

# grib checking

- `grib_ls gfs.t00z.global.0p16.f000.grib2`
  {% fold info @gfs.t00z.global.0p16.f000.grib2 %}
  ```console
  gfs.t00z.global.0p16.f000.grib2
  edition      centre       date         dataType     gridType     stepRange    typeOfLevel  level        shortName    packingType  
  2            kwbc         20250227     fc           regular_ll   0            surface      1            ws           grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            surface      1            wdir         grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            surface      1            u            grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            surface      1            v            grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            surface      1            swh          grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            surface      1            perpw        grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            surface      1            dirpw        grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            surface      1            shww         grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            orderedSequenceData  1            swell        grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            orderedSequenceData  2            swell        grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            orderedSequenceData  3            swell        grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            surface      1            mpww         grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            orderedSequenceData  1            swper        grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            orderedSequenceData  2            swper        grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            orderedSequenceData  3            swper        grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            surface      1            wvdir        grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            orderedSequenceData  1            swdir        grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            orderedSequenceData  2            swdir        grid_jpeg   
  2            kwbc         20250227     fc           regular_ll   0            orderedSequenceData  3            swdir        grid_jpeg   
  19 of 19 messages in gfs.t00z.global.0p16.f000.grib2

  19 of 19 total messages in 1 files
  ```
  {% endfold %}
- re-download dataset that only contains surface variables.
- `grib_to_netcdf gfs.t00z.global.0p16.f000.grib2 -o test.nc`
- `ncvis/ncview test.nc`

{% gi 2 2%}
![](https://i.imgur.com/lflKrCy.png)
![](https://i.imgur.com/PBF8xwC.png)
{% endgi%}

{% gi 7 4-3 %}
![](https://i.imgur.com/qQ6P9fx.png)
![](https://i.imgur.com/HKGKoVg.png)
![](https://i.imgur.com/5svtQWO.png)
![](https://i.imgur.com/1YftA55.png)
![](https://i.imgur.com/s7kzxk9.png)
![](https://i.imgur.com/qvL4hpP.png)
![](https://i.imgur.com/Y0gEygu.png)
{% endgi %}


# Reading

- [兩個海波數值模式的比較 | HKO](https://www.hko.gov.hk/hko/publica/reprint/r136.pdf)
- [WAM 模式之評介 | 交通部中央氣象署](https://photino.cwa.gov.tw/rdcweb/lib/cd/cd01conf/dissertation/1994%20Sea/11.pdf)
- [台灣海域作業化 WAVEWATCH III 湧浪數值預報建置 | 2011](https://www.comc.ncku.edu.tw/wordpress/wp-content/uploads/2023/02/100%E5%B9%B4%E5%BA%A6-%E5%8F%B0%E7%81%A3%E6%B5%B7%E5%9F%9F%E4%BD%9C%E6%A5%AD%E5%8C%96WAVEWATCH-III%E6%B9%A7%E6%B5%AA%E6%95%B8%E5%80%BC%E9%A0%90%E5%A0%B1%E5%BB%BA%E7%BD%AE.pdf)
  - ![](https://i.imgur.com/esFWqXd.png){width=500}

# Literature review

- [Liu, Qingxiang, et al. "Observation-based source terms in the third-generation wave model WAVEWATCH III: Updates and verification." Journal of Physical Oceanography 49.2 (2019): 489-517.](https://journals.ametsoc.org/downloadpdf/view/journals/phoc/49/2/jpo-d-18-0137.1.pdf)
- [曹赛超, 高志一, 赵栋梁. WAVEWATCH Ⅲ第三代海浪数值模式在中国东海的应用和改进[J]. 中国海洋大学学报(自然科学版), 2023, 53(8): 8-15.](https://html.rhhz.net/ZGHYDXXBZRKXB/html/a972b1fe-e8d2-4f30-aa37-0d825e44073e.htm)
- [林幼淳、林鼎傑、許泰文（2024）。以數值模式為基礎探討颱風季的波浪潛能。海岸及海洋工程學刊，20(1&2)，75-84。https://doi.org/10.6266/JOCOE.202411_20(1_2).0006](https://www.airitilibrary.com/Article/Detail/P20231229001-N202411060008-00006)
- [WAVEWATCH III 多重網格模式應用於颱風波浪推算之研究 | 2017](https://www.comc.ncku.edu.tw/wordpress/wp-content/uploads/2023/02/106%E5%B9%B4%E5%BA%A6-WAVEWATCH-III-%E5%A4%9A%E9%87%8D%E7%B6%B2%E6%A0%BC%E6%A8%A1%E5%BC%8F%E6%87%89%E7%94%A8%E6%96%BC%E9%A2%B1%E9%A2%A8%E6%B3%A2%E6%B5%AA%E6%8E%A8%E7%AE%97%E4%B9%8B%E7%A0%94%E7%A9%B6.pdf)