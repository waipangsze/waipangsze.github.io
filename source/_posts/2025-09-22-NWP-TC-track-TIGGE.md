---
layout: post
title: NWP | Tropical Cyclone Track Data | TIGGE
categories: [NWP]
tags: [MPAS, WRF, NWP, GFS, FNL, IFS, ERA5, CMA, TIGGE, TC, tracker]
author: wpsze
date: 2025-09-22 07:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/tvYJYOV.png
banner_img: https://i.imgur.com/tvYJYOV.png
---

[NWP | TC Best Track | IBTrACS](https://waipangsze.github.io/2025/10/30/NWP-TC-Best-Track-IBTrACS/)

---

# TIGGE Project

`TIGGE`, the **THORPEX Interactive Grand Global Ensemble**, is a key component of THORPEX: a World Weather Research Programme to accelerate the improvements in the accuracy of 1-day to 2 week high-impact weather forecasts for the benefit of humanity. Centralized archives of ensemble model forecast data, from many international centers, will be used to enable extensive data sharing and research.

- [The Observing System Research and Predictability Experiment (THORPEX)](https://community.wmo.int/en/wwrp-thorpex)
- <http://gpvjma.ccs.hpcc.jp/TIGGE/>

## Cyclone Exchange

- <https://www.cawcr.gov.au/research/cyclone-exchange/>

A pilot project has been established by the `TIGGE` Working Group to test the real-time exchange of ensemble TC track forecasts; the format for TC data exchange will be Cyclone XML (`CXML`). Exchange of real-time TC forecasts was done in the `THORPEX` Pacific-Asian Regional Campaign (T-PARC) (August 2008-March 2009), designed to study the life-cycle of tropical and extra-tropical cyclones over the northern Pacific.

# NCAR archive

- <https://gdex.ucar.edu/datasets/d330003/>

![](https://i.imgur.com/NETf6SR.png){width=400}

```gdex-download.csh 
#!/usr/bin/env csh
#
# c-shell script to download selected files from gdex.ucar.edu using Wget
# NOTE: if you want to run under a different shell, make sure you change
#       the 'set' commands according to your shell's syntax
# after you save the file, don't forget to make it executable
#   i.e. - "chmod 755 <name_of_script>"
#
# Experienced Wget Users: add additional command-line flags to 'opts' here
#   Use the -r (--recursive) option with care
#   Do NOT use the -b (--background) option - simultaneous file downloads
#       can cause your data access to be blocked
set opts = "-N"
#
# Check wget version.  Set the --no-check-certificate option 
# if wget version is 1.10 or higher
set v = `wget -V |grep 'GNU Wget ' | cut -d ' ' -f 3`
set a = `echo $v | cut -d '.' -f 1`
set b = `echo $v | cut -d '.' -f 2`
if(100 * $a + $b > 109) then
  set cert_opt = "--no-check-certificate"
else
  set cert_opt = ""
endif

set filelist= ( \
  https://osdf-data.gdex.ucar.edu/ncar/gdex/d330003/ecmf/2025/20250921/z_tigge_c_ecmf_20250921000000_ifs_glob_prod_all_glo.xml \
)
while($#filelist > 0)
  set syscmd = "wget $cert_opt $opts $filelist[1]"
  echo "$syscmd ..."
  $syscmd
  shift filelist
end
```

# ECMWF 

- <https://confluence.ecmwf.int/display/TIGGE/Tools#Tools-tc>
- ECMWF Tropical Cyclone Track Data (XML format) 
  - Be aware that the links below might work depending on the browser used. Authentification using provided user ID and password can be needed depending on the browser and user location.
- FTP: <ftp://tigge_glob_tc:tgtc2018@acq.ecmwf.int>
- WEB: <https://acq.ecmwf.int>  (user: **tigge_glob_tc**, password: **tgtc2018**)

![CMWF Tropical Cyclone Track Data (hosted by ECMWF)](https://i.imgur.com/CkTVTQ9.png){width=400}

![ftp://tigge_glob_tc:tgtc2018@acq.ecmwf.int](https://i.imgur.com/EJPsonl.png){width=400}

## By bash shell

```sh
wget --user=tigge_glob_tc --password=tgtc2018 -c https://acq.ecmwf.int/ecpds/data/file/2025/09/z_tigge_c_ecmf_20250921000000_ifs_glob_prod_all_glo.xml
```

# Best Track

1. [**International Best Track Climatology Archive for Tropical Cyclone Data**](https://www.ncei.noaa.gov/products/international-best-track-archive)
   1. `IBTrACS Version 4r01`
2. [Global Tropical Cyclone "Best Track" Position and Intensity Data](https://gdex.ucar.edu/datasets/d824001/)
