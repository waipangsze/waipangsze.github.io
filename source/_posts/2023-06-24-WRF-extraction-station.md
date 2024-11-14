---
layout: post
title: WRF | WRF-extraction-station
categories: [WRF]
tags: [NWP, Jupyter]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/RwpxULg.png
banner_img: https://i.imgur.com/RwpxULg.png 
---

```python
import numpy as np
import xarray as xr
import pandas as pd
from netCDF4 import Dataset
import matplotlib.pyplot as plt
from matplotlib.cm import get_cmap

from wrf import (to_np, getvar, smooth2d, get_cartopy, cartopy_xlim,
                 cartopy_ylim, latlon_coords, extract_vars, xy_to_ll, ll_to_xy)

# How to display full output in Jupyter, not only last result?
from IPython.core.interactiveshell import InteractiveShell
InteractiveShell.ast_node_interactivity = "all"

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

rad2deg = 57.2957795

# # single xy to latlon
# lat_lon = xy_to_ll(ncfile_domain_1, 100, 100)
# print(lat_lon.values)

# # single latlon to xy
# x_y = ll_to_xy(ncfile_domain_1, 34.62, 105.46)
# print(x_y.values)

# # more points
# lat_lon = xy_to_ll(ncfile_domain_1, [100,105], [100,205])
# print(lat_lon.values)

# # more points
# x_y = ll_to_xy(ncfile_domain_1, [34.62, 35], [105.46, 100])
# print(x_y.values)

# get met stations
def get_station_info(statfile):
    stats = pd.read_csv(statfile, sep=',')
    stats = stats[['WMO_id','lat','lon']]
    stats = stats.dropna(axis=0)
    stats = stats.astype({'WMO_id':int})
    return (stats)

def read_model_data(domloc, wrfdir, dom, statf, sdate, edate, utc):
    import glob
    from netCDF4 import Dataset
    wrfoutfiles = sorted(glob.glob('{0}/wrfout_{1}_*'.format(wrfdir,dom)))
    wrflist = [ Dataset(f) for f in wrfoutfiles ]
    from wrf import getvar, ALL_TIMES, interplevel, extract_times, to_np, ll_to_xy, extract_times
    wrftimes = extract_times(wrflist, timeidx=ALL_TIMES, method='cat')

    # get start and end index
    times = [ str(s).split(':')[0] for s in wrftimes ]
    print(times)
    sdate = (datetime.strptime(sdate,'%Y-%m-%d %H:%M:%S')+timedelta(hours=-utc)).strftime('%Y-%m-%dT%H') 
    edate = (datetime.strptime(edate,'%Y-%m-%d %H:%M:%S')+timedelta(hours=-utc)).strftime('%Y-%m-%dT%H')
    st = times.index(sdate)
    et = times.index(edate) + 1
    print ('{0}: {1} - {2}'.format(domloc, st, et))

    stats = get_station_info(statf)
    stats['ij'] = stats.apply(lambda row: np.array(ll_to_xy(wrflist[0], row.lat, row.lon).values), axis=1)
    stats['i'] = stats.apply(lambda row: row.ij[0], axis=1)
    stats['j'] = stats.apply(lambda row: row.ij[1], axis=1)

    varlist = ["T2","Q2", "U10","V10"]
    df = pd.DataFrame()
    for varname in varlist:
        for tt in range(st,et):
            stats['varname'] = varname
            var = getvar(wrflist, varname, timeidx=tt, method="cat")
            if varname == 'T2':
                var = var - 273.15
            stats['datetime'] = var.coords['Time'].values
            stats['value'] = stats.apply(lambda row: to_np(var[row.i, row.j]), axis=1)
            df =df.append(stats[['datetime','WMO_id','varname','value']])
    df['datetime'] = df.apply(lambda row: (datetime.strptime(str(row.datetime),'%Y-%m-%d %H:%M:%S')+timedelta(hours=utc)).strftime("%Y-%m-%d %H:%M:%S"), axis=1)
    df['model'] = domloc
    #df['WSPD10'] = df.apply(lambda row: math.sqrt(row.U10 ** 2 + row.V10 ** 2))
    #df['WDIR10'] = df.apply(lambda row: 180+(360/(2/3.1415926)*math.atan2(row.U10,row.V10)))
    #df = df[]
    return (df)

station = get_station_info("./station.txt")
station

# WMO_id	lat	lon
# 0	1	22.312	114.173
# 1	2	33.000	105.000

domloc = "ucm"
wrfdir = "/xxx/wrf/"
dom = "d02"
statf = "./station.txt"
sdate = "2023-03-31 21:00:00"
edate = "2023-04-01 03:00:00"
utc = 0 # 8

df = read_model_data(domloc, wrfdir, dom, statf, sdate, edate, utc)
df

select_var = df[(df["varname"] == "T2") & (df["WMO_id"] == 1)]
select_var

plt.plot(pd.to_datetime(select_var.datetime), select_var.value, 'ro--')
plt.title("T2m")
plt.ylabel("T2m (deg C)")
plt.xlabel("Time (UTC)")
plt.legend()
# plt.axhline(y=0.0, color='k', linestyle='-')
plt.xticks(rotation=45)
plt.grid()
plt.show()
plt.close()
```
