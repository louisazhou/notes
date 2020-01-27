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

> Development of surface characterization sea ice albedo

* Fitted equations that link each of the sea ice physical parameters with sea ice thickness.
* Trained a 3-layer Neural Network model to retrieve broadband sea ice albedo based on radiance data, achieving 0.3% RMSE.
* Achieved reasonable retrieval results when applied on MODIS images.
* Application of the trained model to SGLI images \(and testing/validation against MODIS results

  \) -- work in progress.

## RT model for coupled atmosphere-sea ice/ocean system

#### Physical processes in the coupled atmosphere-sea ice/ocean system include:

* Absorption and scattering by atmospheric molecules, clouds, and aerosols.
* Absorption and scattering by snow.
* Absorption by pure ice, and absorption and scattering by inclusions in sea ice \(brine pockets and air bubbles\).
* Absorption and scattering by sea water and by hydrosols in the ocean.

#### **Radiative Transfer Equation**

#### **Isolation of Azimuth Dependence**

#### **Air-Water Interface**

#### Optical Properties and Parameterization of each stratum in the system:

* Clouds: Equivalent Radius \(ER\) and the Liquid Water Content \(LWC\) of the clouds.
* Snow: snow effective grain size, density, and impurity concentration.
* Sea ice: brine pockets, air bubbles, ice temperature, density, and salinity. 
* Ocean: chlorophyll concentration.

#### Input Parameters:

* Incident spectral radiation at the top of atmosphere.
* Profiles of temperature, pressure, gas and aerosol concentrations in the atmosphere.
* ER and LWC of clouds, cloud height, and thickness.
* Surface temperature and snow conditions.
* Profiles of temperature, salinity, and density in the ice; or profiles of volume fractions of gas and brine inclusions in the ice.
* Vertical distribution of hydrosols in ocean.

#### Output Parameters:

* Irradiances and mean intensities \(scalar irradiances in Ocean Optics terminology\) at specified vertical positions in the coupled system.
* Total and polarized radiances in desired directions at specified vertical positions in the coupled system.

## Generation of Synthetic Data Set

Generate angles of [illumination](https://www.sciencedirect.com/topics/chemistry/illumination) and observation

Solar zenith angle: \[20,80\] degree

Sensor angle: \[0.01~65\] degree

Azimuth angle: \[0.01~180\] degree

Fit equations for sea ice physical parameters based on the table from 

![Sea ice physical properties of New Young \(NY\), First-Year \(FY\), and Multi-Year \(MY\) sea ice.](https://cdn.mathpix.com/snip/images/v9XMEDWr6v5Bi48phU5SOShg9pEZuGHdjm8WWy7Tg0A.original.fullsize.png)

> Table 1, Modeling of radiation transport in coupled atmosphere-snow-ice-ocean systems

Fit relations between ice thickness \(m\) with each of the other sea ice physical parameters based on the table. 

  
aerosol optical depths

![Comparison of ISIOP ISBRDF derived sea ice spectral albedos for New Young \(NY\), First-Year \(FY\)  ice for several ice thicknesses with observed spectral albedos.](.gitbook/assets/image%20%2877%29.png)

> Graph 9, Modeling of radiation transport in coupled atmosphere-snow-ice-ocean systems

Generate tables of radiance of bare sea ice, snow-covered sea ice, and melting-pond covered sea ice at selected bands as well as broadband albedo were generated \(run with xx streams\) for each combination of sea ice type \(NY, FY, MY\) for a range of viewing geometries and wavelengths

Wavelength:  
Aerosol Optical Depth of background aerosols:  
solar:  
sensor:  
azimuth:  
snow thickness: \[0, \)  
melting pond depth: \[0, \)   
  
\* snow thickness=0 & melting pond depth=0 for bare sea ice  
  


## Training of Neural Network

A 3-layer Neural Network, with ReLU \(a=max\(0,x\)\) as the activation function, was trained to predict albedos from visible\(\), near infrared\(\), and short wave range.

> Graph

| Visible | Near Infrared | Short Wave |
| :--- | :--- | :--- |
|  |  |  |

> Graph of RMSE

## Application to Synthetic Data \(sanity tests\) 

> Graph cloud-masked image of broadband albedo with MODIS-channel radiance data

shows reasonable xx of xx 

## Application to real data

comparison between same day MODIS and SGLI sea ice albedo results

> wait for Nan's cloud mask / or cut the corner

## Future Work

1. Application to SGLI images \(and testing/validation against MODIS results\) is currently in progress.
2. Improve the albedo retrieval for thin, New Young \(formed within 1 year\) sea ice.
3. Surface classification during melting and snow-falling seasons based on albedo retrievals.
4. Sea ice physical parameters retrieval based on TOA albedo.

