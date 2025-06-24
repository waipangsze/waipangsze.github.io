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
index_img: https://i.imgur.com/oOsTWeD.png
banner_img: https://i.imgur.com/oOsTWeD.png
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

# ERA5

## Spherical harmonic transforms (Not used)

**Performing a global spherical Fourier transform on ERA5 data in Python typically involves using spherical harmonic transforms**, which are the generalization of the Fourier transform to the sphere. This is particularly relevant in the context of global weather forecasting and climate modeling, where the Earth's spherical geometry needs to be accurately represented.

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

## XY Fourier transform 

Performing 2D Fourier transforms (forward and backward) in Python is typically done using the **numpy.fft** module, particularly the **fft2** and **ifft2** functions

{% fold info @FFT %}
```python
import numpy as np
import matplotlib.pyplot as plt

# Create a sample 2D image (e.g., a 2D Gaussian)
def create_sample_image(size=128):
    x = np.linspace(-10.0, 10.0, size)
    y = np.linspace(-10.0, 10.0, size)
    X, Y = np.meshgrid(x, y)
    image = np.sin(0.2*X*np.pi) + 0.5*np.sin(5*X*np.pi) + 0.2*np.sin(20*X*np.pi)
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
    # np.abs() fix the box of k-space
    filter_mask_KX = np.abs(KX) <= Nx_kmax
    filter_mask_KY = np.abs(KY) <= Ny_kmax
    
    # Step 3: Apply the filter in k domain
    f_transform_filtered = f_transform_shifted * filter_mask_KX
    f_transform_filtered = f_transform_filtered * filter_mask_KY
    
    # Step 4: Inverse FFT
    f_transform_unshifted = np.fft.ifftshift(f_transform_filtered)  # Shift back
    filtered_image = np.fft.ifft2(f_transform_unshifted)
    filtered_image = np.real(filtered_image)  # Take magnitude to ensure real output
    
    return filtered_image, f_transform_shifted, f_transform_filtered

# Main execution
if __name__ == "__main__":
    # Generate sample image
    size = 1280
    image = create_sample_image(size)

    # Apply Fourier transform and filtering
    Nx_kmax = 3 # size/2
    Ny_kmax = 30
    filtered_image, f_transform, f_transform_filtered = fourier_filter(image, Nx_kmax, Ny_kmax)

    # Visualize results
    plt.figure(figsize=(12, 4))

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

    # Visualize results
    plt.figure(figsize=(12, 8))

    plt.subplot(221)
    plt.plot(image[0, :])
    plt.title('Original Image')

    plt.subplot(222)
    plt.plot(np.log(np.abs(f_transform[:, 0]) + 1), 'ro')
    plt.title('Fourier Transform (log scale)')

    plt.subplot(223)
    plt.plot(filtered_image[0, :], label='Filtered Image')
    plt.title('Filtered Image')
    plt.legend()

    plt.subplot(224)
    plt.plot(image[0, :], label='Original Image')
    plt.plot(filtered_image[0, :], label='Filtered Image')
    plt.title('Filtered Image')
    plt.legend()

    plt.tight_layout()
    plt.show()
    plt.close()


    plt.plot(filtered_image[0, :]- image[0, :], 'ro')
    plt.title('Filtered Image - Original Image')
    plt.tight_layout()
    plt.show()
```
{% endfold %}

### Checking

#### Orignal signal

![](https://i.imgur.com/2PNh9NN.png)

### Add some noises

![](https://i.imgur.com/qnAUvMc.png)

# Apply on MPAS-A/ERA5

