---
layout: post
title: NWP | plot GFS grib files
categories: [NWP]
tags: [MPAS, WRF, NWP, GFS, FNL, IFS, ERA5, CMA]
author: wpsze
date: 2025-09-11 07:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/jzIMNr2.png
banner_img: https://i.imgur.com/jzIMNr2.png
---

# Download subset of GFS

- NCEP GFS Forecasts (0.25 degree grid)
  - <https://nomads.ncep.noaa.gov/gribfilter.php?ds=gfs_0p25>

# Herbie

- <https://herbie.readthedocs.io/en/latest/gallery/noaa_models/gfs.html>
- <https://herbie.readthedocs.io/en/latest/gallery/noaa_models/gefs.html>
- <https://herbie.readthedocs.io/en/latest/gallery/ecmwf_models/ecmwf.html>

```sh
#!/bin/bash

#set -e # stop the shell on first error
#set -u # fail when using an undefined variable
#set -x # echo script lines as they are executed
set -o pipefail


#source /home/wpsze/micromamba/etc/profile.d/micromamba.sh
source /home/wpsze/micromamba/bin/activate herbie-data #micromamba activate venv
export SCRIPT_DIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" >/dev/null 2>&1 && pwd )"

export HERBIE_SAVE_DIR="${SCRIPT_DIR}"

time python plot.py
```

```python
#!/bin/python
# -*- coding: utf-8 -*-

from herbie import Herbie

import cartopy.crs as ccrs
import matplotlib.pyplot as plt
import numpy as np

from herbie import paint
from herbie.toolbox import EasyMap, pc

from datetime import date, timedelta

def daterange(start_date: date, end_date: date):
    days = int((end_date - start_date).days)
    for n in range(days):
        yield start_date + timedelta(n)

start_date = date(2025, 5, 19)
end_date = date(2025, 5, 20)

print(start_date, end_date)

H = Herbie("2025-05-19", model="ifs", product="oper", fxx=00)

# Show the search_help
#print(H.search_help)
#print(H.inventory())
print(H.inventory().param.unique())

#=================================
ds = H.xarray(r":10[u|v]")
print(ds)

ax = EasyMap("50m", crs=ds.herbie.crs, figsize=[10, 10]).STATES().BORDERS().ax

# Set the limits for the plot to focus on China
ax.set_xlim(73, 135)  # Longitude limits for China
ax.set_ylim(18, 54)   # Latitude limits for China

# Add u, v
# Create a quiver plot for u and v wind vectors
Q = ax.quiver(
    ds.longitude[::10],
    ds.latitude[::10],
    ds.u10[::10,::10],  # u-component of wind
    ds.v10[::10,::10],  # v-component of wind
    color='blue',  # Color of the arrows
)

# Optionally, you can add a quiver key (legend) to indicate the scale
qk = ax.quiverkey(Q, 0.9, 0.9, 0.1, '0.1 m/s')

ax.set_title(
    f"{ds.model.upper()}: {H.product_description}\nValid: {ds.valid_time.dt.strftime('%H:%M UTC %d %b %Y').item()}",
    loc="left",
)

plt.savefig(f"./plot/202509/20250919/u10v10.png") #, bbox_inches='tight')
plt.close()

exit()
#=================================
ds = H.xarray(":2t:")
print(ds)

ax = EasyMap("50m", crs=ds.herbie.crs, figsize=[10, 10]).STATES().BORDERS().ax

# Set the limits for the plot to focus on China
ax.set_xlim(73, 135)  # Longitude limits for China
ax.set_ylim(18, 54)   # Latitude limits for China

p = ax.pcolormesh(
    ds.longitude,
    ds.latitude,
    ds.t2m - 273.15,
    transform=pc,
    **paint.NWSTemperature.kwargs2,
)
plt.colorbar(
    p, ax=ax, orientation="horizontal", pad=0.05, **paint.NWSTemperature.cbar_kwargs2
)

ax.set_title(
    f"{ds.model.upper()}: {H.product_description}\nValid: {ds.valid_time.dt.strftime('%H:%M UTC %d %b %Y').item()}",
    loc="left",
)
ax.set_title(ds.t2m.GRIB_name, loc="right")

plt.savefig(f"./plot/202509/20250919/t2m.png")
plt.close()

#================================
# Now lets get the humidity and geopotential height at 500 hPa
ds = H.xarray(":(?:q|gh|u|v):500")

print(ds.longitude)
print(ds.u)

# NOTE: transforming data to a Robinson projection can take some time...
#ax = EasyMap("50m", crs=ccrs.Robinson(), figsize=[10, 10]).STATES().BORDERS().ax
ax = EasyMap("50m", crs=ds.herbie.crs, figsize=[10, 10]).STATES().BORDERS().ax


# Color shade by specific humidity
p = ax.pcolormesh(ds.longitude, ds.latitude, ds.q, transform=pc, cmap="Greens")

plt.colorbar(
    p,
    ax=ax,
    orientation="horizontal",
    pad=0.05,
    label=f"{ds.q.GRIB_name} ({ds.q.units})",
)

# Contours for geopotential height
ax.contour(
    ds.longitude,
    ds.latitude,
    ds.gh,
    transform=pc,
    colors="k",
    linewidths=0.5,
    levels=range(0, 10_000, 60 * 2),
)

# Add a red contour line at gh=5880
ax.contour(
    ds.longitude,
    ds.latitude,
    ds.gh,
    transform=pc,
    colors="red",
    linewidths=2,
    levels=[5880],  # Specify the level for the red contour
)

ax.set_title(
    f"{ds.model.upper()}: {H.product_description}\nValid: {ds.valid_time.dt.strftime('%H:%M UTC %d %b %Y').item()}",
    loc="left",
)
ax.set_title(
    f"{ds.isobaricInhPa.item()} {ds.isobaricInhPa.units}\n{ds.q.GRIB_name}/{ds.gh.GRIB_name}",
    loc="right",
)
plt.savefig(f"./plot/202509/20250919/q_gh_500hPa.png")
plt.close()
```

# Plot GFS grib file **TODO (abandoned)**

- <https://www.nco.ncep.noaa.gov/pmb/codes/nwprod/rap.v5.1.20/sorc/rap_wps.fd/WPSV3.9.1/util/gfs.ncl>

```sh
#!/bin/bash

source /home/wpsze/micromamba/bin/activate venv 
export SCRIPT_DIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" >/dev/null 2>&1 && pwd )"

gfspath="/home/wpsze/mpas/GFS/extract_GFS/0p25/202509/20250910"

export filename="${gfspath}/gfs.t00z.pgrb2.0p25.f003.grb"
export outfilename="gfs.t00z.pgrb2.0p25.f003"

ncl gfs.ncl
```

{% fold info @gfs.ncl %}
```ncl
;***************************************************************************************************************
; This is a sample NCL script to process GRIB data, plot surface variable (SKINTEMP and MSLP) and upper-air 
; variables (U, V, T, RH) in pressure level.  
;
;**************************************************************************************************************
;
load "$NCARG_ROOT/lib/ncarg/nclscripts/csm/gsn_code.ncl"
load "$NCARG_ROOT/lib/ncarg/nclscripts/csm/gsn_csm.ncl"
begin
  fname = getenv("filename")
  grb_file = addfile(fname,"r")
  names = getfilevarnames(grb_file)  ; Get the variable names in the
  print(names)                       ; GRIB file and print them out.

;  atts = getfilevaratts(grb_file,names(0)) ; Get the variable attributes and
;  dims = getfilevardims(grb_file,names(0)) ; dimension names from the GRIB
  pres= grb_file->lv_ISBL0 ;lv_ISBL0
  npres=dimsizes(pres)
  presRH= grb_file->lv_HTGL1 ;lv_ISBL6
  npresRH=dimsizes(presRH)
  soil= grb_file->lv_DBLL3_l0 ;lv_DBLL10_l0
  nsoil=dimsizes(soil)
  wks = gsn_open_wks("png","gfs") ; Open an X11 workstation.
  gsn_define_colormap(wks,"wh-bl-gr-ye-re")

;----------- MCHEN Begin plot -----------------------------------------
  resources             = True
  resources@cnFillOn                    = True                 ; turn on color
  resources@cnLinesOn                   = False                ; no contour lines
  resources@gsnSpreadColors             = True                 ; use full color map
  resources@lbLabelAutoStride           = True                 ; every other label
  resources@cnLevelSelectionMode        = "ManualLevels"       ; manual levels

  do i = 0,dimsizes(names)-1
    names_char = stringtochar(names(i))
; PMSL
    if(names(i).eq."PRMSL_P0_L101_GLL0") then   
      MSLP = grb_file->PRMSL_P0_L101_GLL0
        if(isatt(MSLP,"units").eq.True)  
          resources@tiMainString = "MSLP ( " + MSLP@units  + ")" 
        else
          resources@tiMainString = "PRMSL_P0_L101_GLL0"
        end if               
    plot = gsn_csm_contour_map_ce(wks,MSLP,resources)
    end if
; SKINTEMP
    if(names(i).eq."TMP_P0_L1_GLL0") then   
      SKINTEMP = grb_file->TMP_P0_L1_GLL0
      printVarSummary(SKINTEMP)
        if(isatt(SKINTEMP,"units").eq.True)  
          resources@tiMainString = "SKINTEMP ( " + SKINTEMP@units + ")"
        else
          resources@tiMainString = "TMP_P0_L1_GLL0"
        end if               
    plot = gsn_csm_contour_map_ce(wks,SKINTEMP,resources)
    end if
; T2
    if(names(i).eq."TMP_P0_L103_GLL0") then   
      T2 = grb_file->TMP_P0_L103_GLL0
        if(isatt(T2,"units").eq.True)  
          resources@tiMainString = "T2    (" + T2@units + ")"
        else
          resources@tiMainString = "TMP_P0_L103_GLL0"
        end if               
    plot = gsn_csm_contour_map_ce(wks,T2(0,:,:),resources)
    end if
; U10
    if(names(i).eq."UGRD_P0_L103_GLL0") then   
      U10 = grb_file->UGRD_P0_L103_GLL0
        if(isatt(T2,"units").eq.True)  
          resources@tiMainString = "U10   (" + U10@units + ")"
        else
          resources@tiMainString = "UGRD_P0_L103_GLL0"
        end if               
    plot = gsn_csm_contour_map_ce(wks,U10(0,:,:),resources)
    end if
; V10
    if(names(i).eq."VGRD_P0_L103_GLL0") then   
      V10 = grb_file->VGRD_P0_L103_GLL0
        if(isatt(T2,"units").eq.True)  
          resources@tiMainString = "V10,UNIT: " + V10@units + ")"
        else
          resources@tiMainString = "VGRD_P0_L103_GLL0"
        end if               
    plot = gsn_csm_contour_map_ce(wks,V10(0,:,:),resources)
    end if
;  SOILM in 4 levels
    if(names(i).eq."SOILW_P0_2L106_GLL0") then   
      SOILM = grb_file->SOILW_P0_2L106_GLL0
        if(isatt(SOILM,"units").eq.True)  
          MainString = "SOILM, UNIT:" + SOILM@units + ")"
        end if               
     do nlev=0,nsoil-1 
          resources@tiMainString = MainString + " At " + soil(nlev)  + " m"
       plot = gsn_csm_contour_map_ce(wks,SOILM(nlev,:,:),resources)
     end do
    end if
;  SOILT in 4 levels
    if(names(i).eq."TMP_P0_2L106_GLL0") then   
      SOILT = grb_file->TMP_P0_2L106_GLL0
        if(isatt(SOILT,"units").eq.True)  
          MainString = "SOILT  (" + SOILT@units + ")"
        end if               
     do nlev=0,nsoil-1 
          resources@tiMainString = MainString + " At " + soil(nlev) + " m" 
       plot = gsn_csm_contour_map_ce(wks,SOILT(nlev,:,:),resources)
     end do
    end if
;  T in Pres Level
 ;   if(names_char(0:15).eq."TMP_P0_L100_GLL0") then   
    if(names(i).eq."TMP_P0_L100_GLL0") then   
      T = grb_file->TMP_P0_L100_GLL0
      printVarSummary(T)
        if(isatt(T,"units").eq.True)  
          MainString = "TEMPERATURE ( " + T@units + ")"
        end if               
     do nlev=0,npres-1 
          resources@tiMainString = MainString + " At " + pres(nlev)/100 +"hpa"
       plot = gsn_csm_contour_map_ce(wks,T(nlev,:,:),resources)
     end do
    end if
;  RH in Pres Level
  ;  if(names_char(0:14).eq."RH_P0_L100_GLL0") then   
    if(names(i).eq."RH_P0_L100_GLL0") then   
      RH = grb_file->RH_P0_L100_GLL0
        if(isatt(RH,"units").eq.True)  
          MainString = "RH  (" + RH@units  + ")"
        end if               
     do nlev=0,npresRH-1 
          resources@tiMainString = MainString + " At " + presRH(nlev)/100 +"hpa"
       plot = gsn_csm_contour_map_ce(wks,RH(nlev,:,:),resources)
     end do
    end if
; UGRD in pressure
   ; if(names_char(0:16).eq."UGRD_P0_L100_GLL0") then   
    if(names(i).eq."UGRD_P0_L100_GLL0") then   
      U = grb_file->UGRD_P0_L100_GLL0
        if(isatt(U,"units").eq.True)  
          MainString = "U  ( " + U@units + ")"
        end if               
     do nlev=0,npres-1 
          resources@tiMainString = MainString + " At " + pres(nlev)/100 +"hpa"
       plot = gsn_csm_contour_map_ce(wks,U(nlev,:,:),resources)
     end do
    end if
; VGRD in pressure
  ;  if(names_char(0:11).eq."VGRD_P0_L100") then   
    if(names(i).eq."VGRD_P0_L100_GLL0") then   
      V = grb_file->VGRD_P0_L100_GLL0
        if(isatt(V,"units").eq.True)  
          MainString = "V ( " + V@units  + ")"
        end if               
     do nlev=0,npres-1 
          resources@tiMainString = MainString + " At " + pres(nlev)/100 +"hpa"
       plot = gsn_csm_contour_map_ce(wks,V(nlev,:,:),resources)
     end do
    end if
    delete(names_char)
  end do                        
end
```
{% endfold %}