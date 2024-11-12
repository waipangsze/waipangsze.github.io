---
layout: post
title: "ERA5 ERA5T expver dimension issue"
categories: [NWP]
tags: [WRF,WPS,MPAS,IC/BC,ERA5,ECMWF]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://www.ecmwf.int/sites/default/files/flags-council-chamber-650px.jpg
banner_img: https://www.ecmwf.int/sites/default/files/flags-council-chamber-650px.jpg
---

# ERA5 ERA5T expver dimension issue

Ref: [ERA5 CDS requests which return a mixture of ERA5 and ERA5T data](https://confluence.ecmwf.int/display/CUSF/ERA5+CDS+requests+which+return+a+mixture+of+ERA5+and+ERA5T+data)

**The ERA5 hourly and monthly data are made available with a 3 month delay.** This means that after a month has passed, another month's worth of ERA5 data is written to the dataset.

**ERA5T (near real time)** preliminary data are used to fill the gap between the end of the ERA5 data and 5 days before the present date. The oldest month of these is overwritten each month as new ERA5 data become available.

> It depends on time, it is **expver either 1 or 5**, will not exist at the same time\
> If already past 3 months' time, it will use ERA5, otherwise use ERA5T if too recent

## For example,
So as an example, say we have a current date  of 15th February 2020:

> ERA5 data are currently from 1/1/1979 - 30/11/2019 (instantaneous variables)  and 1/1/1979 - 1/12/2019 (00-06 UTC, accumulated variables)\
> ERA5T data (with a 5 day delay) are from 1/12/2019- 10/2/2020 (instantaneous variables)  and 1/12/2019 (07-23 UTC, accumulated variables)- 10/2/2020

For requests which return a mixture of ERA5 and ERA5T data  (such as for data from the 1st of the month), instantaneous variables (e.g temperature) come from ERA5T (which has 'experiment version'  of 5) while accumulated variables (fluxes, precipitation) come from both datasets with the following structure:

> 00-06 UTC on 1 day of the month from **ERA5 (expver 1)**\
> 07-23 UTC on 1 day of the month (and the following dates up to 5 day from present) from **ERA5T (expver 5)**
When these data are converted to netCDF a new dimension is created called expver containing 1 and 5. Moreover, a single time coordinate is used which covers the entire requested period.

```sh
dimensions:
        longitude = 1440 ;
        latitude = 721 ;
        expver = 2 ;
        time = 24 ;
variables:
        float longitude(longitude) ;
                longitude:units = "degrees_east" ;
                longitude:long_name = "longitude" ;
        float latitude(latitude) ;
                latitude:units = "degrees_north" ;
                latitude:long_name = "latitude" ;
        int expver(expver) ;
                expver:long_name = "expver" ;
        int time(time) ;
                time:units = "hours since 1900-01-01 00:00:00.0" ;
                time:long_name = "time" ;
                time:calendar = "gregorian" ;
        short tp(time, expver, latitude, longitude) ;
                tp:scale_factor = 9.06276558810304e-07 ;
                tp:add_offset = 0.0296950577259784 ;
                tp:_FillValue = -32767s ;
                tp:missing_value = -32767s ;
                tp:units = "m" ;
                tp:long_name = "Total precipitation" ;
data:

 expver = 5, 1 ;
 ...
}
```

Both expver dimensions use the full time extent of time coordinate but the expver 1 data only covers the first 7 timesteps, the remaining timesteps are 'padded' with empty fields.
For the expver 5 data, the first 7 timesteps are padded with empty fields, with the remaining timesteps coming from the ERA5T data.

When the last ERA5 data are released, **they will overwrite the ERA5T data for the entire month and for accumulated variables for 00-06 in next month.** This process will be repeated each month.

