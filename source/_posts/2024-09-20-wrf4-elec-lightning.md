---
layout: post
title: Predicting lightning activity using WRF-ELEC model  
categories: [WRF]
tags: [WRF-ELEC, Lightning]
author: wpsze
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
---

# WRF-ELEC model

WRF-ELEC provides basic electrification and **lightning using the NSSL microphysics scheme**. The electrification parameterizations are based on past work in modeling. The basics of the charge separation schemes comes from Mansell et al. (2005). Their use in the **NSSL 2-moment microphysics** is shown in Mansell et al. 2010 and Mansell and Ziegler (2013). First implementation into WRF is described by Fierro et al. (2013), which includes details of the basic discharge scheme (cylindrical discharge regions centered on lightning initiation points).

- Mansell et al. 2005: Charge structure and lightning sensitivity in a simulated multicell thunderstorm. J. Geophys. Res., 110, D12101, doi:10.1029/2004JD005287
- Mansell, E. R., C. L. Ziegler, and E. C. Bruning, 2010: Simulated electrification of a small thunderstorm with two-moment bulk microphysics. J. Atmos. Sci., 67, 171-194, doi:10. 1175/2009JAS2965.1.
- **Fierro, A. O., E.R. Mansell, C. Ziegler and D. R. MacGorman 2013: The implementation of an explicit charging and discharge lightning scheme within the WRF-ARW model: Benchmark simulations of a continental squall line, a tropical cyclone and a winter storm. Monthly Weather Review, Volume 141, 2390-2415** (see below)

If you have trouble with this option please contact the developers directly (i.e., not wrf_help):

> Ted Mansell (Ted.Mansell _at_ noaa.gov)\
> Alexandre Fierro (Alex.Fierro _at_ noaa.gov) 

## BoxMG 

- Black Box Multigrid (BoxMG) algorithm

From <https://github.com/MicroTed/wrf4-elec/blob/7c90e71bdc4db527d0ec034804e4fd200cdacf28/doc/README.electricity#L41>, 

The **BoxMG elliptic equation solver** is required for WRF-ELEC to calculate the 3D electric potential. This library is distributed at the moment as a source code archive at

- <http://sourceforge.net/projects/boxmg4wrf/>

The git repository is recommended as the most up-to-date. Note that the code has only been compiled for Intel/AMD (non-Itanium) architecture under Linux and OS X using either Intel Fortran or GNU compilers. (For problems, contact <ted.mansell@noaa.gov>)

{% note primary %}
**BoxMG needs to be installed before configuring WRF.**
{% endnote %}

Install the code where you want the libraries to be and then follow the instructions in the INSTALL document:

```sh
$ git clone https://git.code.sf.net/p/boxmg4wrf/git boxmg4wrf
$ ll
34M boxmg4wrf
```

- <https://sourceforge.net/p/boxmg4wrf/git/ci/main/tree/>
  - Branches: main, boxmg4wrf-0.1, master, release0.0


git logg (2024-09-24)

```
* d8e89bc (HEAD -> master, origin/master, origin/HEAD)  Fix mpi calls to use single or double precision as set by RKIND (11 months ago) <Ted Mansell>
* 817df7a  Update to compile on M1 mac (assumes using gfortran as compiler) (2 years ago) <Ted Mansell>
* 3cee0c8 (origin/boxmg4wrf-0.1) Added comment (2 years, 9 months ago) <Ted Mansell>
* 71c38c1 - Added a check for gcc version 10 or greater to add the -fallow-argument-mismatch compile flag - Updates to test example ex_direct_1_f90 (e.g., double quotes on preprocessed include files) (2 years, 9 months ago) <Ted Mansell>
* 0e19676 erm: add sgl/dbl suffix for libs (3 years, 7 months ago) <Ted Mansell>
* 6682023 erm: turn off all but the one that works (currently) (3 years, 7 months ago) <Ted Mansell>
* 3644096 erm: added dependcies for "check" (3 years, 7 months ago) <Ted Mansell>
* d98ff35 erm: Minor changes for clarity (hopefully) (3 years, 7 months ago) <Ted Mansell>
* aaaa34c erm: minor updates (3 years, 7 months ago) <Ted Mansell>
* b54bb70 erm: added .gitignore file (3 years, 7 months ago) <Ted Mansell>
* d96cb63 (origin/release0.0) Initial commit (3 years, 7 months ago) <Ted Mansell>
```

Compile:

- wrf_env.sh: go to <https://waipangsze.github.io/2023/04/25/WRF_installation_on_a_Linux_based_machine/>

To check environment variables,

- /ARCH/machine

```
  ifeq ($(findstring x86_64,$(UNAME_M)),x86_64)

    BOXMG_CPU = AMD64
```

- add these to Makefile to print variables,

```
$(info $$BOXMG_OS is [${BOXMG_OS}])
$(info $$BOXMG_CPU is [${BOXMG_CPU}])
$(info $$BOXMG_COMPILER is [${BOXMG_COMPILER}])
$(info $$BOXMG_ARCHmake is [${BOXMG_ARCHmake}])
$(info $$BOXMG_ARCH is [${BOXMG_ARCH}])
$(info $$BOXMG_PRECISION is [${BOXMG_PRECISION}])
$(info $$BOXMG_MPI is [${BOXMG_MPI}])
```

- $ make

```
 $ uname -a
Linux xxx 4.18.0-477.10.1.el8_8.x86_64 #1 SMP Tue May 16 11:38:37 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux

$BOXMG_OS is [Linux]
$BOXMG_CPU is [AMD64]
$BOXMG_COMPILER is [gnu]
$BOXMG_ARCHmake is [./ARCH]
$BOXMG_ARCH is [Linux-AMD64]
$BOXMG_PRECISION is [SINGLE]
$BOXMG_MPI is [yes]
```

```sh
#!/bin/bash
export WRF_ENV="/home/wpsze/WRF/WRFv440/wrf_env.sh"
source ${WRF_ENV}

gcc --version
gfortran --version
mpirun --version
which mpif90 & mpif90 --version
which mpif77 # Must

export BOXMG_MPI=yes
make
```

{% note success %}
The libraries ( libboxmg_opt_sgl.a, libboxmg-extras_opt_sgl.a ) will be in ./lib.
{% endnote %}

install process:

```sh
.......
.......
gfortran   -funderscoring -DRKIND=4  -Waliasing -Wsurprising -O3  -c -o BMG_rand.o BMG_rand.f
ar csrv  ../../lib/libboxmg-extras_opt_sgl.a BMG_rand.o
a - BMG_rand.o
rm BMG_rand.o

 $ ll lib
total 1.6M
drwxrwxr-x  2 wpsze wpsze  33K Sep 23 16:32 ./
drwxrwxr-x 11 wpsze wpsze  33K Sep 23 16:28 ../
-rw-rw-r--  1 wpsze wpsze    0 Sep 23 16:13 .keepme
-rw-rw-r--  1 wpsze wpsze 1.4M Sep 23 16:32 libboxmg-extras_opt_sgl.a
-rw-rw-r--  1 wpsze wpsze 1.4M Sep 23 16:30 libboxmg_opt_sgl.a
```

- [Error while Making BoxMG?](https://forum.mmm.ucar.edu/threads/error-while-making-boxmg.12977/)


### Test BoxMG

1. Go to /tests and make

```sh
#!/bin/bash
export WRF_ENV="/home/wpsze/WRF/WRFv440/wrf_env.sh"
source ${WRF_ENV}

gcc --version
gfortran --version
mpirun --version
which mpif90 & mpif90 --version
which mpif77

export LDFLAGS=-L/EM/wpsze/WRF/WRF4-elec/wrf_install/boxmg4wrf/lib
export LD_LIBRARY_PATH=/EM/wpsze/WRF/WRF4-elec/wrf_install/boxmg4wrf/lib:$LD_LIBRARY_PATH
export CPPFLAGS=-I/EM/wpsze/WRF/WRF4-elec/wrf_install/boxmg4wrf/include
echo ${LD_LIBRARY_PATH}

export BOXMG_MPI=yes

make
```

it shows,

```
 - building boxmg-sym-std-2D objects in libboxmg_opt_sgl.a ... 
 - building boxmg-sym-std-3D objects in libboxmg_opt_sgl.a ... 
 - linking objects .... 
 - the executable ex_direct_1_f90 has been created.
```

- boxmg-sym-std-3D/ex_direct_1_f90 is successful.
- All the rest fails. (why? related RKIND issue.)

### Configure/compile WRF

{% note danger %}
Important note: \
Compile WRF using **only one core** with BoxMG !!! \
./compile -j 1 em_real 2>&1 | tee compile.log
{% endnote %}

To configure WRF, two environment variables must be set: WRF_ELEC and BOXMG

For bash:

```sh
export WRF_ELEC=1
export BOXMGLIBDIR=[path to boxmg directory]
```

For example, if the boxmg library directory is /home/wpsze/WRF/WRF4-elec/wrf_install/boxmg4wrf/lib, then (for bash)

```sh
export BOXMGLIBDIR=/home/wpsze/WRF/WRF4-elec/wrf_install/boxmg4wrf/
```

Then run 'configure' in the WRF main directory and select an option for **'dmpar' (distributed memory parallel)**. The code is set up only to use the MPI parallel solver from BoxMG, so do not compile for serial mode. (It can still be run on a single processor, however.)

## MicroTed/wrf4-elec

<https://github.com/MicroTed/wrf4-elec>

The impact of initial conditions in predicting lightning activity using the Weather Research and Forecasting (WRF) with the electrification (ELEC) extra package, known as WRF-ELEC model, has been investigated.

### An example of namelist.input

<https://github.com/MicroTed/wrf4-elec/blob/7c90e71bdc4db527d0ec034804e4fd200cdacf28/test/em_quarter_ss/electest/namelist.input#L55>

```
 &physics
 mp_physics                          = 18,     1,     1,
 elec_physics                        = 1,
 nssl_ipelec                         = 2,
 nssl_cccn                           = 0.8e9,
 nssl_isaund                         = 11,
 nssl_iscreen                        = 1,
 nssl_idischarge                     = 1,
 nssl_ibrkd                          = 4,
 ra_lw_physics                       = 0,     0,     0,
 ra_sw_physics                       = 0,     0,     0,
 radt                                = 30,    30,    30,
 sf_sfclay_physics                   = 0,     0,     0,
 sf_surface_physics                  = 0,     0,     0,
 bl_pbl_physics                      = 0,     0,     0,
 bldt                                = 0,     0,     0,
 cu_physics                          = 0,     0,     0,
 cudt                                = 5,     5,     5,
 num_soil_layers                     = 5,
 /
```

## README.electricity

{% note primary %}
<https://github.com/MicroTed/wrf4-elec/blob/7c90e71bdc4db527d0ec034804e4fd200cdacf28/doc/README.electricity#L41>
{% endnote %}

## wrf4-elec's registry.elec

<https://github.com/MicroTed/wrf4-elec/blob/main/Registry/registry.elec>

{% note primary %}
Look at namelist !!
{% endnote %}

```
# SPACE CHARGE EXPLICIT LIGHTNING
state   real    scr            ikjftb  scalar      1         -     \
   i0rhusdf=(bdy_interp:dt)    "SCR"       "Rain space charge mixing ratio" "# C kg(-1)"
state   real    scw            ikjftb  scalar      1         -     \
   i0rhusdf=(bdy_interp:dt)    "SCW"       "cloud water space charge mixing ratio" "# C kg(-1)"
state   real    sci            ikjftb  scalar      1         -     \
   i0rhusdf=(bdy_interp:dt)    "SCI"       "cloud ice space charge mixing ratio" "# C kg(-1)"
state   real    scs            ikjftb  scalar      1         -     \
   i0rhusdf=(bdy_interp:dt)    "SCS"       "snow space charge mixing ratio" "# C kg(-1)"
state   real    sch            ikjftb  scalar      1         -     \
   i0rhusdf=(bdy_interp:dt)    "SCH"       "graupel water space charge mixing ratio" "# C kg(-1)"
state   real    schl           ikjftb  scalar      1         -     \
   i0rhusdf=(bdy_interp:dt)    "SCHL"       "hail water space charge mixing ratio" "# C kg(-1)"
state   real    sciona          ikjftb  scalar      1         -     \
   i0rhusdf=(bdy_interp:dt)    "SCIONA"       "Passive net ion space charge" "# C kg(-1)"
state   real    clnox          ikjftb  scalar      1         -     \
   i0rhusdf=(bdy_interp:dt)    "CLNOX"      "Lightning NOx concentration" "# moles kg(-1)"
# END SPACE CHARGE EXPLICIT LIGHTNING

# EXPLICIT LIGHTNING
#
state    real    rscghis_2d      ij   misc    1         -     irh         "rscghis_2d"             "MAX NONINDUCTIVE CHARGING 2D"     "C m-2"
state    real    induc           ikj  misc    1         -     irh         "induc"                  "TOTAL INDUCTIVE CHARGING "     "C m-3"
state    real    noninduc        ikj  misc    1         -     irh         "noninduc"               "TOTAL NONINDUCTIVE CHARGING"     "C m-3"
state    real    sctot           ikj  misc    1         -     irh         "sctot"                  "Total Space Charge Density"     "C m-3"
state    real    elecmag         ikj  misc    1         -     irh         "elecmag"                "EFIELD MAGNITUDE"     "V m-1"
state    real    elecx           ikj  misc    1         -     irh         "elecx"                  "EFIELD X-Component"     "V m-1"
state    real    elecy           ikj  misc    1         -     irh         "elecy"                  "EFIELD Y-Component"     "V m-1"
state    real    elecz           ikj  misc    1         -     irh         "elecz"                  "EFIELD Z-Component"     "V m-1"
state    real    pot             ikj  misc    1         -     irh         "pot"                    "POTENTIAL"     "V"
state    real    light            ij  misc    1         -     irh         "light"                  "lightning flash initiations"         "flash origin density"
state    real    lightdens        ij  misc    1         -     irh         "lightdens"              "lightning flash density"    "flash column-1"
state    real    lightfod         ij  misc    1         -     irh         "lightfod"               "normalized lightning flash origin density"   "flash column-1"
state    real    flshfedic        ij  misc    1         -     irh         "flshfedic"              "IC lightning flash extent density"         "flash column-1"
state    real    flshfedicp       ij  misc    1         -     irh         "flshfedicp"             "IC pos lightning flash extent density"         "flash column-1"
state    real    flshfedicn       ij  misc    1         -     irh         "flshfedicn"             "IC neg lightning flash extent density"         "flash column-1"
state    real    flshfedcg        ij  misc    1         -     irh         "flshfedcg"              "CG lightning flash extent density"         "flash column-1"
state    real    flshfedcgn       ij  misc    1         -     irh         "flshfedcgn"             "CG neg lightning flash extent density"         "flash column-1"
state    real    flshfedcgp       ij  misc    1         -     irh         "flshfedcgp"             "CG pos lightning flash extent density"         "flash column-1"
state    real    lightdis         ij  misc    1         -     irh         "lightdis"               "lightning source density"     "Source column-1"
state    real    flshi           ikj  misc    1         -     irh         "flshi"                  "Lightning init points"     "count"
state    real    flshn           ikj  misc    1         -     irh         "flshn"                  "Negative channels"     "count"
state    real    flshp           ikj  misc    1         -     irh         "flshp"                  "Positive channels"     "count"
# END EXPLICIT LIGHTNING


# Explicit lightning
rconfig   integer  nssl_ipelec            namelist,physics      max_domains   0       rh       "Electrification selection"  ""      ""
rconfig   integer  nssl_isaund            namelist,physics      1             12      rh       "Charge separation selection"  ""      ""
rconfig   integer  nssl_iscreen           namelist,physics      1             0       rh       "Screening layer parameterization flag"  ""      ""
rconfig   real     nssl_lightrad          namelist,physics      1             12000   rh       "discharge cylinder radius (m)"  ""      ""
rconfig   integer  nssl_idischarge        namelist,physics      1             1       rh       "lightning discharge flag"  ""      ""
rconfig   integer  nssl_ibrkd             namelist,physics      1             4       rh       "Critical Breakeven Efield profile selection"  ""      ""
rconfig   real     nssl_elgtfestop        namelist,physics      1             0.8     rh       "3D lightning initial channel stopping fraction"  ""      ""
rconfig   real     nssl_ecrit             namelist,physics      1             120000  rh       "Critical Breakeven Efield magnitude for discharge (V/m) assuming height-constant Ecrit profile"  ""      ""
rconfig   real     nssl_disfrac           namelist,physics      1             0.3     rh       "percentile of charge removed upon discharge (BLM)"  ""      ""
rconfig   real     nssl_einternal         namelist,physics      1           200.0     rh       "Lightning channel internal field (MSZ)"  ""      ""
rconfig   real     nssl_tgrnd             namelist,physics      1           266.16    rh       "Temperature threshold for CG (MSZ)"  ""      ""
rconfig   real     nssl_zgrnd             namelist,physics      1             -1.     rh       "Height threshold for CG (MSZ)"  ""      ""
rconfig   integer  nssl_clnox             namelist,physics      1             0       rh       "Lightning NOX prediction flag"  ""      ""
# end Explicit lightning

rconfig   integer  elec_physics           namelist,physics      1            0       irh       "elec_physics"            ""      ""

# external WRF-ELEC package
package   noelec             elec_physics==0             -             -
package   eleclgt            elec_physics==1             -             scalar:scr,scw,sci,scs,sch,schl,sciona;state:rscghis_2d,sctot,noninduc,induc,pot,elecmag,elecx,elecy,elecz,light,lightdens,lightdis
# for case of no hail (nssl_2momg)
package   eleclgtg           elec_physics==11            -             scalar:scr,scw,sci,scs,sch,sciona;state:rscghis_2d,sctot,noninduc,induc,pot,elecmag,elecx,elecy,elecz,light,lightdens,lightdis,lightfod

package lightning1d          nssl_idischarge==1   -    state:lightfod
package lightning3d          nssl_idischarge==2   -    state:flshi,flshn,flshp,flshfedic,flshfedicp,flshfedicn,flshfedcg,flshfedcgp,flshfedcgn
# package lightning3da         nssl_idischarge==3   -    state:flshi,flshn,flshp

package nssl_cnox_off nssl_clnox==0   -   -
package nssl_cnox_on  nssl_clnox==1   -   scalar:clnox
```

here noted,

<!-- <style>
tr:nth-child(even) {
  background-color: #b2b2b2!important;
  !color: #f4f4f4!important;
}
</style> -->

| elec_physics  | nssl_ipelec  | nssl_idischarge | outputs |
|:------------- |:---------------:|:---------------:| :-------------|
| 1         | 2          | 1        | ? |
| 1         | 3          | 1        | scalar:scr,scw,sci,scs,sch,schl,sciona; <br> state:rscghis_2d,sctot,noninduc,induc,pot, <br> elecmag,elecx,elecy,elecz,light,lightdens,lightdis |
| 1         | 3          | 2       | state:flshi,flshn,flshp,flshfedic, <br> flshfedicp,flshfedicn,flshfedcg,flshfedcgp,flshfedcgn |

## Specific namelist.input options

```
Specific namelist.input options are given below, along with a list of output fields.


Electrification only works with the NSSL 2-moment options (with hail):

 mp_physics(max_dom)
                                     = 17, NSSL 2-moment 4-ice scheme (steady background CCN)
                                     = 18, NSSL 2-moment 4-ice scheme with predicted CCN (better for idealized than real cases)
                                       ; to set a global CCN value, use
                                     = 22, NSSL 2-moment 3-ice (hail species turned off)
                                       
Other variables in the &physics namelist:

 elec_physics                        = 0, electrification (and charge arrays) turned off (DEFAULT)
                                     = 1, electrification turned on (only works with mp_physics = 17, 18, or 22 i.e., 2-moment NSSL schemes)

 nssl_ipelec (max_dom)     ! NOTE: only set this to a nonzero value on the innermost domain
                                     = 0, charging turned off (DEFAULT)
                                     = 2, non-inductive charging only
                                     = 3, non-inductive + inductive charging
                                     = 1, not used (reserved for future use)

 nssl_idischarge                     = 0, no discharge
                                     = 1, Cylindrical "1D" lightning scheme based on Ziegler and MacGorman (1994, JAS) 
                                     = 2, 3D discrete discharge adapted from MacGorman et al. (2001)

 nssl_iscreen                        = 0, no screening layer (DEFAULT)
                                     = 1, screening layer scheme of Ziegler et al. (1991, JGR)
                                     = 2, screening layer only applied at cloud top (seems to be better for storm complexes and large storms)

 nssl_ibrkd                          = breakdown electric field profile to initiate lightning
                                     = 1, constant profile with height with value set by nssl_ecrit
                                     = 2 to 5, variants of vertical Ecrit profile of Dwyer (2003, GRL) - '4' is recommended (DEFAULT is 4).

 nssl_isaund                         = Calls the appropriate version of the UMIST (e.g., Saunders et al. 1991 scheme)
                                     = -5 : Saunders etal 1991 (following Helsdon et al. 2001, but use normal charging instead of 'anomalous' zones)
                                     = 0 : Saunders 1991 (modified as in Wojcik 1994)
                                     = 2 : RR scheme ( no extra factor ) (Mansell et al. 2005, JGR)
                                     = 4 : Saunders and Peck Scheme ( no extra factor ) (Mansell et al. 2005, JGR)
                                     = 9 : Saunders and Peck Scheme ( no extra factor, cutoff at -32.47 as orig eq. from sp98 ) (Mansell et al. 2010, JAS)
                                     = 10 : Brooks et al. RARcrit for T > -15 using saund2 (otherwise same as isaund=2) (set rarfac to negative in saund2)
                                     = 11 : Brooks et al. RARcrit for T > -15 using saund6 (otherwise same as isaund=4)
                                     = 12 : Brooks et al. RARcrit for T > -15 using saund6 (otherwise same as isaund=9) (DEFAULT)
                                     = 13 : Brooks et al. RARcrit for T > -15 using saund8 (cosine roll-off function for smoother approach to zero at low temperature)
                                   For Takahashi (1978, 1984) charge separation (see below)
                                     = 1 : Takahashi 1984 with factors for crystal size and impact speed
                                     = 3 : Takahashi 1984 WITHOUT factors for crystal size and impact speed


 nssl_lightrad                       = 12000, radius (m) of discharge cylinder for 1-D discharge scheme

 nssl_disfrac                        = 0.3, nominal fraction of charge removed per discharge (for 1D lightning)

 nssl_ecrit                          = 120000, breakdown electric field (Volts/m) to initiate lightning (i.e., constant value with height). (for nssl_ibrkd=1)

 3D discharge namelist options
 NSSL_EINTERNAL  =   200.0000  : internal field for channel resistance (V/m)
 NSSL_TGRND      =   266.1600  : Temperature threshold for considering CG flash
 NSSL_ZGRND      =  -1.000000  : Altitude threshold for considering CG flash (only if > 0, otherwise uses temperature)

The Takahashi (1978, 1984) charge separation requires a lookup table to be read in. 
The table file is 'takahashi.txt' and needs to be copied from WRF/elec to the working 
directory. To use this option, the NSSL microphysics can read an internal namelist, 
which needs 'nonigrd = -1':

&nssl_mp_params
  nonigrd = -1
/

Also set the value nssl_isaund=1
```

## Output fields
```
2D, time-dependent:

  LIGHT      : Sum of lightning initations in the column (For 1D discharge, this counts all points that 
                 have electric field magnitude that exceeds the initiation threshold
  LIGHTDENS  : Flash extent density (FED; sum of the number of flashes that extend into the grid column)
  LIGHTDIS   : A kind of "source point" density that adds up the number of points in a column where
                 lightning charge is deposited
  rscghis_2d : column sum of charge separation (magnitude) over the history interval

  For 3D lightning:

   flshfedic - FED from IC flashes
   flshfedicp - FED from IC flashes (pos. channels)
   flshfedicn - FED from IC flashes (neg. channels)
   flshfedcg - FED from CG flashes
   flshfedcgp - FED from CG flashes (pos. channels)
   flshfedcgn - FED from CG flashes (neg. channels)
   
3D, time-dependent:

  POT         : Electric potential (Volts)
  ELECMAG     : Electric field magnitude (V/m)
  ELECX       : x-component of E (V/m)
  ELECY       : y-component of E (V/m)
  ELECZ       : z-component of E (V/m)
  INDUC       : Inductive charge separation rate (C m^-3 s^-1)
  NONINDUC    : Net noninductive charge separation rate (C m^-3 s^-1)
  SCW         : Charge density carried by cloud droplets (C/kg, i.e., 'charge mixing ratio')
  SCR         : Charge density carried by rain (C/kg)
  SCI         : Charge density carried by cloud ice (C/kg)
  SCS         : Charge density carried by snow (C/kg)
  SCH         : Charge density carried by graupel (C/kg)
  SCHL        : Charge density carried by hail (C/kg)
  SCTOT       : Net charge density (C m^-3) Is NOT mixing ratio
  SCIONA      : Residual charge from lightning and evaporating hydrometeors that has not been reattached to another species (C/kg)

  For 3D lightning:
    FLSHN, FLSHP : Negative/positive "channel" points
    FLSHI : Flash initiation points
```

## sourceforege

<https://sourceforge.net/projects/wrfelec/files/>

Source: README.wrfelec, updated 2015-05-04

# Compilation process details

- BoxMG directory: /home/wpsze/WRF/WRF4-elec/wrf_install/boxmg4wrf/

{% note danger %}
Important note: \
Compile WRF using **only one core** with BoxMG !!! \
./compile -j 1 em_real 2>&1 | tee compile.log
{% endnote %}

part of script is shown below,

```sh
export WRF_ELEC=1
export BOXMGLIBDIR=/home/wpsze/WRF/WRF4-elec/wrf_install/boxmg4wrf

cd wrf4-elec/

./clean -a
./configure 
##  9 (dmpar) GNU [gfortran/gcc]

sed -i "s/\s\stime\s//g" configure.wrf
./compile -j 1 em_real 2>&1 | tee compile.log
```

```sh
checking for perl5... no
checking for perl... found /usr/bin/perl (perl)
Will use NETCDF in dir: /home/wpsze/WRF/WRFv440/Library/
ADIOS2 not set in environment. Will configure WRF for use without.
Will use HDF5 in dir: /home/wpsze/WRF/WRFv440/Library/
PHDF5 not set in environment. Will configure WRF for use without.
Building WRF with electrification option
!-DWRF_ELEC=1
Will use BoxMG in dir: /home/wpsze/WRF/WRF4-elec/wrf_install/boxmg4wrf/
Will use 'time' to report timing information
set sw_boxmg_path
WRF_ELEC was found in  -DWRF_ELEC=1
$JASPERLIB or $JASPERINC not found in environment, configuring to build without grib2 I/O...
------------------------------------------------------------------------
Please select from among the following Linux x86_64 options:

  1. (dmpar)   PGI (pgf90/gcc)
  2. (dmpar)   PGI (pgf90/pgcc): SGI MPT
  3. (dmpar)   PGI (pgf90/gcc): PGI accelerator
  4. (dmpar)   INTEL (ifort/icc)
               INTEL (ifort/icc): Xeon Phi (MIC architecture)
  5. (dmpar)   INTEL (ifort/icc): Xeon (SNB with AVX mods)
  6. (dmpar)   INTEL (ifort/icc): SGI MPT
  7. (dmpar)   INTEL (ifort/icc): IBM POE
  8. (dmpar)   PATHSCALE (pathf90/pathcc)
  9. (dmpar)   GNU (gfortran/gcc)
 1.  (dmpar)   IBM (xlf90_r/cc_r)
 2.  (dmpar)   PGI (ftn/gcc): Cray XC CLE
 3.  (dmpar)   CRAY CCE (ftn $(NOOMP)/cc): Cray XE and XC
 4.  (dmpar)   INTEL (ftn/icc): Cray XC
 5.  (dmpar)   PGI (pgf90/pgcc)
 6.  (dmpar)   PGI (pgf90/gcc): -f90=pgf90
 7.  (dmpar)   PGI (pgf90/pgcc): -f90=pgf90
 8.  (dmpar)   INTEL (ifort/icc): HSW/BDW
 9.  (dmpar)   INTEL (ifort/icc): KNL MIC
 10. (dmpar)   AMD (flang/clang) :  AMD ZEN1/ ZEN2/ ZEN3 Architectures
 11. (dmpar)   FUJITSU (frtpx/fccpx): FX10/FX100 SPARC64 IXfx/Xlfx

Enter selection [1-20] : 9

......

######################
------------------------------------------------------------------------
Settings listed above are written to configure.wrf.
If you wish to change settings, please edit that file.
If you wish to change the default options, edit the file:
     arch/configure.defaults
NetCDF users note:
 This installation of NetCDF supports large file support.  To DISABLE large file
 support in NetCDF, set the environment variable WRFIO_NCD_NO_LARGE_FILE_SUPPORT
 to 1 and run configure again. Set to any other value to avoid this message.
  

Testing for NetCDF, C and Fortran compiler

This installation of NetCDF is 64-bit
                 C compiler is 64-bit
           Fortran compiler is 64-bit
              It will build in 64-bit
 
NetCDF version: 4.7.4
Enabled NetCDF-4/HDF-5: yes
NetCDF built with PnetCDF: no
```

there is an extra folder called "elec",

```sh
 $ ls wrf4-elec/elec/
binwrf.pl                           module_discharge.F
depend.elec                         module_discharge_msz.F
include_microphysics_driver_elec.F  module_mp_nssl_2mom_elec.F
include_solve_lightningnudge.F      module_nudge_light.F
Makefile                            module_screen.F
module_boxmgsetup.F                 takahashi.txt
module_commasmpi.F
```

# Compile Errors

1. Error NoahMP submodule files not populating WRF directories. 
   1. <https://waipangsze.github.io/2024/06/04/WRF_install_Error_NoahMP_submodule_files/>
   2. Download noahmp @ xxxx folder to your local /WRF/phys/
   3. wrf4-elec is noahmp @ 3be0b28
2. time No such file or directory.
   1. <https://waipangsze.github.io/2024/05/29/WRF_install_time_error/>
   
# Done

```sh
--->                  Executables successfully built                  <---
 
-rwxrwxr-x 1 wpsze wpsze 44236440 Sep 24 14:26 main/ndown.exe
-rwxrwxr-x 1 wpsze wpsze 44121776 Sep 24 14:26 main/real.exe
-rwxrwxr-x 1 wpsze wpsze 43532016 Sep 24 14:26 main/tc.exe
-rwxrwxr-x 1 wpsze wpsze 50585304 Sep 24 14:25 main/wrf.exe

 $ ls run/*exe
run/ndown.exe  run/real.exe  run/tc.exe  run/wrf.ex
```

# WRF-ELEC outputs

It is important to note that the FOD and reflectivity are derived from ELEC, whereas the LPI originates from WRF-ARW simulations.

## simulated radar reflectivity

radar reflectivity is diagnosed. 

- <https://wrf-python.readthedocs.io/en/latest/user_api/generated/wrf.dbz.html>
- This function computes equivalent reflectivity factor [dBZ] at each model grid point assuming spherical particles of constant density, with exponential size distributions. This function is based on “dbzcalc.f” in RIP.

or on namelist,

```sh
&physics
...
do_radar_ref = 0, 	; 1 = allows radar reflectivity to be computed using mp-scheme-specific parameters.  Currently works for mp_physics = 2,4,6,7,8,10,14,16
```

with do_radar_reflectivity=1 and after running got the REFL_10cm variable. But this variable REFL_10cm is giving the estimated 10 cm wavelength (S-band) radar reflectivity in dB relative to reflectivity (dBZ) as produced by the WRF microphysics scheme. 

> but, above mp_physics = 17 or 18? 

## lightning_option (from WRF-ARW)

Lightning parameterization option to allow flash rate prediction without chemistry.

[WRF-Chem Version 4.4 User's Guide: Appendix C: Using the Lightning-NOx Parameterization in WRF-Chem](https://ruc.noaa.gov/wrf/wrf-chem/Users_guide.pdf)

{% note primary %}
(for convection resolved runs; must also use 
do_radar_ref = 1, and mp_physics = 2,4,6,7,8,10,14, or 16)
{% endnote %}

```
lightning_option (max_dom)                       Lightning parameterization option to allow flash rate prediction without chemistry
                                    = 0        ! off
                                    = 1        ! PR92 based on maximum w, redistributes flashes within dBZ > 20 (for convection resolved runs; must also use 
                     do_radar_ref = 1, and mp_physics = 2,4,6,7,8,10,14, or 16)
                                    = 2        ! PR92 based on 20 dBZ top, redistributes flashes within dBZ > 20 (for convection resolved runs; must also use 
                     do_radar_ref = 1, and mp_physics = 2,4,6,7,8,10,14, or 16)
                                    = 3        ! Predicting the potential for lightning activity (based on Yair et al, 2010, J. Geophys. Res., 115, D04205, doi:10.1029/2008JD010868) 
                                    = 11       ! PR92 based on level of neutral buoyancy from convective parameterization (for scales
                                                where a CPS is used, intended for use at 10 < dx < 50 km; must also use cu_physics = 5 or 93)
lightning_dt  (max_dom)             = 0.       ! time interval (seconds) for calling lightning parameterization. Default uses model time step
lightning_start_seconds (max_dom)   = 0.       ! Start time for calling lightning parameterization. Recommends at least 10 minutes for spin-up.
flashrate_factor  (max_dom)         = 1.0      ! Factor to adjust the predicted number of flashes. Recommends 1.0 for lightning_option = 11
                                                between dx=10 and 50 km. Manual tuning recommended for all other options independently
                                                for each nest.
cellcount_method (max_dom)                       Method for counting storm cells. Used by CRM options (lightning_options=1,2).
                                    = 0,       ! model determines method used
                                    = 1,       ! tile-wide, appropriate for large domains
                                    = 2,       ! domain-wide, appropriate for sing-storm domains
cldtop_adjustment (max_dom)         = 0.       ! Adjustment from LNB in km. Used by lightning_option=11. Default is 0, but recommends 2 km
iccg_method (max_dom)                            IC:CG partitioning method (IC: intra-cloud; CG: cloud-to-ground)
                                    = 0        ! Default method depending on lightning option, currently all options use iccg_method=2 by default
                                    = 1        ! Constant everywhere, set with namelist options iccg_prescribed (num|den)#, default is 0./1. (all CG).
                                    = 2        ! Coarsely prescribed 1995-1999 NLDN/OTD climatology based on Boccippio et al. (2001)
                                    = 3        ! Parameterization by Price and Rind (1993) based on cold-cloud depth
                                    = 4        ! Gridded input via arrays iccg_in_(num|den) from wrfinput for monthly mapped ratios. 
                                                Points with 0/0 values use ratio defined by iccg_prescribed_(num|den)
iccg_prescribed_num (max_dom)       = 0.       ! Numerator of user-specified prescribed IC:CG
iccg_prescribed_den (max_dom)       = 1.       ! Denominator of user-specified prescribed IC:CG
ltng_temp_upper                     = -45.     ! Temperature (C) of upper peak of LNOx vertical distribution for IC lightning (used by &chem lnox_opt=2).
ltng_temp_lower                     = -15.     ! Temperatures (C) of lower peak of LNOx vertical distribution for both IC and CG lightning (used by &chem lnox_opt=2).
```

From WRF-MPAS-A forum, 

- [WRF lightning scheme only being run at model output times?](https://forum.mmm.ucar.edu/threads/wrf-lightning-scheme-only-being-run-at-model-output-times.16207/#post-40290)
- [Lightning option](https://forum.mmm.ucar.edu/threads/lightning-option.13017/#post-31892)
- [Large differences of simulated fields (U, V, W, T) with lightning_option = 3 (LPI) on and off](https://forum.mmm.ucar.edu/threads/large-differences-of-simulated-fields-u-v-w-t-with-lightning_option-3-lpi-on-and-off.9338/#post-22147)
  - An alternative is to calculate the LPI from the model output. This can be done quite simply using an NCL program.

From <https://groups.google.com/a/ucar.edu/g/wrf-chem-lightning/c/v6nIeyL7G2g>,

{% note primary %}
The lightning option = 1 you chose is appropriate for dx<5km because it relies on the maximum updraft speed in the storm updraft. You chose dx=10km which will not produce updraft speeds representative of thunderstorms, and in fact the storms are likely represented by the convective parameterization.

For dx=10 km, you should use lightning_option = 11. 
{% endnote %}

## light/lightdens/Electric POTential difference (POT) (From WRF-ELEC)

Starting with the electrification processes (inductive and non-inductive
processes), calculation method of the electric field, along with the
BoxMG mathematical software for computation of POT is described.

![WRF-ELEC](https://i.imgur.com/kgs4meR.png)


# What is the time interval in light(flash column-1)?

<https://github.com/MicroTed/wrf4-elec/blob/7c90e71bdc4db527d0ec034804e4fd200cdacf28/phys/module_microphysics_driver.F#L117>

!--light         2D flash origin densities per output time
!--lightdens     2D flash extent densities per output time
!--lightdis      2D total number of discharge points per output time 

- per output time? 
  - To find the number of flashes between output times, simply subtract the flashcount array from the previous output time from the current output time to get number of flashes per dt (where64dt = time between output files).

From [solve_em.F](https://github.com/MicroTed/wrf4-elec/blob/7c90e71bdc4db527d0ec034804e4fd200cdacf28/dyn_em/solve_em.F#L157), **diag_flag and diag_flag_hist** are

- Set diagnostic flag value **history output time**

```fortran
!-----------------------------------------------------------------------------
!
! Set restart flag value history output time
!-----------------------------------------------------------------------------
   restart_flag = .false.
   if ( Is_alarm_tstep(grid%domain_clock, grid%alarms(restart_alarm)) ) then
      restart_flag = .true.
   endif
!
! Set diagnostic flag value history output time
!-----------------------------------------------------------------------------

   ke_diag = kms ! default to ke_diag=1 in case of nwp_diagnostics == 1
   diag_flag = .false.
   if ( Is_alarm_tstep(grid%domain_clock, grid%alarms(HISTORY_ALARM)) ) then
      diag_flag = .true.
      ke_diag = min(k_end,kde-1) ! set depth to full domain for reflectivity field
   endif
   diag_flag_hist = diag_flag
```

- In [include_microphysics_driver_elec.F](https://github.com/MicroTed/wrf4-elec/blob/7c90e71bdc4db527d0ec034804e4fd200cdacf28/elec/include_microphysics_driver_elec.F#L24), 
  - diag_flag_hist
  - clear_history = .true.
  - all set = 0.0

```fortran     
! this sets the clear flag for the next time step if a history dump is occurring now
IF ( diag_flag_hist ) THEN
   clear_history = .true.
ENDIF

!    add up flashes up to history dump 

!     if (MOD(NINT(curr_secs),history_interval*60).eq.0) then
IF ( clear_history ) THEN
   clear_history = .false.
!      write(0,*) 'clear arrays'
light(:,:) = 0.
lightdis(:,:) = 0.
lightdens(:,:) = 0.
induc(:,:,:) = 0.0
noninduc(:,:,:) = 0.0
   IF ( idischarge == 1 ) THEN
   lightfod(:,:) = 0.
   ELSEIF ( idischarge >= 2 ) THEN
   flshfedic(:,:) = 0.
   flshfedicp(:,:) = 0.
   flshfedicn(:,:) = 0.
   flshfedcg(:,:) = 0.
   flshfedcgp(:,:) = 0.
   flshfedcgn(:,:) = 0.
   flshi(:,:,:) = 0.
   flshn(:,:,:) = 0.
   flshp(:,:,:) = 0.
   ENDIF
```

# WRF practice

| option  |  |
|:------------- | :-------------|
| elec_physics             |  = 0, electrification (and charge arrays) turned off (DEFAULT) <br> = 1, electrification turned on (only works with mp_physics = 17, 18, or 22 i.e., 2-moment NSSL schemes)        |
| nssl_ipelec (max_dom)    |  ! NOTE: only set this to a nonzero value on the innermost domain <br> = 0, charging turned off <br> = 2, non-inductive charging only <br> = 3, non-inductive + inductive charging <br> = 1, not used (reserved for future use)        |
| nssl_idischarge          |  <br> = 0, no discharge <br> = 1, Cylindrical "1D" lightning scheme based on Ziegler and MacGorman (1994, JAS) <br> = 2, 3D discrete discharge adapted from MacGorman et al. (2001)            |

- The WRF-ELEC model outputs a variety of variables related to electrification processes and lightning activity in thunderstorms. Some of the key output variables include:

| WRF-ELEC variables                                 | Unit                 | Description                               |
|----------------------------------------------------|----------------------|-------------------------------------------|
| LIGHT(Time, south_north, west_east)                | flash origin density | lightning flash initiations               |
| LIGHTDENS(Time, south_north, west_east)            | flash column-1       | lightning flash density                   |
| LIGHTFOD(Time, south_north, west_east)             | flash column-1       | normalized lightning flash origin density |
| LIGHTDIS(Time, south_north, west_east)             | Source column-1      | lightning source density                  |
| POT(Time, bottom_top, south_north, west_east)      | V                    | POTENTIAL                                 |
| ELECMAG(Time, bottom_top, south_north, west_east)  | V m-1                | EFIELD MAGNITUDE                          |
| ELECX(Time, bottom_top, south_north, west_east)    | V m-1                | EFIELD X-Component                        |
| ELECY(Time, bottom_top, south_north, west_east)    | V m-1                | EFIELD Y-Component                        |
| ELECZ(Time, bottom_top, south_north, west_east)    | V m-1                | EFIELD Z-Component                        |
| SCTOT(Time, bottom_top, south_north, west_east)    | C m-3                | Total Space Charge Density                |
| RSCGHIS_2D(Time, south_north, west_east)           | C m-2                | MAX NONINDUCTIVE CHARGING 2D              |
| INDUC(Time, bottom_top, south_north, west_east)    | C m-3                | TOTAL INDUCTIVE CHARGING                  |
| NONINDUC(Time, bottom_top, south_north, west_east) | C m-3                | TOTAL NONINDUCTIVE CHARGING               |
| SCTOT(Time, bottom_top, south_north, west_east)    | C m-3                | Total Space Charge Density                |

- If the lightning discharge flag (nssl_idischarge = 2) that is lightning3d, More output variables include: (LIGHTFOD is not calculated)

| WRF-ELEC variables                              | Unit           | Description                           |
|-------------------------------------------------|----------------|---------------------------------------|
| FLSHFEDIC(Time, south_north, west_east)         | flash column-1 | IC lightning flash extent density     |
| FLSHFEDICP(Time, south_north, west_east)        | flash column-1 | IC pos lightning flash extent density |
| FLSHFEDICN(Time, south_north, west_east)        | flash column-1 | IC neg lightning flash extent density |
| FLSHFEDCG(Time, south_north, west_east)         | flash column-1 | CG lightning flash extent density     |
| FLSHFEDCGN(Time, south_north, west_east)        | flash column-1 | CG neg lightning flash extent density |
| FLSHFEDCGP(Time, south_north, west_east)        | flash column-1 | CG pos lightning flash extent density |
| FLSHI(Time, bottom_top, south_north, west_east) | count          | Lightning init points                 |
| FLSHN(Time, bottom_top, south_north, west_east) | count          | Negative channels                     |
| FLSHP(Time, bottom_top, south_north, west_east) | count          | Positive channels                     |

# Cost: Elapsed time

| WRF-ELEC | mp | idischarge | Elapsed time |
|:----------|:----|:------------|:--------------|
| elec=0   | 17 | none       | 4h49m        |
| elec=1   | 17 | 1          | 6h13m        |
| elec=1   | 17 | 2          | 9h1m         |

WRFv4.5-ELEC. 

# Literature review

Lightning activity is related to two important factors: dynamic–thermodynamic and microphysical characteristics (e.g., Williams et al., 2005; Rosenfeld et al., 2008; Guo et al., 2016; Wang et al., 2018; Zhao et al., 2020). Since the dynamic–thermodynamic processes affect the development of thunderstorm significantly, lightning activity is influenced by various dynamic–thermodynamic variables: temperature (Price, 1993), relative humidity in the lower and middle troposphere (Xiong et al., 2006; Fan et al., 2007), convective available potential energy (Qie et al., 2004; Stolz et al., 2015), and many others.

## (1992) PR92

> Price, C., and D. Rind (1992), A Simple Lightning Parameterization for Calculating Global Lightning Distributions, J. Geophys. Res., 97(D9), 9919-9933, doi:10.1029/92JD00719. \
> Wong, J., M. Barth, and D. Noone, 2013: Evaluating a lightning parameterization based on cloud-top height for mesoscale numerical model simulations, Geosci. Model Dev., 6, 429–443, GMD - Evaluating a lightning parameterization based on cloud-top height for mesoscale numerical model simulations.

ref: [WRF-Chem Version 4.4 User's Guide: Appendix C: Using the Lightning-NOx Parameterization in WRF-Chem](https://ruc.noaa.gov/wrf/wrf-chem/Users_guide.pdf)

You may also find useful information in the actual code for the schemes:

- module_lightning_driver.F
- module_ltng_crmpr92.F

## (2010) Prediction of lightning flash density with the WRF model

> <https://pdfs.semanticscholar.org/a206/1d033ccf90082c05a1514df4920005035c0b.pdf> \
> Lynn, B., and Yoav Yair. "Prediction of lightning flash density with the WRF model." Advances in Geosciences 23 (2010): 11-16.

The **Lightning Potential Index (LPI)** is a measure of the potential for charge generation and separation that leads to lightning flashes in convective thunderstorms. It is calculated from model simulated updraft and microphysical fields. It was designed to predict the potential of lightning occurrence in operational weather forecasting models, but could possibly be used to improve short-range forecasts of heavy rain. **The index is modified here to be model grid-scale transparent between 1 and 4 km (the approximate upper limit of explicit microphysical weather forecasts)**. Two case studies show that the modification appears to work quite well, and that LPI can be calculated on both an extremely high resolution research-grid (i.e., 1.33 km) and high resolution (i.e., 4 km) operationally compatible forecast grid. Analytical expressions are presented to use the LPI to predict the hourly lightning flash density.

## (2013) Fierro et al.

> Fierro, A. O., Mansell, E. R., MacGorman, D. R., & Ziegler, C. L. (2013). The implementation of an explicit charging and discharge lightning scheme within the WRF-ARW model: Benchmark simulations of a continental squall line, a tropical cyclone, and a winter storm. Monthly Weather Review, 141(7), 2390-2415.

This work describes the recent implementation of explicit lightning physics within the Weather Research and Forecasting (WRF) Model. 

- Charging of hydrometeors consists of five distinct noninductive parameterizations, polarization of cloud water, and the exchange of charge during collisional mass transfer. 
- The three components of the ambient electric field are explicitly solved for via the computationally efficient multigrid elliptic solver. 
- The discharge process employs concepts adapted from two well-documented bulk lightning models, whereby charge reduction is imposed within a prescribed volume centered at grid points characterized by electric field magnitudes exceeding a given breakdown threshold. 

This lightning model was evaluated through benchmark 

- convection-allowing (3 km) model simulations of three contrasting convective systems: a continental squall line, 
- a major hurricane (Rita 2005), and 
- a winter storm. 
  
The areal coverage and magnitude of the simulated hourly flash origin density (FOD) for the continental squall line are qualitatively comparable to that of the total lightning data observations from Earth Networks Total Lightning Network (ENTLN). In agreement with the ENTLN observations, no FOD are simulated for the winter storm case. The simulated spatial FOD pattern of the hurricane and the eyewall gross charge structure were both in reasonable agreement with observations. The simulated FOD for all three cases were also evaluated against those obtained with the recently developed McCaul diagnostic lightning prediction schemes and exhibited overall good qualitative agreement with each other for Rita and the continental squall line.

## Thunderstorm Indices (LPI, Layrh, SCP)

**Thunderstorm Indices (LPI, Layrh, SCP)** obtained from IMD-WRF (3km) Hourly outputs.

- Model GEFS - WRF Description
- <https://srf.tropmet.res.in/srf/ts_prediction_system/lpi_model.php>
- **Lightning Potential Index (LPI)**
  - Here, LPI has been calculated using IMD-WRF (3km) hourly model output. 24 Hour accumulated LPI has been generated by adding hourly LPI values. The threat of LPI has been categorized in Low, Moderate and High. LOW: LPI < 0.001 and >0.0005; Moderate: LPI :< 0.01 and > 0.001; Threat Level: High: LPI > 0.01.
- **SuperCell Composite Parameter (SCP)**
  - SCP > 1 indicated that the environment is conducive for thunderstorm formation. The Threat Level of SCP has been categorized in Low, Moderate and High. LOW: 3 < SCP < 5; Moderate: 5 < SCP < 7; High: SCP > 7.
- **Layer Relative Humidity (LAYRH)**
  - The threat level of Layrh has been categorized in Low, Moderate and High. LOW: 20 < LAYRH < 40; Moderate: 40 < LAYRH < 60; High : LAYRH > 60

## (2019) Numerical study of performance of two lightning prediction methods based on: Lightning Potential Index (LPI) and electric POTential difference (POT) over Tehran area

> **Gharaylou, M., Farahani, M. M., Hosseini, M., & Mahmoudian, A. (2019). Numerical study of performance of two lightning prediction methods based on: Lightning Potential Index (LPI) and electric POTential difference (POT) over Tehran area**. Journal of Atmospheric and Solar-Terrestrial Physics, 193, 105067.

The electric POTential difference (POT) and the Lightning Potential Index (LPI) performance in predicting the lightning activity is investigated and the probable relationship between them is examined. These two indices have a similar dependency on microphysical variables such as ice, graupel mixing ratios, and also updraft characteristics within the cloud. Regardless of this similarity, the LPI directly calculated from WRF model is a more favorable parameter for predicting the lightning events in comparison with the POT, which requires an extra package (ELEC) model. Ten years' available data over the Tehran area were reviewed and four thundercloud cases with distinct characteristics (CAPE, time-frequency, intensity) were selected. In order to acquire the associated physical properties, four simulations have been done using the WRF-ELEC model, which is initialized with ERA-Interim data.

## **(2020) Prediction of lightning activity using WRF-ELEC model

> **Gharaylou, M., Farahani, M. M., Mahmoudian, A., & Hosseini, M. (2020). Prediction of lightning activity using WRF-ELEC model: Impact of initial and boundary conditions**. Journal of Atmospheric and Solar-Terrestrial Physics, 210, 105438.

> - <https://sci-hub.se/https://doi.org/10.1016/j.jastp.2020.105438>

- The WRF model belongs to the first group in which lightning is predicted based on the **Bulk Lightning Model (BLM)**. Ziegler and MacGorman (1994) have studied lightning flashes in a twoand three-dimensional cloud models using the WRF model.
- In the early 90s, Price and Rind (1992) developed a lightning parameterization scheme based on **cloud-top height**. 
- Furthermore, another lightning flash parameterization scheme has been introduced by Allen and Pickering (2002) and Allan et al. (2010) based on the **square of deep convective mass flux**. 
- Choi et al. (2005) and Zhao et al. (2009) incorporated the **convective available potential energy (CAPE)** in their comparative parameterizations.
- Yair et al. (2010) introduced **Lightning Potential Index (LPI)**, an advanced index for evaluating the probability of lightning occurrence, incorporated the dynamic and microphysic concepts.
- Following, Lynn et al. (2012) introduced the **potential electrical energy (Ep) parameter** into the WRF model, which was used to predict both **Cloud-to-Ground (CG)** and **Intra-Cloud (IC)** *lightning events*. 
- Afterwards, Wong et al. (2013) developed the lightning parameterization based on cloud-top height (Price and Rind, 1992).
- Gharaylou et al. (2019) examined 2 indices which are used in predicting the lightning activities both in time and location. They compared the **electric potential difference (POT)** and the **LPI** performance in predicting the lightning associated with four events over a domain located in the north of Iran.
- **The LPI is computationally less expensive and is directly calculated by the WRF model with less computational cost in comparison with the POT which requires an extra package (ELEC model).**
  - LPI calculations have been discussed in our previous paper (Gharaylou et al., 2019)

### Effect of Initial and boundary conditions (IBCs)

- The mesoscale models ordinarily obtain their initial and boundary conditions (IBCs) from large scale global models. **Applying different initial conditions highly influences the accuracy of the model prediction in local areas** (Zhang et al., 2002, 2006).
- A few works have been done to investigate the effect of initial conditions on lightning activity (Lynn et al., 2012). 
- Zepka et al. (2014) studied the impact of the resolution of the NCEP operational Global Forecast System.
- As mentioned, ***there are very few works*** related to the assessment of IBC effects in the WRF-ELEC model for lightning prediction.
  - WRF-ELEC model initialized with different datasets needs to be verified.

# Case: Hong Kong Lightning

The "Hong Kong Observatory Lightning Statistics" refers to the detailed records of lightning activity in the Hong Kong region, primarily categorized into cloud-to-ground lightning and intra-cloud lightning. This data not only aids in understanding meteorological patterns but also enhances awareness of the safety risks associated with lightning strikes.

- [香港天文台閃電位置資訊服務 HKO Lightning Location Information Service](https://www.hko.gov.hk/tc/wservice/tsheet/llis.htm)

![閃電定位網絡各個探測站的位置分佈圖/Distribution of lightning sensor stations of the lightning location network](https://www.hko.gov.hk/tc/wservice/tsheet/images/fact2c.jpg)

- [Lightning density map for Hong Kong](https://www.hko.gov.hk/en/wxinfo/llis/llis_map.htm)

![Average yearly number of cloud-to-ground lightning strokes per square kilometer](https://www.hko.gov.hk/en/wxinfo/llis/images/ll_density_e.png)

- [閃電（試驗版） | 香港及珠江三角洲區域自動分區天氣預報/Automatic regional weather forecast in Hong Kong and the Pearl River Delta region](https://maps.weather.gov.hk/ocf/index_uc.html?data=ncln)

## Statistics of lightning days at the HKO Weather Station

![Statistics of lightning days at the HKO Weather Station (Last updated: August 31, 2024). From [i-lens.hk](https://i-lens.hk/hkweather/special_stat.php?event=LIGHTING_DAY&station=HKO)](../../../../figures/2024-09-20-wrf4-elec-lightning/Statistics_of_lightning_days_at_the_HKO_Weather_Station.png)


## All Weather Stations - Ranking of Highest Daily Total Lightning Counts in History

From [i-lens.hk](https://i-lens.hk/hkweather/rank.php?station=ALL&element=L_BOTH&type=MAX&year=&month=), Last updated: August 31, 2024

| Ranking |    Weather Station    | Start observation year | Observation date | Total number of lightning strikes per day (times) |
|:-------:|:---------------------:|:----------------------:|:----------------:|:-------------------------------------------------:|
|    1    | Hong Kong Observatory |          2005          |    2020-06-06    |                       47537                       |
|    2    | Hong Kong Observatory |          2005          |    2020-06-07    |                       41446                       |
|    3    | Hong Kong Observatory |          2005          |    2019-04-20    |                       36299                       |
|    4    | Hong Kong Observatory |          2005          |    2023-06-01    |                       36253                       |
|    5    | Hong Kong Observatory |          2005          |    2024-04-30    |                       32603                       |
|    6    | Hong Kong Observatory |          2005          |    2021-06-28    |                       31859                       |
|    7    | Hong Kong Observatory |          2005          |    2019-08-25    |                       24951                       |
|    8    | Hong Kong Observatory |          2005          |    2019-04-19    |                       22053                       |
|    9    | Hong Kong Observatory |          2005          |    2018-08-22    |                       21152                       |
|    10   | Hong Kong Observatory |          2005          |    2022-07-30    |                       20187                       |
|    11   | Hong Kong Observatory |          2005          |    2019-06-13    |                       19526                       |
|    12   | Hong Kong Observatory |          2005          |    2010-09-09    |                       18813                       |
|    13   | Hong Kong Observatory |          2005          |    2013-05-22    |                       18568                       |
|    14   | Hong Kong Observatory |          2005          |    2020-09-15    |                       17879                       |
|    15   | Hong Kong Observatory |          2005          |    2020-05-30    |                       17765                       |
|    16   | Hong Kong Observatory |          2005          |    2006-06-09    |                       17587                       |
|    17   | Hong Kong Observatory |          2005          |    2014-03-30    |                       16823                       |
|    18   | Hong Kong Observatory |          2005          |    2024-04-26    |                       16328                       |
|    19   | Hong Kong Observatory |          2005          |    2010-09-08    |                       14659                       |
|    20   | Hong Kong Observatory |          2005          |    2023-04-21    |                       14471                       |

## Overview of Climate Change Effects

Climate change, driven by the increase in greenhouse gases, has a profound impact on weather patterns. As global temperatures rise, more energy is available in the atmosphere, which intensifies convective storms. Since lightning is primarily a product of such storms, the increase in the frequency and intensity of these storms can lead to changes in lightning activity.

- **Increase Per Degree of Warming:** Across the continental United States (CONUS), the CAPE × P proxy predicts a 12 ± 5% rise in lightning strike rates for each 1°C increase in global mean temperature (Romps et al., 2014). This research suggests that as the planet warms, lightning frequency will likely increase. A 12% rise in lightning activity is anticipated for every degree of warming, which could result in a 50% increase in lightning strikes in regions like the U.S. by the end of the century (Romps et al., 2014; Romps, 2021).
- **Specific Regional Studies:** Focused on the South China Sea (SCS) and surrounding regions in Southeast Asia to analyze long-term trends and future projections of lightning activity using the longest available satellite-based lightning dataset and climate models. The projections indicate that lightning activity in the SCS region is expected to increase by 10% under SSP245 and by 12% under SSP370 by the end of the 21st century. (Xu et al., 2023; Xu et al., 2024)
- **Impact of Global Warming on Future Lightning Events**
  - The IPCC AR6 defines several Shared Socioeconomic Pathways (SSPs) that describe possible future climate scenarios based on different levels of greenhouse gas emissions. (e.g., SSP5-8.5: Very high emissions, with warming over 4.4°C)
- **Regional Studies on Future Lightning Events (e.g., South China Sea)**

From these papers,

- Romps, D. M., Seeley, J. T., Vollaro, D., & Molinari, J. (2014). Projected increase in lightning strikes in the United States due to global warming. Science, 346(6211), 851-854.
- Romps, D. M. (2019). Evaluating the future of lightning in cloud‐resolving models. Geophysical Research Letters, 46(24), 14863-14871.
- Xu, L., Cao, X., Lan, X., Zhang, W., Sun, C., & Zhang, Y. (2024). Future increase in lightning around the South China Sea under climate change. Earth and Space Science, 11(6), e2023EA003356.
- Xu, Mingyi, et al. "Distribution of lightning spatial modes and climatic causes in China." Atmospheric and Oceanic Science Letters 16.2 (2023): 100338.


# References
1. 香港天文台
   1. [什麼是雷暴？](https://www.hko.gov.hk/tc/education/weather/thunderstorm-and-lightning/00031-what-are-thunderstorms.html)
   2. [2006-Reprint 622-探討閃電位置數據應用在臨近預報的前景](https://www.hko.gov.hk/tc/publica/reprint/files/r622.pdf)
   3. [2009-Reprint 817-香港一次夏季雹暴的天氣過程分析及臨近預報系統的應用](https://www.weather.gov.hk/tc/publica/reprint/files/r817a.pdf)
   4. [2012-Reprint 1035-利用閃電數據監測暴雨發展的個案分析](https://www.weather.gov.hk/en/publica/reprint/files/r1035.pdf)
   5. [2018-Reprint 1337-粵港澳閃電定位網絡的最新發展及與超強颱風天鴿相關之數據分析](https://www.hko.gov.hk/en/publica/reprint/files/r1337.pdf)
   6. [2018-Reprint 1341-公眾定點閃電臨近預報服務](https://www.hko.gov.hk/tc/publica/reprint/files/r1341.pdf)
   7. [2019-Reprint 1358-區域與全球閃電定位資料於珠三角地區的精細化比對](https://www.hko.gov.hk/en/publica/reprint/files/r1358.pdf)
   8. [2020-在珠三角一帶與大雨相關的閃電特徵分析](https://www.hko.gov.hk/tc/publica/ghm_21/files/HPaper_WongCP.pdf)

2. 徐良韬, 张义军, 王飞, 郑栋. 2012: 雷暴起电和放电物理过程在WRF模式中的耦合及初步检验. 大气科学, 36(5): 1041-1052. DOI: 10.3878/j.issn.1006-9895.2012.11235
   1. WRFv3.2.1
   2. 本文将雷暴云的起电、放电物理过程引入中尺度的WRF (Weather Research and Forecasting) 模式,并对超级单体和飑线过程进行了模拟研究.起电过程在Milbrandt双参数微物理方案中写入,包含霰、雹与冰晶、雪之间的非感应起电机制,以及霰、雹与云滴之间的感应起电机制.
3. 徐良韬, 陈双, 姚雯, 等. 利用起放电模式开展闪电活动的直接预报试验. 应用气象学报, 2018, 29(5): 534-545. DOI: 10.11898/1001-7313.20180503.
   1. WRFv3.4.1
   2. 利用耦合有起电和放电物理过程的中尺度起电放电模式WRF-Electric
4. Wasson, G., & Panda, S. K. (2024). Sensitivity of PBL parameterization schemes in simulating lightning and thunderstorm using WRF-ELEC model. Climate Dynamics, 1-23.
   1. The WRF-ELEC model, an auxiliary package integrated into  WRF, is specifically designed to compute electric potential and overall lightning flash rate (source: <https://sourceforge.net/projects/wrfelec>).
   2. It incorporates explicit cloudresolving scale physics for lightning prediction ([Fierro et al.2013](https://scholar.google.com/scholar_url?url=https://journals.ametsoc.org/view/journals/mwre/141/7/mwr-d-12-00278.1.xml%3Ftab_body%3Dpdf&hl=en&sa=T&oi=gsr-r-ggp&ct=res&cd=0&d=6841908713848986342&ei=DTHtZqfdAfSC6rQP2qO70AU&scisig=AFWwaeaoJDdXALkuhsn44g0xEyU5)). Detailed formulations can be found in [Mansell et al.(2005)](https://scholar.google.com/scholar_url?url=https://agupubs.onlinelibrary.wiley.com/doi/abs/10.1029/2004jd005287&hl=en&sa=T&oi=gsr-r&ct=res&cd=0&d=13100036974408530565&ei=JTHtZs_QIeqs6rQPw4y2sAM&scisig=AFWwaeZ27Z0oFR35ZZvLDk-yrgo_) and [Fierro et al. (2013)](https://scholar.google.com/scholar_url?url=https://journals.ametsoc.org/view/journals/mwre/141/7/mwr-d-12-00278.1.xml%3Ftab_body%3Dpdf&hl=en&sa=T&oi=gsr-r-ggp&ct=res&cd=0&d=6841908713848986342&ei=OTHtZtytGPay6rQPxrKeiAM&scisig=AFWwaeaoJDdXALkuhsn44g0xEyU5).
   3. skill scores were calculated for each experiment.
      1. standard statistics and skill score analysis of CAPE (J/kg) and accumulated total precipitation (mm),
5. Saleh, N., Gharaylou, M., Farahani,M. M., & Alizadeh, O. (2023).Performance of lightning potentialindex, lightning threat index, and theproduct of CAPE and precipitationin the WRF model. Earth and SpaceScience, 10, e2023EA003104. <https://doi.org/10.1029/2023EA003104>
6. [2011: Lightning Forecast Algorithm (LFA) Overview](https://weather.ndc.nasa.gov/sport/training/lfa/LFAtraining_20111025.pdf)