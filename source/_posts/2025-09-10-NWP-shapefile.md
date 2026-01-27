---
layout: post
title: NWP | shapefile
categories: [NWP]
tags: [MPAS, WRF, NWP, GFS, FNL, IFS, ERA5, CMA]
author: wpsze
date: 2025-09-10 06:00:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/htVo0nI.png
banner_img: https://i.imgur.com/htVo0nI.png
---

# shapefile

A `shapefile` is a widely used geospatial vector data format for storing geographic features' location, shape, and associated attributes, developed by Esri for geographic information system (GIS) software. It consists of a collection of related files `(e.g., .shp, .shx, .dbf)` that collectively define a single feature class containing points, lines, or polygons. While historically used in desktop GIS applications, shapefiles are now compatible with many other GIS and business intelligence programs.

ESRI Shapefile（shp），或簡稱shapefile，是美國環境系統研究所公司（ESRI）開發的空間數據開放格式。目前，該檔案格式已經成為了地理資訊軟件界的開放標準，這表明ESRI公司在全球的地理資訊系統市場的重要性。Shapefile也是重要的交換格式，能夠在ESRI與其他公司的產品之間進行數據互操作。Shapefile檔案用於描述幾何體物件：點、折線與多邊形。例如，Shapefile檔案可以儲存井、河流、湖泊等空間物件的幾何位置。除了幾何位置，shp檔案也可以儲存這些空間物件的屬性，例如河流的名字、城市的溫度等等。

Shapefile屬於一種向量圖形格式，它能夠儲存幾何圖形的位置及相關屬性。但這種格式沒法儲存地理數據的拓撲資訊。Shapefile在九十年代初的ArcView GIS的第二個版本被首次應用。目前，許多自由的程式或商業的程式都可以讀取Shapefile。

Shapefile是一種比較原始的向量數據儲存方式，它僅僅能夠儲存幾何體的位置數據，而無法在一個檔案之中同時儲存這些幾何體的屬性數據。因此，Shapefile還必須附帶一個二維表用於儲存Shapefile中每個幾何體的屬性資訊。Shapefile中許多幾何體能夠代表複雜的地理事物，並為他們提供強大而精確的計算能力。

## Key Characteristics

- Vector Data:
  - Shapefiles store data as points, lines, and polygons, representing geographic features. 
- Attribute Data:
  - They also store associated descriptive data for each feature, like names, addresses, or statistics. 
- Multiple Files:
  - A shapefile is not a single file but a collection of related files that must be kept together. 
- Single Geometry Type:
  - Each shapefile can only contain one type of geometry (points, lines, or polygons). 

## Common Files in a Shapefile

- A shapefile typically includes several mandatory and optional files, all with the same base name: 
  - `.shp`: The main file that stores the feature geometry. 
  - `.shx`: The index file, which stores an index of the feature geometry. 
  - `.dbf`: A dBASE table file containing the attributes for each feature. 
  - `.prj`: (Optional, but essential for many functions) A projection file that defines the coordinate system. 

- Uses
  - Geographic Mapping: Visualizing real-world features on a map. 
  - Spatial Analysis: Performing calculations and analyses based on the location and relationships of geographic data. 
  - Data Exchange: Sharing geographic data between different GIS software and business intelligence programs. 

- Limitations 
  - File Size: Shapefiles have a **2GB size limit**.
  - Field Name Length: **Field names are limited to 10 characters**.
  - Topological Information: They cannot store topological information, which describes the relationships between features.

# NCL: Plotting and working with shapefiles

- <https://www.ncl.ucar.edu/Applications/shapefiles.shtml>

1. Download **shapefile_utils.ncl**
2. Create a new `plot_shp.sh` and `plot_shp.ncl` 

```plot_shp.sh
#!/bin/bash
source /home/wpsze/micromamba/bin/activate venv
export shp_name=${1}
ncl /home/wpsze/NCL/shapefiles/plot_shp.ncl
```

```plot_shp.ncl
load "/home/wpsze/NCL/shapefiles/shapefile_utils.ncl"
sname = getenv("shp_name") ;"Hong_Kong_18_Districts.shp"
print_shapefile_info(sname)
plot_shapefile(sname)
```

{% fold info @shapefile_utils.ncl %}
```ncl
;======================================================================
; This script contains a number of functions and procedures for
; working with shapefiles in NCL.
;
; print_shapefile_info - prints basic information about a shapefile.
;
; plot_shapefile - plots data in a shapefile over a map.
;
; shapefile_mask_data - masks a data array based on outlines in 
;   a shapefile
;======================================================================

;======================================================================
; Given a shapefile name, i.e. "xxxx_yyyy.shp", this function returns
; the prefix "xxxx_yyyy".
;======================================================================
undef("get_shapefile_prefix")
function get_shapefile_prefix(shapefile_name[1]:string)
begin
  s = str_split(shapefile_name,".")
  ns = dimsizes(s)
  if(ns.eq.1)
    return(s)
  else
    return(str_join(s(0:ns-2),"_"))
  end if
end

;======================================================================
; print_shapefile_info(shapefile_name)
;     shapefile_name - Name of shapefile, i.e."AUS_adm0.shp"
;
; This procedure prints basic information about a shapefile.
;======================================================================
undef("print_shapefile_info")
procedure print_shapefile_info(shapefile_name[1]:string)
local n, f, var_names, geo_dims, num_features, lat, lon, nvars, dq
begin
  dq = str_get_dq()

;---Open shapefile
  f = addfile(shapefile_name,"r")

;---Read data off shapefile
  var_names    = getfilevarnames(f)                  ; variable names
  geo_dims     = getfilevardimsizes(f,"geometry")    ; features on file
  num_features = geo_dims(0)
  lon          = f->x
  lat          = f->y
  nvars        = dimsizes(var_names)

  print("======================================================================")
  print("Filename: " + dq + shapefile_name + dq)
  print("   Geometry type: " + f@geometry_type)
  print("   # of features: " + num_features) 
  print("   Min/max lat:   " + sprintf("%7.2f",min(lat)) + "/" + sprintf("%7.2f",max(lat)))
  print("   Min/max lon:   " + sprintf("%7.2f",min(lon)) + "/" + sprintf("%7.2f",max(lon)))
  print("   Variable names and their types:")
  do n=0,nvars-1
     print("       " + var_names(n) + " : " + getfilevartypes(f,var_names(n)))
  end do
  print("======================================================================")
end

;======================================================================
; shapefile_mask_data
;
; This function masks a rectilinear, curvilinear, or unstructured
; data array based on either all the outlines in a shapefile, or 
; based on a string variable name in a shapefile and a list of strings
; to check for, like "Water body" or (/"Ohio","Michigan"/). 
;
; You have the option to return the mask array, rather than the masked
; data array.
;
; Input paramaters
;  data         : numeric - 1D or 2D data to mask or base mask array on
;  shp_file_name[1] : string  - Name of shapefile
;  opt[1]       : logical - Use to set options for this function. If
;                           set to False, then all options will be ignored.
;
;  "opt" can have the following attributes:
;
;   "keep"        - Whether to keep the values in the given shapefile
;                   areas or toss them.
;                   [default True]
;
;   "shape_var"   - Name of variable on shapefile that contains the
;                   string names of specific areas you want to mask.
;                   [default is to use the whole shapefile]
;
;   "shape_names" - List of string names in "shape_name" to mask against
;                   [no default]
;
;   "return_mask" - Whether to return a mask array (0s and 1s) 
;                   instead of the masked data.
;                   [default False]
;
;   "minlat", "maxlat", "minlon", "maxlon" - You can tell the masking 
;                   routine what rough lat/lon box you are interested 
;                   in, so that it doesn't check every lat/lon segment
;                   in the shapefile.
;                   [default is the min/max of the lat/lon on shapefile]
;
;   "loop_check"  - Whether to do a min/max lat/lon check for every loop iteration.
;                   I tested this on four examples, and it sped every one of them up.
;                   I decided to make this True by default.;
;                   [default True]
;
;   "debug"       - Whether to print debug information.
;                   [default False]
;
;  - If a rectilinear grid, then "data" must have coordinate arrays
;    attached. 
;  - If a curvilinear grid, then "data" must have the special lat2d 
;    and lon2d attributes attached.
;  - If a unstructured grid, then "data"must have the special lat1d 
;    and lon1d attributes attached.
;======================================================================
undef("shapefile_mask_data")
function shapefile_mask_data(data:numeric,shapefile_name[1]:string,\
                             opt[1]:logical)
local mask_start_time, mask_end_time,keep_true_value, keep_false_value, \
dims, rank, grid_type, lat1d, lon1d, nlatlon1d, f, segments, geometry, \
segsDims, geomDims, geom_segIndex, geom_numSegs, segs_xyzIndex, 
segs_numPnts,numFeatures, lat, lon, shp_mask_names, found, nf, numFeatures, \
startSegment, numSegments, seg, startPT, endPT, lon_sub, lat_sub, \
min_lat_shp, max_lat_shp, min_lon_shp, max_lon_shp
begin
  mask_start_time = get_cpu_time()

;---Make sure we can open shapefile
  if(.not.isfilepresent(shapefile_name)) then
    print("shapefile_mask_data : Error: " + shapefile_name + \
          " either doesn't exist or is not a valid shapefile.")
    exit
  end if
  f = addfile(shapefile_name,"r")

;---Parse options and check for errors
  DEBUG       = get_res_value_keep(opt,"debug",False)
  KEEP        = get_res_value_keep(opt,"keep",True)
  RETURN_MASK = get_res_value_keep(opt,"return_mask",False)
  LOOP_CHECK  = get_res_value_keep(opt,"loop_check",True)
  SHP_VAR     = opt.and.isatt(opt,"shape_var")
  if(opt.and.isatt(opt,"shape_var")) then
    if(.not.isatt(opt,"shape_names")) then
      print("shapefile_mask_data : Error: if you set 'shape_var' you must also set 'shape_names'")
      exit
    end if
    shp_var_name   = opt@shape_var
    usr_mask_names = opt@shape_names

;---Make sure shp_var_name is on shapefile.
    if(isfilevar(f,shp_var_name)) then
      shp_mask_names = f->$shp_var_name$
    else
      print("shapefile_mask_data : Error: The given variable name to use does not exist on the given shapefile.")
      exit
    end if

;---Make sure usr_mask_names has at least one match in shp_mask_names
    num_found = 0
    nusr_mask_names = dimsizes(usr_mask_names)
    do i=0,nusr_mask_names-1
      if(any(usr_mask_names(i).eq.shp_mask_names)) then
        num_found = num_found+1
      end if
    end do
    if(num_found.eq.0) then
      print("shapefile_mask_data : Error: none of the given mask_names match the names on the shapefile.")
      exit
    end if
    if(num_found.lt.nusr_mask_names) then
      print("shapefile_mask_data : warning: Only found " + num_found + \
            " of the " + nusr_mask_names)
      print("                      given mask_names on the shapefile.")
    end if
  end if

  if(KEEP) then
    keep_true_value  = 1  ; 1 ==> values inside given mask areas will be kept
    keep_false_value = 0
  else
    keep_true_value  = 0  ; 0 ==> values inside given mask areas will be tossed
    keep_false_value = 1
  end if

;---Determine the grid type
  dims = dimsizes(data)
  rank = dimsizes(dims)

  grid_type = ""
  if(rank.eq.2.and.\
     isdimnamed(data,0).and.iscoord(data,data!0).and.\
     isdimnamed(data,1).and.iscoord(data,data!1)) then
    lat1d = ndtooned(conform_dims(dims,data&$data!0$,0))
    lon1d = ndtooned(conform_dims(dims,data&$data!1$,1))
    grid_type = "rectilinear"
  else if(rank.eq.2.and.all(isatt(data,(/"lat2d","lon2d"/)))) then
;---Curvilinear
    lat1d = ndtooned(data@lat2d)
    lon1d = ndtooned(data@lon2d)
    if(product(dims).eq.dimsizes(lat1d).and.\
       product(dims).eq.dimsizes(lon1d)) then
      grid_type = "curvilinear"
    end if
  else if(rank.eq.1.and.all(isatt(data,(/"lat1d","lon1d"/)))) then
;---Unstructured
    lat1d = data@lat1d
    lon1d = data@lon1d
    if(dims.eq.dimsizes(lat1d).and.\
       product(dims).eq.dimsizes(lon1d)) then
      grid_type = "unstructured"
    end if
  end if
  end if
  end if

  if(grid_type.eq."") then
    print("shapefile_mask_data: Error: not a valid rectilinear, curvilinear, or unstructured grid")
    exit
  end if
  nlatlon1d = dimsizes(lat1d)

;---Read data off the shapefile
  segments = f->segments
  geometry = f->geometry
  segsDims = dimsizes(segments)
  geomDims = dimsizes(geometry)

;---Read global attributes  
  geom_segIndex = f@geom_segIndex
  geom_numSegs  = f@geom_numSegs
  segs_xyzIndex = f@segs_xyzIndex
  segs_numPnts  = f@segs_numPnts
  numFeatures   = geomDims(0)

;---Read shapefile lat/lon
  lon = f->x
  lat = f->y
;
; If shp_var_name is specified, then get the approximate lat/lon box that 
; encloses the shapefile areas of interest. This can make the 
; gc_inout checking go faster later. If the user has input
; all four "usr_min/max_lat/lon" attributes, then don't do the check.
;
  if(SHP_VAR.and..not.(opt.and.isatt(opt,"minlat").and.isatt(opt,"maxlat").and.\
                               isatt(opt,"minlon").and.isatt(opt,"maxlon"))) then
    found = False
    do nf=0,numFeatures-1
      if(any(shp_mask_names(nf).eq.usr_mask_names)) then
        startSegment = geometry(nf, geom_segIndex)
        numSegments  = geometry(nf, geom_numSegs)
        do seg=startSegment, startSegment+numSegments-1
          startPT = segments(seg, segs_xyzIndex)
          endPT   = startPT + segments(seg, segs_numPnts) - 1
          lat_sub := lat(startPT:endPT)
          lon_sub := lon(startPT:endPT) 
          if(found) then
            min_lat_shp = min((/min_lat_shp,min(lat_sub)/))
            max_lat_shp = max((/max_lat_shp,max(lat_sub)/))
            min_lon_shp = min((/min_lon_shp,min(lon_sub)/))
            max_lon_shp = max((/max_lon_shp,max(lon_sub)/))
          else
            min_lat_shp = min(lat_sub)
            max_lat_shp = max(lat_sub)
            min_lon_shp = min(lon_sub)
            max_lon_shp = max(lon_sub)
            found       = True
          end if
        end do
      end if
    end do
  else
;---Use the whole shapefile
    min_lat_shp = min(lat)
    max_lat_shp = max(lat)
    min_lon_shp = min(lon)
    max_lon_shp = max(lon)
  end if
  
;--lat/lon coordinates of data array
  min_lat_data = min(lat1d)
  max_lat_data = max(lat1d)
  min_lon_data = min(lon1d)
  max_lon_data = max(lon1d)

;---min/max lat/lon to use for checking the data lat/lon
  min_lat_chk = get_res_value(opt,"minlat",min_lat_shp)
  max_lat_chk = get_res_value(opt,"maxlat",max_lat_shp)
  min_lon_chk = get_res_value(opt,"minlon",min_lon_shp)
  max_lon_chk = get_res_value(opt,"maxlon",max_lon_shp)

;---Get index values where data lat/lon values fall inside this "box".
  if(.not.LOOP_CHECK) then
    ii_latlon = ind(min_lat_chk.le.lat1d.and.lat1d.le.max_lat_chk.and.\
                    min_lon_chk.le.lon1d.and.lon1d.le.max_lon_chk)
    nii = dimsizes(ii_latlon)
  end if

  if(DEBUG) then
    print("==================================================")
    print("Shapefile:         " + shapefile_name)
    if(SHP_VAR) then
      print("Areas of interest: " + str_join(""+usr_mask_names,","))
    else
      print("Areas of interest: the whole shapefile")
    end if
    print("min_lat_chk:       " + min_lat_chk)
    print("max_lat_chk:       " + max_lat_chk)
    print("min_lon_chk:       " + min_lon_chk)
    print("max_lon_chk:       " + max_lon_chk)
    print("min_lat_data:      " + min_lat_data)
    print("max_lat_data:      " + max_lat_data)
    print("min_lon_data:      " + min_lon_data)
    print("max_lon_data:      " + max_lon_data)
    print(dimsizes(lat1d) + " data values originally")
    if(.not.LOOP_CHECK) then
      print(nii + " data values within given shapefile areas.")
    end if
    if(keep_true_value.eq.1) then
      print("Will keep data values inside given shapefile areas")
    else
      print("Will toss data values inside given shapefile areas")
    end if
  end if

;---Create array to hold new data mask
  data_mask_1d = new(nlatlon1d,integer)
  data_mask_1d = keep_false_value

;
; Setting opt@loop_check = True seems to produce the best results, timing-wise.
; Maybe get rid of the "else" part of this "if" statement at some point?
;
  if(LOOP_CHECK) then
    do nf=0,numFeatures-1
      if(.not.SHP_VAR.or.(SHP_VAR.and.\
         any(shp_mask_names(nf).eq.usr_mask_names))) then
        startSegment = geometry(nf, geom_segIndex)
        numSegments  = geometry(nf, geom_numSegs)
        do seg=startSegment, startSegment+numSegments-1
          startPT = segments(seg, segs_xyzIndex)
          endPT   = startPT + segments(seg, segs_numPnts) - 1
          lat_sub := lat(startPT:endPT)
          lon_sub := lon(startPT:endPT) 
          ii_latlon := ind(min(lat_sub).le.lat1d.and.lat1d.le.max(lat_sub).and.\
                           min(lon_sub).le.lon1d.and.lon1d.le.max(lon_sub))
          if(any(ismissing(ii_latlon))) then
            continue
          end if          
          nii = dimsizes(ii_latlon)
          do n=0,nii-1
            iltln = ii_latlon(n)
            if(data_mask_1d(iltln).ne.keep_true_value.and.\
               gc_inout(lat1d(iltln),lon1d(iltln),lat_sub,lon_sub)) then
              data_mask_1d(iltln) = keep_true_value
            end if
          end do
        end do
      end if
    end do
  else
    do n=0,nii-1
      iltln = ii_latlon(n)
      do nf=0,numFeatures-1
        if(data_mask_1d(iltln).ne.keep_true_value.and.\
           (.not.SHP_VAR.or.(SHP_VAR.and.\
           any(shp_mask_names(nf).eq.usr_mask_names)))) then
          startSegment = geometry(nf, geom_segIndex)
          numSegments  = geometry(nf, geom_numSegs)
          do seg=startSegment, startSegment+numSegments-1
            startPT = segments(seg, segs_xyzIndex)
            endPT   = startPT + segments(seg, segs_numPnts) - 1
            lat_sub := lat(startPT:endPT)
            lon_sub := lon(startPT:endPT) 
            if(.not.(all(lon_sub.lt.min_lon_data).or. \
                     all(lon_sub.gt.max_lon_data).or. \
                     all(lat_sub.lt.min_lat_data).or. \
                     all(lat_sub.gt.max_lat_data)).and.\
               gc_inout(lat1d(iltln),lon1d(iltln),lat_sub,lon_sub)) then
              data_mask_1d(iltln) = keep_true_value
              break
            end if
          end do
        end if
      end do
    end do
  end if
  if(DEBUG) then
    print("==================================================")
    if(KEEP) then
      print(num(data_mask_1d.eq.keep_true_value) + " data values kept")
   else
      print(num(data_mask_1d.ne.keep_true_value) + " data values kept")
    end if
  end if
;
; Create a 2D data array of same size as original data,
; but with appropriate values masked. Create a missing
; value if our data doesn't have one.
;
  if(.not.isatt(data,"_FillValue")) then
    data_msg = default_fillvalue(typeof(data))
  else
    data_msg = data@_FillValue
  end if

;---Keep all the locations where the mask array is 1.
  if(RETURN_MASK) then
    data_mask = onedtond(data_mask_1d,dims)
  else
    data_mask = where(onedtond(data_mask_1d,dims).eq.1,data,data_msg)
    copy_VarMeta(data,data_mask)      ; Copy all metadata

    if(.not.isatt(data,"_FillValue")) then
      data_mask@_FillValue = data_msg
    end if
  end if

  if(DEBUG) then
;---Print timings
    mask_end_time = get_cpu_time()
    print("shapefile_mask_data: elapsed time: " + \
          (mask_end_time-mask_start_time) + " CPU seconds.")
    print("==================================================")
  end if
  return(data_mask)
end

;======================================================================
; create_shapefile_map(wks,shapefile_name,geometry_type,lat,lon)
;          wks            - workstation
;
;          shapefile_name - Name of shapefile, i.e."AUS_adm0.shp"
;
;          geometry_type  - Geometry type, read off shapefile via 
;                           "geometry_type" attribute. Must be 
;                           "point", "polylines", or "polygon".
;                           As of NCL V6.5.0, this routine will 
;                           use "point" if the geometry is not
;                           set or not recognized.
;
;          lat,lon        - Arrays containing lat/lon values - can be
;                           read off file via "y" and "x" arrays on
;                           shapefile.
;
; This function creates a map for which to add shapefile outlines 
; or points. It uses the shapefile_name for a title, and the geometry
; type to determine whether to draw map outlines.
;
; Note: this function could have been written to open the shapefile
; and read the geometry type and lat/lon, but since it is likely
; to be called from a routine that has to read lat/lon anyway, it 
; seemed best just to pass this information in, to save time and
; memory.
;======================================================================
undef("create_shapefile_map")
function create_shapefile_map(wks,shapefile_name,geometry_type,lat,lon)
local res
begin
  res                       = True

  res@tiMainString          = shapefile_name

  res@gsnMaximize           = True     ; maximize plot in frame
  res@gsnDraw               = False
  res@gsnFrame              = False

  if(geometry_type.eq."point") then
    res@mpOutlineOn           = True
    res@mpOutlineBoundarySets = "AllBoundaries"
  else
    res@mpFillOn              = False
    res@mpOutlineOn           = False
  end if
  res@mpMinLatF             = min(lat)
  res@mpMaxLatF             = max(lat)
  res@mpMinLonF             = min(lon)
  res@mpMaxLonF             = max(lon)

  res@pmTickMarkDisplayMode = "Always"

  map = gsn_csm_map(wks,res)
  
  return(map)
end

;======================================================================
; plot_shapefile - Given a shapefile name ("AUS_adm0.shp"), this 
; procedure draws a map with the data from the given shapefile drawn
; on top.
;
; The data is drawn depending on the "geometry_type" attribute of 
; the shapefile:
;
;    - "points"   - data is drawn as filled black circles
;    - "polyline" - data is drawn as black polylines
;    - "polygon"  - data is drawn as colored, filled polygons, with 
;                   black polylines.
;======================================================================
undef("plot_shapefile")
procedure plot_shapefile(shapefile_name)
local f, wks, shp_type, sres, geometry_type
begin
;---Open file
  f = addfile(shapefile_name,"r")
;---Check validity of shapefile
  if(.not.isatt(f,"geometry_type")) then
    print("plot_shapefile: Error: This doesn't appear to be a valid shapefile")
    print("No 'geometry_type' attribute is present.")
    exit
  else if(.not.any(f@geometry_type.eq.(/"point","polyline","polygon"/))) then
    print("plot_shapefile: Warning: the 'geometry_type' attribute on '" + shapefile_name + "'") 
    print("             is not set to 'point', 'polyline', or 'polygon'.")
    print("             Will try to plot this data using 'point', but you")
    print("             may want to examine the file further to make sure")
    print("             it is correct.")
    geometry_type = "point"
  else
    geometry_type = f@geometry_type
  end if
  end if

;---Open device to draw graphics.
  wks = gsn_open_wks("png",get_shapefile_prefix(shapefile_name))

;---Read lat/lon and create map
  lat = f->y
  lon = f->x
  map = create_shapefile_map(wks,shapefile_name,geometry_type,lat,lon)
  
  sres = True
  if(geometry_type.eq."point") then
    sres@gsMarkerIndex = 16
    id = gsn_add_shapefile_polymarkers(wks,map,shapefile_name,sres)
  else if(geometry_type.eq."polyline") then
    id = gsn_add_shapefile_polylines(wks,map,shapefile_name,sres)
  else if(geometry_type.eq."polygon") then
    sres@gsFillColor = "SkyBlue"
    id1 = gsn_add_shapefile_polygons(wks,map,shapefile_name,sres)
    id2 = gsn_add_shapefile_polylines(wks,map,shapefile_name,sres)
  end if
  end if
  end if

  draw(map) ; This will draw map and attached outlines or markers
  frame(wks)
end
```
{% endfold %}

## Example

```log
(0)	======================================================================
(0)	Filename: "Hong_Kong_18_Districts.shp"
(0)	   Geometry type: polygon
(0)	   # of features: 18
(0)	   Min/max lat:     22.15/  22.56
(0)	   Min/max lon:    113.84/ 114.44
(0)	   Variable names and their types:
(0)	       geometry : integer
(0)	       segments : integer
(0)	       x : double
(0)	       y : double
(0)	       OBJECTID : int64
(0)	       TCNAME : string
(0)	       ENAME : string
(0)	       Shape__Are : double
(0)	       Shape__Len : double
(0)	======================================================================
```

![](https://i.imgur.com/htVo0nI.png){width=400}


# Download

- [Download GADM data (version 4.1)](https://gadm.org/download_country.html)
  - shapefiles
  - geojson
  - **KMZ for Google Map** 
    - ![](https://i.imgur.com/aLxlrpC.png)

# Files

## ne_10m_admin_0_countries (Admin 0 – Countries)

- <https://www.naturalearthdata.com/downloads/10m-cultural-vectors/10m-admin-0-countries/>

![](https://i.imgur.com/xtBhEYc.png){width=400}

## gadm36_CHN

![](https://i.imgur.com/TrINpU0.png){width=400}

## ChinaAdminDivisonSHP-23_01_04

![](https://i.imgur.com/ix8iMG3.png){width=400}

## US_States_and_Territories_18mr25

![](https://i.imgur.com/Dlp4pod.png){width=400}
