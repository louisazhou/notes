---
description: >-
  Use Machine Learning and Deep Learning to predict sea-ice albedo in the polar
  regions with satellite radiance data
---

# Sea Ice Albedo Retrievals

sea ice physical parameters: 

brine pocket concentration effective brine pocket size, 

air bubble concentration and effective air bubble size, 

volume fraction of ice [impurities](https://www.sciencedirect.com/topics/physics-and-astronomy/impurities) and impurity [absorption coefficient](https://www.sciencedirect.com/topics/physics-and-astronomy/absorptivity),  

sea ice thickness.

angles of [illumination](https://www.sciencedirect.com/topics/chemistry/illumination) and observation





snow physical parameters \(snow effective grain size, density, and impurity concentration\)



generate broad-band albedo over sea ice

## Summary

* Evaluated broadband \(VIS, NIR, SW\) TOA sea ice albedos with radiance received by seven MODIS and SGLI channels.
* Visible-band albedo derived from MODIS-channel and SGLI-channel radiance shows good resemblance. 
* \(?\) haven't reached the phase of characterizing surface yet 

## RT model for coupled atmosphere-sea ice/ocean system

Physical processes in the coupled atmosphere-sea ice/ocean system include:

* absorption and scattering by atmospheric molecules, clouds, and aerosols
* absorption and scattering by snow
* absorption by pure ice, and absorption and scattering by inclusions in sea ice \(brine pockets and air bubbles\)
* absorption and scattering by sea water and by hydrosols in the ocean.

**Radiative Transfer Equation**

**Isolation of Azimuth Dependence**

Optical Properties and Parameterization of each stratum in the system:

* clouds: Equivalent Radius \(ER\) and the Liquid Water Content \(LWC\) of the clouds  
* snow: snow effective grain size, density, and impurity concentration
* pure ice: brine pockets, air bubbles, ice temperature, density, and salinity 

Input Parameters:

* incident solar beam irradiance \[W·m−2 \], solar zenith angle\(s\) \[degree\]
* profiles of temperature, pressure, gas and aerosol concentrations in the atmosphere
* ER and LWC of clouds, cloud height, and thickness
* surface temperature and snow conditions
* profiles of temperature, salinity, and density in the ice; or profiles of volume fractions of gas and brine inclusions in the ice
* vertical distribution of hydrosols in ocean.

Output Parameters:

* irradiances and mean intensities \(scalar irradiances in Ocean Optics terminology\) at specified vertical positions in the coupled system
* total and polarized radiances in desired directions at specified vertical positions in the coupled system.

## Generation of synthetic data set

![Sea ice physical properties New Young \(NY\), First-Year \(FY\), and Multi-Year \(MY\) sea ice.](https://cdn.mathpix.com/snip/images/v9XMEDWr6v5Bi48phU5SOShg9pEZuGHdjm8WWy7Tg0A.original.fullsize.png)

> Table 1, Modeling of radiation transport in coupled atmosphere-snow-ice-ocean systems

Fit relations between ice thickness \(m\) with each of the other sea ice physical parameters based on the table. 

  
aerosol optical depths

![Comparison of ISIOP ISBRDF derived sea ice spectral albedos for New Young \(NY\), First-Year \(FY\)  ice for several ice thicknesses with observed spectral albedos.](.gitbook/assets/image%20%2866%29.png)

> Graph 9, Modeling of radiation transport in coupled atmosphere-snow-ice-ocean systems

Generate tables of radiance of bare sea ice, snow-covered sea ice, and melting-pond covered sea ice at selected bands as well as broadband albedo were generated \(run with xx streams\) for each combination of sea ice type \(NY, FY, MY\) for a range of viewing geometries and wavelengths

wavelength:  
solar:  
sensor:  
azimuth:  
snow thickness: \[0, \)  
melting pond depth: \[0, \)   
  
\* snow thickness=0 & melting pond depth=0 for bare sea ice  
  


## Training of Neural Network

3-layer Neural Network, with ReLU \(a=max\(0,x\)\) as the activation function.

> Graph

| Visible | Near Infrared | ShortWave |
| :--- | :--- | :--- |
|  |  |  |

> Graph of RMSE

## Application to synthetic data \(sanity tests\) 

> Graph cloud-masked image of broadband albedo with MODIS-channel radiance data

shows reasonable xx of xx 

## Application to real data

comparison between same day MODIS and SGLI sea ice albedo results

> wait for Nan's cloud mask / or cut the corner

## Future Work

1. Characterize madeup  
2. xx - work in progress

