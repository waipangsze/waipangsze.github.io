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
站名,英文站名,標籤,緯度_十進制,經度_十進制,海拔高度(米)
天文台,Hong Kong Observatory,hko,22.30194,114.17417,32
香港國際機場,Hong Kong International Airport,hka,22.30944,113.92194,6
上水雙魚河,Beas River,br1,22.49333,114.10500,11
黃麻角,Bluff Head,bhd,22.19750,114.21194,94
中環碼頭,Central Pier,cp1,22.28889,114.15583,19
長洲,Cheung Chau,cch,22.20111,114.02667,72
青衣(青柏樓),Ching Pak House (Tsing Yi),cph,22.34806,114.10917,122
清水灣,Clear Water Bay,cwb,22.26333,114.29972,66
青洲,Green Island,gi,22.28500,114.11278,88
跑馬地,Happy Valley,hpv,22.27056,114.18361,5
香港公園,Hong Kong Park,hkp,22.27833,114.16222,26
啟德,Kai Tak,se,22.30972,114.21333,3
啟德跑道公園,Kai Tak Runway Park,se1,22.30500,114.21694,4
嘉道理農場暨植物園,Kadoorie Farm and Botanic Garden,kfb,22.43278,114.12083,307
吉澳,Kat O,kat,22.53639,114.30194,10
滘西洲,Kau Sai Chau,ksc,22.37028,114.31250,39
京士柏,King's Park,kp,22.31194,114.17278,65
九龍城,Kowloon City,klt,22.33500,114.18472,92
觀塘,Kwun Tong,ktg,22.31861,114.22472,90
南丫島,Lamma Island,lam,22.22611,114.10861,7
流浮山,Lau Fau Shan,lfs,22.46889,113.98361,31
彌勒山,Nei Lak Shan,nls,22.26333,113.91111,747
新青衣站,New Tsing Yi Station,ty1,22.34417,114.11000,8
昂坪,Ngong Ping,ngp,22.25861,113.91278,593
北潭涌(鯽魚湖),Pak Tam Chung (Tsak Yue Wu),tyw,22.40278,114.32306,5
坪洲,Peng Chau,pen,22.29111,114.04333,34
平洲,Ping Chau,epc,22.54667,114.42833,29
西貢,Sai Kung,skg,22.37556,114.27444,4
沙螺灣,Sha Lo Wan,slw,22.29111,113.90694,61
沙田,Sha Tin,sha,22.40250,114.21000,6
深水埗,Sham Shui Po,ssp,22.33583,114.13694,11
筲箕灣,Shau Kei Wan,skw,22.28167,114.23611,53
石崗,Shek Kong,sek,22.43611,114.08472,16
上水,Sheung Shui,ssh,22.50194,114.11111,10
赤柱,Stanley,sty,22.21417,114.21861,31
打鼓嶺,Ta Kwu Ling,tkl,22.52861,114.15667,15
大隴,Tai Lung,tls,22.48472,114.11750,21
大美督,Tai Mei Tuk,plc,22.47528,114.23750,51
大帽山,Tai Mo Shan,tms,22.41056,114.12444,955
大埔(元洲仔公園),Tai Po (Yuen Chau Tsai Park),yct,22.44833,114.17722,6
塔門,Tap Mun,tap,22.47139,114.36056,15
大老山,Tate's Cairn,tc,22.35778,114.21778,572
山頂,The Peak,vp1,22.26417,114.15500,406
將軍澳,Tseung Kwan O,jkb,22.31583,114.25556,38
荃灣,Tsuen Wan,twn,22.38361,114.10778,142
荃灣城門谷,Tsuen Wan Shing Mun Valley,tw,22.37556,114.12667,35
屯門兒童及青少年院,Tuen Mun Children and Juvenile Home,tu1,22.38583,113.96417,28
橫瀾島,Waglan Island,wgl,22.18222,114.30333,56
濕地公園,Wetland Park,wlp,22.46667,114.00889,4
黃竹坑,Wong Chuk Hang,hks,22.24778,114.17361,5
黃大仙,Wong Tai Sin,wts,22.33944,114.20528,21
元朗公園,Yuen Long Park,ylp,22.44083,114.01833,8
自動氣象浮標2號,Automatic Weather Buoy No.2 (Hong Kong International Airport, West),wb2,22.29111,113.88222,9
自動氣象浮標8號,Automatic Weather Buoy No.8 (Hong Kong International Airport, East),wb8,22.30583,113.95389,9
長洲泳灘,Cheung Chau Beach,ccb,22.21083,114.02917,27
北角,North Point,np,22.29444,114.19972,26
沙洲,Sha Chau,sc,22.34583,113.89111,31
青衣蜆殼油庫,Shell Oil Depot,shl,22.34667,114.08639,43
九龍天星碼頭,Star Ferry (Kowloon),sf,22.29306,114.16861,18
大磨刀,Tai Mo To,tmt,22.32972,113.96667,15
大澳,Tai O,to,22.25611,113.85472,105
大埔滘,Tai Po Kau,tpk,22.44250,114.18389,12
屯門政府合署,Tuen Mun Government Offices,tun,22.39056,113.97667,69
二東山,Yi Tung Shan,yts,22.25917,113.96417,752
塔門東,Tap Mun East,tme,22.46833,114.36306,48
凹頭,Au Tau,r28,22.45000,114.05306,3
鶴咀,Cape D'Aguilar,cda,22.20944,114.25472,45
愉景灣,Discovery Bay,r12,22.29139,114.00917,106
破邊洲,Po Pin Chau,ppc,22.36167,114.37139,68
落馬洲,Lok Ma Chau,r29,22.51167,114.08028,67
昂坪食水配水庫,Ngong Ping Fresh Water Reservoir,r11,22.25556,113.91139,479
鰂魚涌,Quarry Bay,qu1,22.29111,114.21333,7
西貢(香港三育書院),Hong Kong Adventist College (Sai Kung),r18,22.30750,114.28694,122
沙頭角,Sha Tau Kok,r24,22.53750,114.21111,39
大美督抽水站,Tai Mei Tuk Pumping Station,r31,22.47833,114.23889,24
大埔王肇枝中學,Tai Po Wong Shiu Chi Secondary School,r23,22.44556,114.17167,23
踏石角,Tap Shek Kok,r21,22.37917,113.92000,28
尖鼻咀,Tsim Bei Tsui,r22,22.48639,114.01167,8
屯門水庫,Tuen Mun Reservior,tmr,22.40750,113.98722,98
大灘訓練營,Tai Tan Camp,ttc,22.43528,114.33417,19
尖鼻咀潮汐站,Tsim Bei Tsui,tbt,22.48722,114.01417,
鰂魚涌潮汐站,Quarry Bay,qub,22.29111,114.21333,
石壁潮汐站,Shek Pik,spw,22.22028,113.89444,
大廟灣潮汐站,Tai Miu Wan,tmw,22.26972,114.28833,
大埔滘潮汐站,Tai Po Kau,tpk,22.44250,114.18389,
橫瀾島潮汐站,Waglan Island,wag,22.18306,114.30278,
中環廣場,Central Plaza,wcn,22.28139,114.17111,378
長沙灣,Cheung Sha Wan,csw,22.33278,114.15389,30
吉澳漁業研究分站,Kat O Fisheries Research Sub-Station,r30,22.53611,114.13528,10
又一村,Yau Yat Chuen,yyc,22.33250,114.17250,64
糧船灣,Leung Shuen Wan,r32,22.35194,114.35306,23
自動氣象浮標3號,Automatic Weather Buoy No.3 (Hong Kong International Airport, East),wb3,22.31972,113.96139,9
北角潮汐站,North Point (Tide Station),npt,22.30000,114.20000,
深屈,Sham Wat,sw,22.26861,113.88694,13
元朗,Yuen Long,r27,22.41889,113.99611,102
北潭凹,Pak Tam Au,r25,22.41306,114.32972,106
鰂魚涌(舊),Quarry Bay (old),r19,22.29111,114.21333,7
大埔(自然環境保護研究中心),Tai Po (Conservation Studies Centre),tpo,22.44611,114.17889,15
鶴咀(舊),Cape D'Aguilar (old),r14,22.20944,114.25500,45
小蠔灣,Siu Ho Wan,shw,22.30583,113.97917,15
```

```sh
站名,英文站名,標籤,緯度_十進制,經度_十進制,海拔高度(米)
天文台,Hong Kong Observatory,hko,22.30194,114.17417,32
香港國際機場,Hong Kong International Airport,hka,22.30944,113.92194,6
上水雙魚河,Beas River,br1,22.49333,114.10500,11
黃麻角,Bluff Head,bhd,22.19750,114.21194,94
中環碼頭,Central Pier,cp1,22.28889,114.15583,19
長洲,Cheung Chau,cch,22.20111,114.02667,72
青衣(青柏樓),Ching Pak House (Tsing Yi),cph,22.34806,114.10917,122
清水灣,Clear Water Bay,cwb,22.26333,114.29972,66
青洲,Green Island,gi,22.28500,114.11278,88
跑馬地,Happy Valley,hpv,22.27056,114.18361,5
香港公園,Hong Kong Park,hkp,22.27833,114.16222,26
啟德,Kai Tak,se,22.30972,114.21333,3
啟德跑道公園,Kai Tak Runway Park,se1,22.30500,114.21694,4
嘉道理農場暨植物園,Kadoorie Farm and Botanic Garden,kfb,22.43278,114.12083,307
吉澳,Kat O,kat,22.53639,114.30194,10
滘西洲,Kau Sai Chau,ksc,22.37028,114.31250,39
京士柏,King's Park,kp,22.31194,114.17278,65
九龍城,Kowloon City,klt,22.33500,114.18472,92
觀塘,Kwun Tong,ktg,22.31861,114.22472,90
南丫島,Lamma Island,lam,22.22611,114.10861,7
流浮山,Lau Fau Shan,lfs,22.46889,113.98361,31
彌勒山,Nei Lak Shan,nls,22.26333,113.91111,747
新青衣站,New Tsing Yi Station,ty1,22.34417,114.11000,8
昂坪,Ngong Ping,ngp,22.25861,113.91278,593
北潭涌(鯽魚湖),Pak Tam Chung (Tsak Yue Wu),tyw,22.40278,114.32306,5
坪洲,Peng Chau,pen,22.29111,114.04333,34
平洲,Ping Chau,epc,22.54667,114.42833,29
西貢,Sai Kung,skg,22.37556,114.27444,4
沙螺灣,Sha Lo Wan,slw,22.29111,113.90694,61
沙田,Sha Tin,sha,22.40250,114.21000,6
深水埗,Sham Shui Po,ssp,22.33583,114.13694,11
筲箕灣,Shau Kei Wan,skw,22.28167,114.23611,53
石崗,Shek Kong,sek,22.43611,114.08472,16
上水,Sheung Shui,ssh,22.50194,114.11111,10
赤柱,Stanley,sty,22.21417,114.21861,31
打鼓嶺,Ta Kwu Ling,tkl,22.52861,114.15667,15
大隴,Tai Lung,tls,22.48472,114.11750,21
大美督,Tai Mei Tuk,plc,22.47528,114.23750,51
大帽山,Tai Mo Shan,tms,22.41056,114.12444,955
大埔(元洲仔公園),Tai Po (Yuen Chau Tsai Park),yct,22.44833,114.17722,6
塔門,Tap Mun,tap,22.47139,114.36056,15
大老山,Tate's Cairn,tc,22.35778,114.21778,572
山頂,The Peak,vp1,22.26417,114.15500,406
將軍澳,Tseung Kwan O,jkb,22.31583,114.25556,38
荃灣,Tsuen Wan,twn,22.38361,114.10778,142
荃灣城門谷,Tsuen Wan Shing Mun Valley,tw,22.37556,114.12667,35
屯門兒童及青少年院,Tuen Mun Children and Juvenile Home,tu1,22.38583,113.96417,28
橫瀾島,Waglan Island,wgl,22.18222,114.30333,56
濕地公園,Wetland Park,wlp,22.46667,114.00889,4
黃竹坑,Wong Chuk Hang,hks,22.24778,114.17361,5
黃大仙,Wong Tai Sin,wts,22.33944,114.20528,21
元朗公園,Yuen Long Park,ylp,22.44083,114.01833,8
長洲泳灘,Cheung Chau Beach,ccb,22.21083,114.02917,27
北角,North Point,np,22.29444,114.19972,26
沙洲,Sha Chau,sc,22.34583,113.89111,31
青衣蜆殼油庫,Shell Oil Depot,shl,22.34667,114.08639,43
九龍天星碼頭,Star Ferry (Kowloon),sf,22.29306,114.16861,18
大磨刀,Tai Mo To,tmt,22.32972,113.96667,15
大澳,Tai O,to,22.25611,113.85472,105
大埔滘,Tai Po Kau,tpk,22.44250,114.18389,12
屯門政府合署,Tuen Mun Government Offices,tun,22.39056,113.97667,69
二東山,Yi Tung Shan,yts,22.25917,113.96417,752
塔門東,Tap Mun East,tme,22.46833,114.36306,48
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

# 没有SimHei 字体

1. 下载字体
   1. <https://link.juejin.cn/?target=https%3A%2F%2Flink.zhihu.com%2F%3Ftarget%3Dhttps%253A%2F%2Fgithub.com%2Fyuehuhu%2Fsome-useful%2Fraw%2Fmaster%2Fttf%2FSimHei.ttf>
2. 将下载的字体放到
   1.  `home/wpsze/micromamba/envs/<envs>/lib/python3.10/site-packages/matplotlib/mpl-data/fonts/ttf`
3. 修改 `matplotlibrc` 文件
   1. `lib/python3.10/site-packages/matplotlib/mpl-data/matplotlibrc`
      1. ```text
        font.family:  sans-serif
        # 去掉前面的# 
        font.sans-serif: SimHei, DejaVu Sans, Bitstream Vera Sans,
        # 去掉前面的#，并在冒号后面添加SimHei
        axes.unicode_minus: False  # use Unicode for the minus symbol rather than hy    phen.  See
        # 去掉前面的#，并将True改为False
        ```
4. 删除 `matplotlib` 的系统缓存
   1. `rm -rf /root/.cache/matplotlib/*`
   2. 等会运行python缓存文件就会自动生成
