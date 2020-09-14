# Lidar Project

## Questions

1. The current RT models we have? How many variations and what's the difference?

> **AccuRT** \(interpolated angles\) coupled, polarization 
>
> DISORT arbitrary angles

> **VDISORT** arbitrary angles, polarization \(snow is ready, ice is not ready\) 
>
> **Robert** coupled, arbitrary angles \(only ocean, need to add snow/ice\)

Run AccuRT, abs/scattering coefficients, output; How abs/scattering vary with snow layer  
Use it as input in Robert's model

Check radiance straight out from the snow surface \(sun overhead\)

~~Understand how to set up IOP for VDISORT~~ 

1. Availability of scaler 1DSS and 1DTF model?
2. \(if available\) Progress of testing against 3D Monte Carlo?
3. My main part is testing against synthetic? validation against CALIOP? or extending the scalar to vector? or extend to the coupled atmosphere-water system?
4. Put sea-ice in the big picture? 
5. What about 'Time Series prediction of sea ice albedo' project?
6. For Prof. Huang's project, we'll be using AccuRT? or vector 1D VDISORT? Do we need to test against MC for this one, or do we know already that the result will be good enough? 



