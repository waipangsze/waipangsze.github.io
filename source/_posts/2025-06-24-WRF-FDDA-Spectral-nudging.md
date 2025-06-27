---
layout: post
title: MPAS | WRF | FDDA | Spectral nudging
categories: [MPAS]
tags: [WRF, MPAS, FDDA, NWP, Spectral nudging]
author: wpsze
date: 2025-06-24 10:45:00
math: true
mathjax: true
mathjax_autoNumber: true
mermaid: true
index_img: https://i.imgur.com/6xCRnQa.png
banner_img: https://i.imgur.com/6xCRnQa.png
---

# Nudging

**Nudging, also known as [four-dimensional data assimilation (FDDA)](https://github.com/wrf-model/TechNote/blob/master/fdda.tex)**, is a method of keeping simulations close to analyses and/or observations or forcing data over the course of an integration by applying extra forcing terms to the model equations.

There are three types of nudging and some can be used in combination. 

- **Grid- or analysis-nudging** simply forces the model simulation towards a series of analyses grid-point by grid-point. 
- **Observational- or station-nudging** locally forces the simulation towards observational data. 
- **Spectral nudging** only forces the model to the selected spectra of waves in the analysis.

These methods provide a four-dimensional analysis that is somewhat balanced dynamically, and in terms of continuity,while allowing for complex local topographical or convective variations. Such datasets can cover long periods, and have particular value in driving off-line air quality or atmospheric chemistry models.

# Spectral nudging

Spectral nudging is another way to nudge the model solution to either analysis or any forcing data. The key difference between this method and that of grid- or obs-nudging is that it is more selective in the scales one would like to nudge. In contrast to directly nudge model variables toward analysis or observation, **this method decomposes the difference fields spectrally**, and **nudges toward the longer waves that correspond to the analysis**. The spectral coefficients from the **selected waves** are then transformed back from the wave space to physical space and added to the model as tendencies. This technique was originally implemented to the Reginoal Atmospheric Modeling System, and later to ARW in 2010.

Spectral nudging uses the same input data as for grid-nudging, and nudges model variables u, v, temperature, and geopotential height. Water mixing ratio nudging was added in Version 4.0. Applications described above for grid-nudging can be considered using spectral nudging too.

Options to control the grid-nudging are also used for spectral nudging (such as nudging end time and ramping, nudging strength, controls of nudging in the boundary layer or lower levels and so on). In addition, there is an option to control the ramping in the vertical between the layer without nudging to the layer where the nudging is on. **The number of waves to nudge in the x and y directions are user-defined**.

# module_fdda_spnudging.F

- <https://github.com/wrf-model/WRF/blob/master/phys/module_fdda_spnudging.F>

```fortran
!
! Compute 3-D nudging tendencies for u, v
!
!
!GMM Fist calculate differences between model variables and analysis values,
!then filter in the x and y direction all wave numbers higher than
! xwavenum and ywavenum, as specified in the namelist.
!If either xwavenum or ywavenum are not specified,
! default values are zero, and spectral nudging is turned off
!Then use the filtered differences to calculate the spectral nudging tendencies
```

## spectral_nudging_filter_3dx

{% note primary %}
! Variables will stay in domain form since this routine is meaningless\
! unless tile extent is the same as domain extent in E/W direction, i.e.,\
! the processor has access to all grid points in E/W direction.\
! There may be other ways of doing FFTs, but we haven't learned them yet...\
{% endnote %}

```fortran
SUBROUTINE spectral_nudging_filter_3dx( f, nwave,            &
                            ids, ide, jds, jde, kds, kde,    &
                            ims, ime, jms, jme, kms, kme,    &
                            its, ite, jts, jte, kts, kte )

  IMPLICIT NONE

  INTEGER ,       INTENT(IN   ) :: nwave
  INTEGER ,       INTENT(IN   ) :: ids, ide, jds, jde, kds, kde, &
                                   ims, ime, jms, jme, kms, kme, &
                                   its, ite, jts, jte, kts, kte

  REAL , DIMENSION( ims:ime , kms:kme, jms:jme ) , INTENT(INOUT) ::  f

  REAL , DIMENSION(1:ide-ids+1,1:kte-kts+1) :: sheet
  INTEGER ::  i, j, j_end, k, nx, ny

  ! Variables will stay in domain form since this routine is meaningless
  ! unless tile extent is the same as domain extent in E/W direction, i.e.,
  ! the processor has access to all grid points in E/W direction.
  ! There may be other ways of doing FFTs, but we haven't learned them yet...

  ! Check to make sure we have full access to all E/W points
  IF ((its /= ids) .OR. (ite /= ide)) THEN
     WRITE ( wrf_err_message , * ) 'module_spectral_nudging: 3d: (its /= ids) or (ite /= ide)',its,ids,ite,ide
     CALL wrf_error_fatal ( TRIM( wrf_err_message ) )
  END IF


  nx = ide-ids+1 
  ny = kte-kts+1 ! we can filter extra level for variables that are non-Z-staggered
  j_end = MIN(jte, jde-1)
  IF (j_end == jde-1) j_end = jde
  DO j = jts, j_end

        DO k=kts,kte
        DO i=ids,ide-1
           sheet(i-ids+1,k-kts+1) = f(i,k,j)
        END DO
           sheet(ide,k-kts+1) = 0.
        END DO

        CALL spectralnudgingfilterfft2dncar(nx,ny,nwave,sheet)

        DO k=kts,kte
           DO i=ids,ide
              f(i,k,j) = sheet(i-ids+1,k-kts+1)
           END DO
        END DO
  END DO ! outer j (latitude) loop

END SUBROUTINE spectral_nudging_filter_3dx
```

- **fftpack5** routines
  - call **rfftmi**(n,wsave,lensav,ier)
    - forward transform: initialize coefficients, place in wsave
  - call **rfftmf**( lot, jump, n, inc, fin, lenr, wsave, lensav, work, lenwrk, ier )
    - do the forward transform
  - filter all waves with wavenumber larger than nwave
  - call **rfftmb**( lot, jump, n, inc, fin, lenr, wsave, lensav, work, lenwrk, ier )
    - do the backward transform

**FFTPACK5** provides a suite of routines for computing Fast Fourier Transforms (FFTs) of both real and complex data, including 1D and 2D transforms, as well as multiple vectors. These routines are designed for efficiency and flexibility, supporting various data types (real/complex, single/double precision) and lengths.

```fortran
SUBROUTINE spectralnudgingfilterfft2dncar(nx,ny,nwave,fin)
  IMPLICIT NONE
  INTEGER , INTENT(IN) :: nx, ny, nwave
  REAL , DIMENSION(nx,ny), INTENT(INOUT) :: fin

  INTEGER :: i, j
  REAL, dimension(nx,ny) :: fp

  INTEGER :: lensave, ier, nh, n1
  INTEGER :: lot, jump, n, inc, lenr, lensav, lenwrk
  REAL, DIMENSION(nx+15) :: wsave
  REAL, DIMENSION(nx,ny) :: work

!  we are following the naming convention of the fftpack5 routines

  n = nx
  lot = ny
  lensav = n+15
  inc = 1
  lenr = nx*ny
  jump = nx
  lenwrk = lenr

!  forward transform
!  initialize coefficients, place in wsave
!   (should place this in init and save wsave at program start)

  call rfftmi(n,wsave,lensav,ier)
  IF(ier /= 0) THEN
    write(wrf_err_message,*) ' error in rfftmi ',ier
    CALL wrf_message(TRIM(wrf_err_message))
  END IF

!  do the forward transform

  call rfftmf( lot, jump, n, inc, fin, lenr, wsave, lensav, work, lenwrk, ier )
  IF(ier /= 0) THEN
    write(wrf_err_message,*) ' error in rfftmf ',ier
    CALL wrf_message(TRIM(wrf_err_message))
  END IF

      nh = min(max(1 + 2*nwave,0),n)


! filter all waves with wavenumber larger than nwave

  fp = 1.

  DO j=1,ny
     DO i=nh+1,n
         fp(i,j) = 0.
     ENDDO
  ENDDO

  DO j=1,ny
    DO i=1,nx
      fin(i,j) = fp(i,j)*fin(i,j)
    ENDDO
  ENDDO

!  do the backward transform

  call rfftmb( lot, jump, n, inc, fin, lenr, wsave, lensav, work, lenwrk, ier )
  IF(ier /= 0) THEN
    write(wrf_err_message,*) ' error in rfftmb ',ier
    CALL wrf_message(TRIM(wrf_err_message))
  END IF

END SUBROUTINE spectralnudgingfilterfft2dncar
```

# WRF setting

1. [Spectral nudging with ERA5 | Jan 9, 2023](https://forum.mmm.ucar.edu/threads/spectral-nudging-with-era5.12376/)
   1. In order to turn on **spectral nudging**, you will need to set `grid_fdda = 2`. The wavenumber nudged is selected in namelist (e.g., `xwavenum`, `ywavenum`, e.g. =3). Please choose the number so that 
      1. **(domain size)/(wavenumber)=~1000 km in each direction.**
2. [Gómez, B., and G. Miguez‐Macho. "The impact of wave number selection and spin‐up time in spectral nudging." Quarterly Journal of the Royal Meteorological Society 143.705 (2017): 1772-1786.](https://d1wqtxts1xzle7.cloudfront.net/80749838/Breo2017-libre.pdf?1644795289=&response-content-disposition=inline%3B+filename%3DThe_impact_of_wave_number_selection_and.pdf&Expires=1750824854&Signature=XTobtJZdY6gKmpCapI21Pyr~a6FLvqBkbigpFKtJQqTN~J40-lowROWCYPkDQpZeUlspxiqFdFhAZj9QpkZ3A-WA5XCcdAa8SdtKBzp3wJVji6lzI2n3tI49KGDQMoLVsUJqvvP9y-OdJKmwF1sTUytEAyVi0Nus-Xr9mpfqCh2PXMV3dVOkMr1gjzX1Nr7TlK9DmX0viZXB1hrhFTzDARNVhzs~JKZ~n6owVfy~-ozIt0PCJczXaU3Z~YPa4LF3vPXcdziTUAnO2CLFdnq992txN9YCps6JGdVuDnecQgUE9ig20DnutEinJ6m~M~gCb3ecMNhnRta-VxmX6HCAyQ__&Key-Pair-Id=APKAJLOHF5GGSLRBV4ZA)
   1. when selecting smaller scales, the fine-scale contribution of the model is damped, thus **making 1000km the appropriate scale threshold** to nudge in order to balance both effects. 
   2. $119 \times 105$ horizontal points at **36km resolution** and 33 vertical, $119 \times 36 = 4284km$
   3. ![](https://i.imgur.com/MG12PRA.png){width=400}
   4. ![](https://i.imgur.com/6xCRnQa.png){width=400}
3. [Silva, Natália Pillar da, and Ricardo de Camargo. "Impact of wave number choice in spectral nudging applications during a South Atlantic convergence zone event." Frontiers in Earth Science 6 (2018): 232.](https://www.frontiersin.org/journals/earth-science/articles/10.3389/feart.2018.00232/pdf)
   1. **1000km - R = the Rossby radius length for most mesoscale studies**
   2. ![](https://i.imgur.com/wMBuSSI.png){width=400}

# ERA5

## Spherical harmonic transforms (Not used)

**Performing a global spherical Fourier transform on ERA5 data in Python typically involves using spherical harmonic transforms**, which are the generalization of the Fourier transform to the sphere. This is particularly relevant in the context of global weather forecasting and climate modeling, where the Earth's spherical geometry needs to be accurately represented.

- Q: how to define **high wavenumber** to filter Rossby waves?

### Spherical Harmonic Transform Libraries:

- Utilize Python libraries designed for spherical harmonic transforms. pyshtools is a prominent library for this purpose, offering functions for forward and inverse spherical harmonic transforms.
- These libraries handle the complexities of representing functions on a sphere and performing the appropriate transforms.

```python
import pyshtools as sh
import xarray as xr # For handling ERA5 data

# Load ERA5 data (example, assuming 'era5_data.nc' exists)
ds = xr.open_dataset('era5_data.nc')
# Select a variable and time slice for transformation
data_variable = ds['temperature'].isel(time=0)

# Extract data as a numpy array, ensuring correct dimensions (latitude, longitude)
# Note: pyshtools often expects data ordered from north to south latitude
data_array = data_variable.values

# Perform the spherical harmonic transform
# This will return a SphericalHarmonicCoefficients object
coeffs = sh.SHGrid.from_array(data_array).expand()

# You can then analyze or manipulate 'coeffs'
# For example, filter out high-frequency components

# Reconstruct the data from the spherical harmonic coefficients
reconstructed_data = coeffs.to_grid()
```

## Empirical orthogonal function expansion (EOF) (Not used)

- have to find out needed components as Rossby waves (2D). 

## XY Fourier transform 

### real-space to k-space

![](https://i.imgur.com/Wv9jNcT.png){width=400}

$$
\begin{align}
f(x) &= \sum_{n=1}^{N} F(k_n) e^{ik_n x} \qquad x \in [0,L] \\
\text{Assume} \qquad f(x \pm  L) &= f(x)  \\
\Rightarrow e^{\pm i k_n L} &= 1 \qquad \forall n \\
& k_n (n = 0,1,...,N)\\
k_n L &= 2n \pi \qquad m \in \mathbb{Z} \\
k_n &= \frac{2 \pi}{L} n \\
dk &= \frac{2 \pi}{L} \\
\because \lambda &= \frac{2 \pi}{k} \\
\lambda_n &= \frac{L}{n}\\
\end{align}
\tag{1}
$$

| $n$ | $k_n$             | $\lambda$   |
|---|---------------------|-------------|
| 0 | $k_0 = 0$             | $\infty$    |
| 1 | $k_1= \frac{2\pi}{L}$ | $L$ |
| 2 | $k_1= \frac{2\pi}{L} \times 2$ | $\frac{L}{2}$ |
| 3 | $k_1= \frac{2\pi}{L} \times 3$ | $\frac{L}{3}$ |
| $\cdots$ | $\cdots$  | $\cdots$  |
| N | $k_1= \frac{2\pi}{L} \times N$| $\frac{L}{N}$ |

### np.fft2 

Performing 2D Fourier transforms (forward and backward) in Python is typically done using the **numpy.fft** module, particularly the **fft2** and **ifft2** functions

{% fold info @FFT %}
```python
import numpy as np
import matplotlib.pyplot as plt

# Create a sample 2D image (e.g., a 2D Gaussian)
# L = [0, 1]
def create_sample_image(size=1280):
    x = np.linspace(0.0, 1.0, size)
    y = np.linspace(0.0, 1.0, size)
    X, Y = np.meshgrid(x, y)
    image = np.sin(2*X*2*np.pi) + 0.5*np.sin(3*X*2*np.pi) + 0.2*np.sin(10*X*2*np.pi) + 0.1*np.sin(25*X*2*np.pi)
    return image

# Perform 2D Fourier Transform, filter high wavenumbers, and inverse transform
def fourier_filter(image, Nx_kmax, Ny_kmax):
    # Step 1: Perform 2D FFT
    f_transform = np.fft.fft2(image)
    f_transform_shifted = np.fft.fftshift(f_transform)  # Shift zero to center
    
    # Step 2: Create a low-pass filter mask
    # Create wavenumber grid
    N = image.shape[0]  # Grid size
    print("N = ", N)
    # L = 20.0  # Domain length
    # dx = L / N
    # dk = 2 * np.pi / L 
    # k = np.linspace(-N*dk/2, N*dk/2, N, endpoint=False)
    k = np.linspace(-N/2, N/2, N, endpoint=False)
    KX, KY = np.meshgrid(k, k)

    # Define high-wavenumber filter (e.g., remove wavenumbers above N_kmax)
    filter_mask_KX = np.abs(KX) <= Nx_kmax
    filter_mask_KY = np.abs(KY) <= Ny_kmax
    
    # Step 3: Apply the filter in k domain
    f_transform_filtered = f_transform_shifted * filter_mask_KX
    f_transform_filtered = f_transform_filtered * filter_mask_KY
    
    # Step 4: Inverse FFT
    f_transform_unshifted = np.fft.ifftshift(f_transform_filtered)  # Shift back
    filtered_image = np.fft.ifft2(f_transform_unshifted)
    filtered_image = np.real(filtered_image)  # Take magnitude to ensure real output
    
    return filtered_image, f_transform, f_transform_filtered

# Main execution
if __name__ == "__main__":
  # Generate sample image
  size = 1280
  x = np.linspace(0.0, 1.0, size)
  k = np.linspace(-size/2, size/2, size, endpoint=False) # unit = (2*np.pi/1.0)
  # m_lambda = np.linspace(-size/2, size/2, size, endpoint=False) # unit = L/

  image = create_sample_image(size)

  # Apply Fourier transform and filtering
  Nx_kmax = 6 # size/2
  Ny_kmax = 30
  filtered_image, f_transform, f_transform_filtered = fourier_filter(image, Nx_kmax, Ny_kmax)

  #===============================================================
  # One Dim
  plt.figure(figsize=(12, 4))

  plt.suptitle(f"Cut-off wavenumber \n Nx_kmax = {Nx_kmax}", fontsize=24)

  plt.subplot(131)
  plt.imshow(image, cmap='gray')
  plt.title('Original Image')
  plt.colorbar()

  plt.subplot(132)
  plt.imshow(np.log(np.abs(f_transform) + 1), cmap='gray')
  plt.title('Fourier Transform (log scale)')
  plt.colorbar()

  plt.subplot(133)
  plt.imshow(filtered_image, cmap='gray')
  plt.title('Filtered Image')
  plt.colorbar()

  plt.tight_layout()
  plt.show()
  plt.close()

  #===============================================================
  # Visualize results
  plt.figure(figsize=(12, 8))

  plt.subplot(221)
  plt.plot(x, image[0, :])
  plt.grid()
  plt.title('Original Image')

  plt.subplot(222)
  plt.plot(k, np.log(np.abs(f_transform[0, :]) + 1), 'ro')
  plt.xlabel('k [unit = (2*np.pi/L)]')
  plt.grid()
  plt.title('Fourier Transform (log scale)')

  plt.subplot(223)
  plt.plot(x, filtered_image[0, :], label='Filtered Image')
  plt.title('Filtered Image')
  plt.grid()
  plt.legend()

  plt.subplot(224)
  plt.plot(x, image[0, :], label='Original Image')
  plt.plot(x, filtered_image[0, :], label='Filtered Image')
  plt.title('Filtered Image')
  plt.grid()
  plt.legend()

  plt.tight_layout()
  plt.show()
  plt.close()

  #===============================================================
  # k and \lambda : Fourier Transform (log scale)
  plt.figure(figsize=(12, 4))

  plt.subplot(121)
  plt.plot(k, np.log(np.abs(f_transform[0, :]) + 1), 'ro')
  plt.xlabel('k [unit = (2*np.pi/L)]')
  plt.grid()
  plt.title('Fourier Transform (log scale)')

  plt.subplot(122)
  plt.plot(k[:10], np.log(np.abs(f_transform[0, :10]) + 1), 'ro')
  # plt.xlabel('m [unit = (L/\lambda)]')
  plt.xlabel('k [unit = (2*np.pi/L)]')
  plt.grid()
  plt.title('Fourier Transform (log scale)')

  plt.tight_layout()
  plt.show()
  plt.close()

  #===============================================================
  # k and \lambda : Fourier Transform 
  plt.figure(figsize=(12, 4))

  plt.subplot(121)
  plt.plot(k+size/2, np.abs(f_transform[0, :]), 'ro')
  plt.xlabel('k [unit = (2*np.pi/L)]')
  plt.grid()
  plt.title('Fourier Transform')

  plt.subplot(122)
  plt.plot(k[:30]+size/2, np.abs(f_transform[0, :30]), 'ro')
  # plt.xlabel('m [unit = (L/\lambda)]')
  plt.xlabel('k [unit = (2*np.pi/L)]')
  plt.grid()
  plt.title('Fourier Transform')

  plt.tight_layout()
  plt.show()
  plt.close()

  #===============================================================
  plt.plot(filtered_image[0, :]- image[0, :], 'ro')
  plt.title('Filtered Image - Original Image')
  plt.grid()
  plt.tight_layout()
  plt.show()
```
{% endfold %}

### Checking

#### Orignal signal

![](https://i.imgur.com/2PNh9NN.png)

### Add some noises

![](https://i.imgur.com/qnAUvMc.png)

### More wavenumbers

![](https://i.imgur.com/eoLuDUn.png)

{% gi 4 2-2 %}
![](https://i.imgur.com/ZE0DaUv.png)
![](https://i.imgur.com/ruF7m81.png)
![](https://i.imgur.com/EvlQcxd.png)
![](https://i.imgur.com/oAAwUSR.png)
{% endgi %}

### 2D waves

```python
image = np.sin(2*X*2*np.pi) + 0.5*np.sin(3*X*2*np.pi) + 0.2*np.sin(10*X*2*np.pi) + 0.1*np.sin(25*X*2*np.pi)
image = image*(np.exp(-(Y-0.25)**2/(0.1**2)) + np.exp(-(Y-0.75)**2/(0.1**2)))
```

![](https://i.imgur.com/FDgr1gW.png)

- plot at `y = 0.25`

![](https://i.imgur.com/D0R77j6.png)

#### Results

{% gi 10 2-2-2-2-2 %}
![](https://i.imgur.com/9curLqh.png)
![](https://i.imgur.com/KuZibwt.png)
![](https://i.imgur.com/nDnjREn.png)
![](https://i.imgur.com/6cNemc8.png)
![](https://i.imgur.com/pv44Nbk.png)
![](https://i.imgur.com/0r67qGZ.png)
![](https://i.imgur.com/8yJqXNp.png)
![](https://i.imgur.com/LyKNyHM.png)
![](https://i.imgur.com/aLZVofF.png)
![](https://i.imgur.com/gYzJcpL.png)
{% endgi %}

### Thinking

-  Rationale for the Approach
   - We can express the relationship as $\lambda_m = \frac{L}{m}$. If the length $L$ decreases while maintaining a constant mass $m$, the resulting wavelength $\lambda_m$ becomes shorter, indicating that the corresponding wave number $k$ increases.
- This increase in $k$ shifts the distribution towards higher values, resulting in a greater number of higher $k$ values being included in the analysis.

# Apply on MPAS-A/ERA5

