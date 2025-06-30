---
layout: post
title: PALM | Topography and Flow_around_buildings
categories: [PALM]
tags: [NWP, MPAS, WRF, PALM, LES, Urban, PALM, Topography]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
date: 2025-06-27 11:00:00
index_img: https://i.imgur.com/Unyeq0D.png
banner_img: https://i.imgur.com/Unyeq0D.png
---

# Exercise presentations

- <https://palm.muk.uni-hannover.de/trac/wiki/doc/tut/palm#Exercisepresentations>

![](https://i.imgur.com/vli06Jo.png)

## E3: Flow around a cubical building

### Required lecture

Required lectures:

- Topography
- Non-cyclic boundary conditions
- Restart runs

Required files:

- Parameter file (`*_p3d`, `*_pcr`)
  - Input files can be downloaded from the supplementary material. Within the parameter files, some options are left blank (marked as “???”) and need to be filled in before starting the simulation.
- Topography file (`*_static`)

#### flow_around_cube_cyclic

- namelist file: `flow_around_cube_noncyclic_p3d`
  - set `topography` = `read_from_file`
- topography file: `flow_around_cube_noncyclic_topo`
  - alternatively use provided python script to create **static input file** `flow_around_cube_noncyclic_static`
- set `topography`
  - ![](https://i.imgur.com/ZgeuKkS.png)

{% gi 4 2-2 %}
![](https://i.imgur.com/cVrBqQ5.png)
![](https://i.imgur.com/71HGboz.png)
![](https://i.imgur.com/sEDjfww.png)
![](https://i.imgur.com/yCikg50.png)
{% endgi %}

##### Prapare `*_static`

![](https://i.imgur.com/JFTbGlz.png)

- <https://palm.muk.uni-hannover.de/trac/wiki/doc/app/iofiles/pids/static#topo>
- <https://palm.muk.uni-hannover.de/trac/wiki/doc/app/initialization_parameters#topography>
- <https://palm.muk.uni-hannover.de/trac/wiki/doc/app/iofiles#TOPOGRAPHY_DATA>

##### my toy case

```python
......
# specify general grid parameters
# these values must equal those set in the initialization_parameters
self.nx = 245
self.ny = 83
self.nz = 80
self.dx = 2.0
self.dy = 2.0
self.dz = 2.0
......
# Set index ranges for buildings
build_inds_start_x = [ 80, 120, 160 ]
build_inds_end_x   = [ 99, 139, 179 ]

build_inds_start_y = [20, 50] #build_inds_start_x
build_inds_end_y   = [39, 69] #build_inds_end_x
......
```

![](https://i.imgur.com/HJTScbq.png){width=400}

#### flow_around_cube_noncyclic

##### Modify `*_p3d`, `*_pcr`

- namelist file: `flow_around_cube_noncyclic_p3d`
- topography file: `flow_around_cube_noncyclic_topo`
  - alternatively use provided python script to create **static input file** `flow_around_cube_noncyclic_static`

1. psolver [link](https://palm.muk.uni-hannover.de/trac/wiki/doc/app/initialization_parameters#psolver)
   1. 'poisfft'
2. end_time
   1. 9000.0
3. Message: psolver = "poisfft" requires that boundary conditions along x and y are both either cyclic or non-cyclic
   1. 'https://docs.palm-model.com/25.04/Reference/LES_Model/Logging/#PAC0077'
   2. Description: Switch to `psolver = 'multigrid'` or set `bc_lr = bc_ns`.

# Topography dataset

## Shuttle Radar Topography Mission (SRTM GL3) Global 90m

- <https://portal.opentopography.org/raster?opentopoID=OTSRTM.042013.4326.1>

## generate `*.nc` from `*.tif`

```sh
#!/bin/bash
source /home/wpsze/micromamba/etc/profile.d/micromamba.sh
micromamba activate gdal
export SCRIPT_DIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" >/dev/null 2>&1 && pwd )"

gdal_translate -of NetCDF ${1} ${1}.nc
```

![](https://i.imgur.com/NHqvVt4.png)

### Focus on HKIA

{% fold info @HKIA log %}
```log
1. Coordinates
Horizontal Coordinates: WGS84 [EPSG: 4326]
Vertical Coordinates: WGS84 Ellipsoid/EGM1996 Geoid [EPSG: 5773]
Units: degree
Data Selection Coordinates (Select an area on map)
  Xmin = 113.78842160152271	  Ymin = 22.145626613388345	  Xmax = 114.08065799670295	  Ymax = 22.355092712639248
   Manually enter selection coordinates (in the horizontal coordinate system listed above)  

- Band1's min and max = -49.0 888.0

Current selection area is approximately 701 km2.


	lon = 351 ;
	lat = 251 ;

crs:GeoTransform = "113.7879166665999 0.000833333333333144 0 22.35541666667522 0 -0.000833333333333144 " ;

lat diff = 22.355092712639248-22.145626613388345 = 0.20946609925*111km = 23.2507370168km
lon diff = 114.08065799670295-113.78842160152271 = 0.29223639518*111km = 32.438239865km

lat = 251*0.000833333333333144 = 0.20916666666*111km = 23.2174999993km
lon = 351*0.000833333333333144 = 0.2925*111km        = 32.4675 km
```
{% endfold %}

![](https://i.imgur.com/yjCuvm0.png)


## generate `.acs` from `.tif` by rasterio

- `rasterio=1.3.8`

```python
import numpy as np
import rasterio

tif_raster = rasterio.open("output_SRTMGL3.tif")
new_array = tif_raster.read(1)

# Modify array
#value = 50
#new_array  = np.where( (raw_array != 0) & (raw_array <= value) , 1, 0)

with rasterio.open(
    'new_raster.asc',
    'w',
    driver='AAIGrid',
    height=new_array.shape[0],
    width=new_array.shape[1],
    count=1,
    dtype=new_array.dtype,
    crs=tif_raster.crs,
    transform=tif_raster.transform,
    force_cellsize=True
) as dst:
    dst.write(new_array, 1)
```

## create `static file` from `*.nc file`

- above output nc file 

{% fold info @create_static_file.py %}
```python
#------------------------------------------------------------------------------#
# This file is part of the PALM model system.
#
# PALM is free software: you can redistribute it and/or modify it under the
# terms of the GNU General Public License as published by the Free Software
# Foundation, either version 3 of the License, or (at your option) any later
# version.
#
# PALM is distributed in the hope that it will be useful, but WITHOUT ANY
# WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR
# A PARTICULAR PURPOSE.  See the GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License along with
# PALM. If not, see <http://www.gnu.org/licenses/>.
#
# Copyright 1997-2020 Leibniz Universitaet Hannover
#------------------------------------------------------------------------------#

import math
import netCDF4
from   netCDF4 import Dataset
import numpy as np
import datetime


class StaticDriver:
    """ This is an example script to generate static drivers for PALM.

    You can use it as a starting point for creating your setup specific
    driver.
    """


    def __init__(self):
        """ Open the static driver as NetCDF4 file. Here, you have to give the
        full path to the static driver that shall be created. Existing file
        with same name is deleted.
        """
        print('Opening file...')
        self.nc_file = Dataset("new_raster.nc", 'w', format='NETCDF4')
        print('Opening target file ...')
        self.nc_target_file = read_acs()

    def write_global_attributes(self):
        """ Write global attributes to static driver. """
        print("Writing global attributes...")

        # mandatory global attributes
        self.nc_file.origin_lon = 55.0 # used to initialize coriolis parameter
        self.nc_file.origin_lat = 0.0 # (overwrite initialization_parameters)
        self.nc_file.origin_time = '2022-09-14 00:00:00 +00'
        self.nc_file.origin_x = 308124
        self.nc_file.origin_y = 6098908
        self.nc_file.origin_z = 0.0
        self.nc_file.rotation_angle = 0.0

        # optional global attributes
        self.nc_file.author = '...'
        self.nc_file.comment = 'Miscellaneous information about the data ' \
                               'or methods to produce it.'
        self.nc_file.creation_date = str(datetime.datetime.now())
        self.nc_file.institution = 'LUH'
        self.nc_file.history = ''
        self.nc_file.palm_revision = ''
        self.nc_file.title = 'Static driver for E3'


    def define_dimensions(self):
        """ Set dimensions on which variables are defined. """
        print("Writing dimensions...")

        # specify general grid parameters
        # these values must equal those set in the initialization_parameters
        self.nx = self.nc_target_file.shape[1] # lon
        self.ny = self.nc_target_file.shape[0] # lat
        self.nz = 250
        self.dx = 90.0
        self.dy = 90.0
        self.dz = 4.0

        # mandatory dimensions
        self.nc_file.createDimension('x' ,self.nx+1)
        self.x = self.nc_file.createVariable('x', 'f8', ('x',))
        self.x.units = 'm'
        self.x.standard_name = 'x coordinate of cell centers'
        self.x[:] = np.arange(0,(self.nx+1)*self.dx,self.dx)

        self.nc_file.createDimension('xu', self.nx)
        self.xu = self.nc_file.createVariable('xu', 'f8', ('xu',))
        self.xu.units = 'm'
        self.xu.standard_name = 'x coordinate of cell edges'
        self.xu[:] = np.arange(self.dx/2.,self.nx*self.dx,self.dx)

        self.nc_file.createDimension('y', self.ny+1)
        self.y = self.nc_file.createVariable('y', 'f8', ('y',))
        self.y.units = 'm'
        self.y.standard_name = 'y coordinate of cell centers'
        self.y[:] = np.arange(0,(self.ny+1)*self.dy,self.dy)

        self.nc_file.createDimension('yv', self.ny)
        self.yv = self.nc_file.createVariable('yv', 'f8', ('yv',))
        self.yv.units = 'm'
        self.yv.standard_name = 'y coordinate of cell edges'
        self.yv[:] = np.arange(self.dy/2.,self.ny*self.dy,self.dy)

        # if your simulation uses a stretched vertical grid, you need to
        # modify the z and zw coordinates,
        # e.g. z_array = (...) and zw_array = (...)
        z_array = np.append(0, np.arange(self.dz/2,(self.nz+1)*self.dz,self.dz))
        self.nc_file.createDimension('z',self.nz+2)
        self.z = self.nc_file.createVariable('z', 'f8', ('z',))
        self.z.units = 'm'
        self.z.standard_name = 'z coordinate of cell centers'
        self.z[:] = z_array

        zw_array = np.arange(0,(self.nz+2)*self.dz,self.dz)
        self.nc_file.createDimension('zw', self.nz+2)
        self.zw = self.nc_file.createVariable('zw', 'f8', ('zw',))
        self.zw.units = 'm'
        self.zw.standard_name = 'z coordinate of cell edges'
        self.zw[:] = zw_array

        # optional dimensions, uncomment if needed
        #self.nc_file.createDimension('zsoil', len(zsoil_array))
        #...

        #self.nc_file.createDimension('zlad', len(zlad_array))
        #...

        #self.nc_file.createDimension('nsurface_fraction', 3)
        #...


    def add_variables(self):

        print("Writing variables...")
        fill_val_real = -9999.9
        fill_val_int8 = -127

        # topography variables
        nc_buildings_2d = self.nc_file.createVariable(
             'buildings_2d', 'f4', ('y','x'), fill_value=fill_val_real)
        nc_buildings_2d.lod = 1
        nc_buildings_2d[:,:] = fill_val_real

        nc_building_id = self.nc_file.createVariable(
            'building_id', 'i1', ('y','x'), fill_value=fill_val_int8)
        nc_building_id[:,:] = fill_val_int8

        nc_building_type = self.nc_file.createVariable(
            'building_type', 'i1', ('y','x'), fill_value=fill_val_int8)
        nc_building_type[:,:] = fill_val_int8



        ## terrain height
        nc_zt = self.nc_file.createVariable(    \
            'zt', 'f4', ('y','x'), fill_value=fill_val_real)
        nc_zt.long_name = 'orography'
        nc_zt.units = 'm'

        nc_zt[:,:] = 0.0

        nc_buildings_2d[1:,1:] = self.nc_target_file[:,:]
        nc_building_type[:,:] = 2

        # Set index ranges for buildings
        #build_inds_start_x = [ 80, 120, 160 ]
        #build_inds_end_x   = [ 99, 139, 179 ]

        #build_inds_start_y = [20, 50] #build_inds_start_x
        #build_inds_end_y   = [39, 69] #build_inds_end_x

        #for i in range(0,self.nx+1):
        #    for j in range(0,self.ny+1):
        #        # Set building height
        #        for ii in range(0,len(build_inds_start_x)):
        #            for jj in range(0,len(build_inds_start_y)):
        #
        #                if ( i >= build_inds_start_x[ii] and i <= build_inds_end_x[ii] ) and \
        #                    ( j >= build_inds_start_y[jj] and j <= build_inds_end_y[jj] ):
        #                    
        #                    nc_buildings_2d[j,i] = 40.0
        #                    nc_building_type[j,i] = 2
        #
        #                   # For each (ii,jj)-pair create an unique ID
        #                    nc_building_id[j,i] = ii + len(build_inds_start_x) * jj

    def finalize(self):
        """ Close file """
        print("Closing file...")

        self.nc_file.close()


def read_acs(input_file="new_raster.asc"):
    # Assuming a header of 6 lines, adjust 'skiprows' as needed
    data_array = np.loadtxt(input_file, skiprows=5) 
    data_array[data_array < 0] = 0
    data_array = np.flipud(data_array)

    print(data_array.shape)
    # Find the maximum value
    max_value = max(max(row) for row in data_array)
    # Find the minimum value
    min_value = min(min(row) for row in data_array)
    print(f"Maximum value: {max_value}")
    print(f"Minimum value: {min_value}")
    print(data_array)
    return data_array

if __name__ == '__main__':
    driver = StaticDriver()
    driver.write_global_attributes()
    driver.define_dimensions()
    driver.add_variables()
    driver.finalize()
```
{% endfold %}

