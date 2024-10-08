---
layout: post
title: WRF | WPS geo data and binary tile files
categories: [WRF]
tags: [tile files, WPS, WRF, MPAS]
author: wpsze
index_img: 
banner_img: 
---

# Binary files in the geog folder

The file naming convention is described on slides 14 and 15 of the [Advanced Usage of the WPS](https://www2.mmm.ucar.edu/wrf/users/tutorial/presentation_pdfs/202101/duda_wps_advanced.pdf) tutorial presentation. If you have any additional questions about the file names, please don't hesitate to follow up in this forum and I can offer further details.

# Reading tile fines and ploting

```python
import numpy as np
import matplotlib as mpl
import matplotlib.pyplot as plt
import glob
import re
import math
from matplotlib.colors import ListedColormap

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

# Reading binary tile files
# file_dir = "/dir/*"
root_dir = "/dir/geog/"
dataset_dir = "modis_landuse_20class_30s"
#dataset_dir = "modis_landuse_20class_15s"
# dataset_dir = "greenfrac_fpar_modis"
# dataset_dir = "topo_gmted2010_2.5m"
# dataset_dir = "topo_gmted2010_30s"
# dataset_dir = "soiltype_top_30s"
# dataset_dir = "albedo_modis"
# dataset_dir = "albedo_ncep"
# dataset_dir = "greenfrac"

file_dir = root_dir + dataset_dir + "/*"
print(file_dir)
all_files = sorted(glob.glob(file_dir))

all_files

index_file = all_files[-1]
with open(index_file, "r") as f:
    index_info = f.read()
print(index_info)

### WITHOUT SPACE ###
tile_x = int(re.search('tile_x=(.+?)\ntile_y', index_info).group(1))
tile_y = int(re.search('tile_y=(.+?)\ntile_z', index_info).group(1))
tile_z = int(re.search('tile_z=(.+?)\nunits', index_info).group(1))  # for land use, soiltype
# tile_z = int(re.search('tile_z=(.+?)\nscale', index_info).group(1))  # for greenfrac

### WITH SPACE ###
# tile_x = int(re.search('tile_x = (.+?)\ntile_y', index_info).group(1))
# tile_y = int(re.search('tile_y = (.+?)\ntile_z', index_info).group(1))
# tile_z = int(re.search('tile_z = (.+?)\ntile_bdr', index_info).group(1))  # for topo_gmted2010

tile_bdr = 0  # default = 0?
# tile_bdr = int(re.search('tile_bdr=(.+?)\nunits', index_info).group(1))  # for topo_gmted2010

# (tile_z, tile_y + 2 * tile_bdr and tile_x + 2 * tile_bdr, tile_y + 2 * tile_bdr and tile_x + 2 * tile_bdr)
dim1 = tile_z
dim2 = tile_y + 2 * tile_bdr
dim3 = tile_x + 2 * tile_bdr

# degrees per pixel
### WITHOUT SPACE ###
dx = float(re.search('dx=(.+?)\ndy', index_info).group(1))  # for land use, greenfrac, soiltype
### WITH SPACE ###
# dx = float(re.search('dx = (.+?)\ndy', index_info).group(1))  # for topo_gmted2010

print(dim1, dim2, dim3)
print(tile_x, tile_y, tile_z, dx)

def get_tile_name(pixels_per_tile, dx, target_lat, lat_hemi, target_lon, lon_hemi, pad=5):
    
    # lat starts from south pole
    if lat_hemi == 'S':
        lat = 90 - target_lat
    elif lat_hemi == 'N':
        lat = 90 + target_lat
    else:
        raise ValueError('Invalid lat_hemi')
        
    # lon starts from international date line
    if lon_hemi == 'W':
        lon = 180 - target_lon
    elif lon_hemi == 'E':
        lon = 180 + target_lon
    else:
        raise ValueError('Invalid lon_hemi')
    
    print('lat', lat, 'lon', lon)
    
    # 10 deg per tile for 15s/30s
    degs_per_tile = round(tile_x * dx)
    print(degs_per_tile)
    
#     tile_left = int(math.floor(lon / 10) * 10)
#     tile_right = int(math.ceil(lon / 10) * 10)
#     tile_bot = int(math.floor(lat / 10) * 10)
#     tile_top = int(math.ceil(lat / 10) * 10)
    
    tile_left = int(math.floor(lon / degs_per_tile) * degs_per_tile)
    tile_right = int(math.ceil(lon / degs_per_tile) * degs_per_tile)
    tile_bot = int(math.floor(lat / degs_per_tile) * degs_per_tile)
    tile_top = int(math.ceil(lat / degs_per_tile) * degs_per_tile)  

    print('tile dims', tile_left, tile_right, tile_bot, tile_top)
    
#     tile_left_str = str(int(math.floor(lon / 10) * pixels_per_tile + 1))
#     tile_right_str = str(int(math.ceil(lon / 10) * pixels_per_tile))
#     tile_bot_str = str(int(math.floor(lat / 10) * pixels_per_tile + 1))
#     tile_top_str = str(int(math.ceil(lat / 10) * pixels_per_tile))

    tile_left_str = str(int(math.floor(lon / degs_per_tile) * pixels_per_tile + 1)).zfill(pad)
    tile_right_str = str(int(math.ceil(lon / degs_per_tile) * pixels_per_tile)).zfill(pad)
    tile_bot_str = str(int(math.floor(lat / degs_per_tile) * pixels_per_tile + 1)).zfill(pad)
    tile_top_str = str(int(math.ceil(lat / degs_per_tile) * pixels_per_tile)).zfill(pad)

    target_file = tile_left_str + '-' + tile_right_str + '.' + tile_bot_str + '-' + tile_top_str
    
    return target_file

# Set lat/lon
### WITHOUT SPACE ###
pixels_per_tile = int(re.search('tile_x=(.+?)\ntile_y', index_info).group(1))
### WITH SPACE ###
# pixels_per_tile = int(re.search('tile_x = (.+?)\ntile_y', index_info).group(1))

search_lat = 22.3
search_lon = 114.2
print(pixels_per_tile)

# west region: 27.8 N, 92.0 E

search_tile = get_tile_name(pixels_per_tile, dx, search_lat, 'N', search_lon, 'E')
search_file = root_dir + dataset_dir + '/' + search_tile
print(search_file)

# Plot
tiley = pixels_per_tile
tilex = pixels_per_tile

arr = np.fromfile(search_file, dtype='i1')
tile_output = np.reshape(arr, (tiley, tilex))

num_color = 17

# For landuse
lst_color =     [
"darkgreen",
"lime",
"limegreen",
"lightgreen",
"darkseagreen",
"mediumpurple",
"linen",
"tan",
"wheat",
"darkorange",
"blue",
"yellow",
"red",
"olive",
"white",
"lightsteelblue",
"cyan",
"whitesmoke",
"grey",
"dimgrey"
    ]

custom_cmap = ListedColormap(lst_color[:num_color])

print("Min, Max = ", np.min(tile_output), np.max(tile_output))

plt.imshow(tile_output, cmap=custom_cmap, interpolation='nearest', origin='lower')
plt.colorbar()
plt.title( dataset_dir )
plt.show()
plt.close()

# Plot multiple tile fies
search_file

# From small y to large y ==> + 
# From large x to small x ==> -

def tile_combine(header, xs, xe, ys, ye, nxy, pixels_per_tile):
    #
    # Make first one
    #
    px = 0
    print("For x, ", px, pixels_per_tile, pixels_per_tile*px)
    tmp = header+str(xs-pixels_per_tile*px)+'-'+str(xe-pixels_per_tile*px)+'.'+str(ys)+'-'+str(ye)
    print(tmp)
    arr_tmp = np.fromfile(tmp, dtype='i1')
    combine = np.reshape(arr_tmp, (tiley, tilex))

    for py in range(1, nxy):
        print("   --- For y, ", py, pixels_per_tile, pixels_per_tile*py)
        tmp = header+str(xs)+'-'+str(xe)+'.'+str(ys+pixels_per_tile*py)+'-'+str(ye+pixels_per_tile*py)
        print(tmp)
        arr_tmp = np.fromfile(tmp, dtype='i1')
        arr_tmp = np.reshape(arr_tmp, (tiley, tilex))
        combine = np.concatenate((combine, arr_tmp))   
    
    #
    # Build the rest
    #
    for px in range(1, nxy):
        
        print("For x, ", px, pixels_per_tile, pixels_per_tile*px)
        tmp = header+str(xs-pixels_per_tile*px)+'-'+ \
                    str(xe-pixels_per_tile*px)+'.'+ \
                    str(ys)+'-'+ \
                    str(ye)
        print(tmp)
        arr_tmp = np.fromfile(tmp, dtype='i1')
        tmp_combine = np.reshape(arr_tmp, (tiley, tilex))

        for py in range(1, nxy):
            print("   ---For y, ", py, pixels_per_tile, pixels_per_tile*py)
            tmp = header+str(xs-pixels_per_tile*px)+'-'+ \
                        str(xe-pixels_per_tile*px)+'.'+ \
                        str(ys+pixels_per_tile*py)+'-'+ \
                        str(ye+pixels_per_tile*py)
            print(tmp)
            arr_tmp = np.fromfile(tmp, dtype='i1')
            arr_tmp = np.reshape(arr_tmp, (tiley, tilex))
            tmp_combine = np.concatenate((tmp_combine, arr_tmp))
        
        #
        combine = np.concatenate((tmp_combine, combine), axis=1)
    
    
    return combine

con = tile_combine("/dir/data/geog/modis_landuse_20class_30s/", 352801, 354000, 133201, 134400, 8, pixels_per_tile)

plt.imshow(con, cmap=custom_cmap, interpolation='nearest', origin='lower')
plt.colorbar()
plt.title( dataset_dir )
plt.show()
plt.close()

```


