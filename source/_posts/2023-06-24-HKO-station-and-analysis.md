---
layout: post
title: HKO station and analysis
categories: [NWP]
tags: [HKO, obs, WRF,  MPAS]
author: wpsze
index_img: 
banner_img: 
---

# Download HKO weather dataset
From [i-lens.hk 香港自動氣象站氣候觀測資料庫](https://i-lens.hk/hkweather/)
```sh
export yyyy=2023
export mm=04

for dd in {14..16}; do
    wget http://i-lens.hk/hkweather/history_minute_record/${yyyy}/${yyyy}${mm}${dd}.zip ;
    echo $mm$dd sleeping now ;
    #mkdir -p $yyyy
    #mv ${yyyy}${mm}${dd}* ./${yyyy} ;
    #sleep $((80 + RANDOM % 21)) ;
done
```
Subsequently, extract those elements.

# List of HKO station

As part of the HKO stations, the majority of the names I use align with the definitions provided by HKO. However, there are a few instances where I have personally assigned names to certain elements.

```sh
ID,lat,lon,station,station_en,station_ch
1,22.301944,114.174167,hko,HKO (Tsim Sha Tsui),香港天文台
2,22.201111,114.026667,cch,Cheung Chau,長洲
3,22.263389,114.299769,cwb,Clear water bay,清水灣
4,22.309444,113.921944,hka,HK Airport,香港國際機場
5,22.278333,114.162222,hkp,Hong Kong Park,香港公園
6,22.247778,114.173611,hks,Wong Chuk Hang,黃竹坑
7,22.270556,114.183611,hpv,Happy Valley,跑馬地
8,22.315833,114.255556,jkb,Tseung Kwan O,將軍澳
9,22.335000,114.184722,klt,Kowloon City,九龍城
10,22.311944,114.172778,kp,King’s Park,京士柏
11,22.370278,114.312500,ksc,Kau Sai Chau,滘西洲
12,22.318611,114.224722,ktg,Kwun Tong,觀塘
13,22.468889,113.983611,lfs,Lau Fau Shan,流浮山
14,22.258611,113.912778,ngp,Ngong Ping,昂坪
15,22.291111,114.043333,pen,Peng Chau,坪洲
16,22.475278,114.237500,plc,Tai Mei Tuk,大美督
17,22.304722,114.217222,se1,Kai Tak Runway Park,啟德跑道公園
18,22.436111,114.084722,sek,Shek Kong,石崗
19,22.402500,114.210000,sha,Sha Tin,沙田
20,22.375556,114.274444,skg,Sai Kung,西貢
21,22.281667,114.236111,skw,Shau Kei Wan,筲箕灣
22,22.501944,114.111111,ssh,Sheung Shui,上水
23,22.335833,114.136944,ssp,Sham Shui Po,深水埗
24,22.214167,114.218611,sty,Stanley,赤柱
25,22.357778,114.217778,tc,Tate's Cairn,大老山
26,22.528611,114.156667,tkl,Ta Kwu Ling,打鼓嶺
27,22.483480,114.117400,tl,Tai Lung,大隴
28,22.410556,114.124444,tms,Tai Mo Shan,大帽山
29,22.446111,114.178889,tpo,Tai Po,大埔
30,22.385833,113.964167,tu1,Tuen Mun Children and Juvenile Home,屯門兒童及青少年院
31,22.375556,114.126667,tw,Shing Mun Valley,荃灣城門谷
32,22.383611,114.107778,twn,Tsuen Wan,荃灣
33,22.344167,114.110000,ty1,Tsing Yi Station,新青衣站
34,22.402778,114.323056,tyw,Pak Tam Chung,北潭涌(鯽魚湖)
35,22.264167,114.155000,vp1,The Peak,山頂
36,22.182222,114.303333,wgl,Waglan Island,橫瀾島
37,22.466667,114.008889,wlp,Wetland Park,濕地公園
38,22.339444,114.205278,wts,Wong Tai Sin,黃大仙
39,22.440833,114.018333,ylp,ylp,ylp
```

# Jupter Notebook or python

```python
#!/usr/bin/env python
# coding: utf-8

# In[1]:


import glob, os
import numpy as np
import xarray as xr
import pandas as pd
from datetime import timedelta

import matplotlib.pyplot as plt
from matplotlib import cm
SMALL_SIZE = 8
MEDIUM_SIZE = 10
BIGGER_SIZE = 24

plt.rcParams["figure.figsize"] = (10,8)
plt.rc('font', size=BIGGER_SIZE)          # controls default text sizes
plt.rc('axes', titlesize=BIGGER_SIZE)     # fontsize of the axes title
plt.rc('axes', labelsize=BIGGER_SIZE)    # fontsize of the x and y labels
plt.rc('xtick', labelsize=BIGGER_SIZE)    # fontsize of the tick labels
plt.rc('ytick', labelsize=BIGGER_SIZE)    # fontsize of the tick labels
plt.rc('legend', fontsize=BIGGER_SIZE)    # legend fontsize
plt.rc('figure', titlesize=BIGGER_SIZE)  # fontsize of the figure title

# plt.rcParams['font.sans-serif'] = ['SimHei'] 
# plt.rcParams['axes.unicode_minus'] = False

plt.rcParams.update({'font.family':'sans-serif'})
plt.rcParams['font.sans-serif'] = ['DejaVu Sans']

# from IPython.core.interactiveshell import InteractiveShell
# InteractiveShell.ast_node_interactivity = "all"


# # Reading all dataset

# In[2]:


select_year = "202304"

station = pd.read_csv(r'./HKO-station.csv', header=0) # nearwater or inland
select_num = 7
#======================================== station =============================================
select_station = station["station"][select_num]
select_station_en = station["station_en"][select_num]
select_station_ch = station["station_ch"][select_num]
file_name_t2m = select_station+"_"+select_year
file_name_wind = select_station+"_wd_"+select_year
file_name_pressure = select_station+"_pre_"+select_year
print(select_num, select_station, select_station_en, select_station_ch)
station


# In[3]:


#========================================= mpas ================================================
mpas_station_extraction_dir="/xxx/xxx/station_xxxx.csv"
df_mpas = pd.read_csv(mpas_station_extraction_dir)
# .dt.tz_localize(None): to remove timezone from a Timestamp column in a pandas dataframe
df_mpas["Date(UTC)"] = pd.to_datetime(df_mpas["xtime[UTC]"], utc="false").dt.tz_localize(None)
df_mpas["Date(HKT)"] = df_mpas["Date(UTC)"] + timedelta(hours=8)

df_select_mpas = df_mpas[df_mpas["station"] == select_station]
df_select_mpas = df_select_mpas.reset_index(drop = True)

#=========================================== HKO t2m ==============================================
obs_file = []
for file in glob.glob(f"/home/wpsze/hk_data/{select_year}*/{file_name_t2m}*"):
    obs_file.append(file)
print(obs_file)
df = pd.concat((pd.read_csv(f, header = 0, na_values=['nan'], keep_default_na=True) for f in obs_file))
df_hko_obs = df.drop_duplicates()
df_hko_obs = df_hko_obs.reset_index(drop = True)

df_hko_obs["Date(HKT)"] = pd.to_datetime(df_hko_obs["Date"]) 
df_hko_obs["Date(UTC)"] = df_hko_obs["Date(HKT)"] + timedelta(hours=-8)
df_hko_obs = df_hko_obs.reset_index(drop = True)
df_hko_obs
#=========================================================================================
df_combine = pd.merge(df_select_mpas, df_hko_obs)
#=========================================== HKO wind ==============================================
obs_file = []
for file in glob.glob(f"/home/home/wpsze/hk_data/{select_year}*/{file_name_wind}*"):
    obs_file.append(file)
print(obs_file)
df = pd.concat((pd.read_csv(f, header = 0, na_values=['nan'], keep_default_na=True) for f in obs_file))
df_hko_obs = df.drop_duplicates()
df_hko_obs = df_hko_obs.reset_index(drop = True)
df_hko_obs

df_hko_obs["Date(HKT)"] = pd.to_datetime(df_hko_obs["Date"]) 
df_hko_obs["Date(UTC)"] = df_hko_obs["Date(HKT)"] + timedelta(hours=-8)
df_hko_obs["obs-wspd[m/s]"] = df_hko_obs["Wind Spd"]*0.514444444 # knt to m/s
df_hko_obs["obs-wdir[deg]"] = df_hko_obs[" Wind Dir"]
df_hko_obs = df_hko_obs.reset_index(drop = True)
df_hko_obs
#=========================================================================================
df_combine = pd.merge(df_combine, df_hko_obs)
#=========================================================================================
spin_up_period = 72
df_combine_skip_spinup = df_combine[spin_up_period:]
df_combine_skip_spinup = df_combine_skip_spinup.reset_index(drop = True)
df_combine_skip_spinup


# In[4]:


df_combine_final = df_combine_skip_spinup[df_combine_skip_spinup["obs-wspd[m/s]"]<100] 
df_combine_final = df_combine_final.reset_index(drop = True)
df_combine_final


# # For t2m

# In[33]:


plt.plot(df_combine_final["Date(HKT)"], df_combine_final["Temp"], 'ko-', label="Obs")
plt.plot(df_combine_final["Date(HKT)"], df_combine_final["t2m[degC]"],'bo-', label="mpas")
plt.grid(zorder=0)
plt.xlabel("Date Time (HKT)")
plt.ylabel("T2m [deg C]")
plt.title(select_station_en)
plt.xticks(rotation=45)
plt.legend()
plt.tight_layout()
plt.show()
plt.close()

# df_combine_final["Temp"]


# # For wind speed

# In[6]:


plt.plot(df_combine_final["Date(HKT)"], df_combine_final["Temp"], 'ko-', label="Obs")
plt.plot(df_combine_final["Date(HKT)"], df_combine_final["t2m[degC]"],'bo-', label="mpas")
plt.grid(zorder=0)
plt.xlabel("Date Time (HKT)")
plt.ylabel("T2m [deg C]")
plt.title(select_station_en)
plt.xticks(rotation=45)
plt.legend()
plt.show()
plt.close()


# In[7]:


#=========================================================

hko_wind = df_combine_final["obs-wspd[m/s]"]
hko_wdir = df_combine_final["obs-wdir[deg]"]

size = hko_wdir.shape[0]

wspd = hko_wind*1.9438452    # m/s to kt
wdir = hko_wdir*0.0174532925 # deg to rad
U_obs = -wspd*np.sin(wdir)
V_obs = -wspd*np.cos(wdir)

df = pd.DataFrame(np.vstack([U_obs,V_obs]).T, columns=['U', 'V'])

#=========================================================
mpas_wind = df_combine_final["wspd[m/s]"]
mpas_wdir = df_combine_final["wdir[deg]"]

wspd = mpas_wind*1.9438452    # m/s to kt
wdir = mpas_wdir*0.0174532925 # deg to rad
U_mpas = -wspd*np.sin(wdir)
V_mpas = -wspd*np.cos(wdir)

df_mpas = pd.DataFrame(np.vstack([U_mpas,V_mpas]).T, columns=['U', 'V'])

#=========================================================

fig, ax = plt.subplots(1,1, figsize=(15,4))

# ax.plot(df.index.values.astype('d'), df.V * 0.1 + 4, color='k')
# ax.quiver(df.index.values.astype('d'), np.ones(size) * 2, df.U.values, df.V.values) #, pivot='mid')

plt.barbs(df_combine_final["Date(HKT)"], np.ones(size) * 4, df.U.values, df.V.values, length=8, barbcolor='k')

plt.barbs(df_combine_final["Date(HKT)"], np.ones(size) * 2, df_mpas.U.values, df_mpas.V.values, length=8, barbcolor='b')

ax.set_ylim([-1,6])
plt.xticks(rotation=45)
plt.xlabel("Date Time (HKT)")
plt.title(select_station_en)
plt.xticks(rotation=45)
# plt.legend()
plt.show()
plt.close()


# In[8]:


fig, ax = plt.subplots(1,1, figsize=(15,8))

plt.plot(df_combine_final["Date(HKT)"], df_combine_final["obs-wspd[m/s]"], 'ko-', label="Obs")
plt.plot(df_combine_final["Date(HKT)"], df_combine_final["wspd[m/s]"],'ro-', label="mpas")

plt.barbs(df_combine_final["Date(HKT)"], np.ones(size) * -2, df.U.values, df.V.values, length=8, barbcolor='k')

plt.barbs(df_combine_final["Date(HKT)"], np.ones(size) * -5, df_mpas.U.values, df_mpas.V.values, length=8, barbcolor='r')

plt.grid(zorder=0)
plt.xlabel("Date Time (HKT)")
plt.ylabel("Wind Speed [m/s]")
plt.title(select_station_en)
plt.xticks(rotation=45)
plt.ylim([-7,12])
plt.axhline(y=0.0, color='k', linestyle='-')
plt.legend()
plt.show()
plt.close() 


# ## Seperate u and v directions
# 
# U_obs = -wspd x np.sin(wdir)\
# V_obs = -wspd x np.cos(wdir)

# In[9]:


fig, ax = plt.subplots(1,1, figsize=(15,8))

kt2ms = 0.514444444

plt.plot(df_combine_final["Date(HKT)"], U_obs*kt2ms, 'ko-', label="Obs")
plt.plot(df_combine_final["Date(HKT)"], U_mpas*kt2ms,'ro-', label="mpas")

plt.grid(zorder=0)
plt.xlabel("Date Time (HKT)")
plt.ylabel("U Wind Speed [m/s]")
plt.title(select_station_en)
plt.xticks(rotation=45)
# plt.ylim([-7,10])
plt.axhline(y=0.0, color='k', linestyle='-')
plt.legend()
plt.show()
plt.close() 

fig, ax = plt.subplots(1,1, figsize=(15,8))

plt.plot(df_combine_final["Date(HKT)"], V_obs*kt2ms, 'ko-', label="Obs")
plt.plot(df_combine_final["Date(HKT)"], V_mpas*kt2ms,'ro-', label="mpas")

plt.grid(zorder=0)
plt.xlabel("Date Time (HKT)")
plt.ylabel("V Wind Speed [m/s]")
plt.title(select_station_en)
plt.xticks(rotation=45)
# plt.ylim([-7,10])
plt.axhline(y=0.0, color='k', linestyle='-')
plt.legend()
plt.show()
plt.close() 


# # Final Plot

# In[26]:


fig = plt.subplots(2, 2, figsize=(20,12))

ax = plt.subplot(221)
plt.plot(df_combine_final["Date(HKT)"], df_combine_final["Temp"], 'ko-', label="Obs")
plt.plot(df_combine_final["Date(HKT)"], df_combine_final["t2m[degC]"],'ro-', label="mpas")
plt.grid(zorder=0)
plt.xlabel("Date Time (HKT)")
plt.ylabel("T2m [deg C]")
plt.title(select_station_en)
plt.xticks(rotation=45)
plt.legend()
plt.tight_layout()

ax = plt.subplot(222)
plt.plot(df_combine_final["Date(HKT)"], df_combine_final["obs-wspd[m/s]"], 'ko-', label="Obs")
plt.plot(df_combine_final["Date(HKT)"], df_combine_final["wspd[m/s]"],'ro-', label="mpas")
plt.barbs(df_combine_final["Date(HKT)"], np.ones(size) * -2, df.U.values, df.V.values, length=8, barbcolor='k')
plt.barbs(df_combine_final["Date(HKT)"], np.ones(size) * -5, df_mpas.U.values, df_mpas.V.values, length=8, barbcolor='r')
plt.grid(zorder=0)
plt.xlabel("Date Time (HKT)")
plt.ylabel("Wind Speed [m/s]")
plt.title(select_station_en)
plt.xticks(rotation=45)
plt.ylim([-7,12])
plt.axhline(y=0.0, color='k', linestyle='-')
plt.legend()


kt2ms = 0.514444444
ax = plt.subplot(223)
plt.plot(df_combine_final["Date(HKT)"], U_obs*kt2ms, 'ko-', label="Obs")
plt.plot(df_combine_final["Date(HKT)"], U_mpas*kt2ms,'ro-', label="mpas")
plt.grid(zorder=0)
plt.xlabel("Date Time (HKT)")
plt.ylabel("U Wind Speed [m/s]")
plt.title(select_station_en)
plt.xticks(rotation=45)
# plt.ylim([-7,10])
plt.axhline(y=0.0, color='k', linestyle='-')
plt.legend()


ax = plt.subplot(224)
plt.plot(df_combine_final["Date(HKT)"], V_obs*kt2ms, 'ko-', label="Obs")
plt.plot(df_combine_final["Date(HKT)"], V_mpas*kt2ms,'ro-', label="mpas")
plt.grid(zorder=0)
plt.xlabel("Date Time (HKT)")
plt.ylabel("V Wind Speed [m/s]")
plt.title(select_station_en)
plt.xticks(rotation=45)
# plt.ylim([-7,10])
plt.axhline(y=0.0, color='k', linestyle='-')
plt.legend()

plt.show()
plt.close() 


# In[ ]:
```
