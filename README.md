# ORCA2-demonstrator

The ORCA2 demonstrator is a use case for the NEMO ocean model based on the default configuration ORCA2_ICE_PISCES. This tutorial describes how to set-up and run the default configuration as well as selected adjustments to customise the simulation. 

#### The default ORCA2_ICE_PISCES

[The reference configuration tutorial](https://github.com/jbladant/ORCA2-demonstrator/blob/main/Running%20ORCA2_ICE_PISCES.md) describes the steps necessary for compiling and running the default ORCA2_ICE_PISCES configuration. This is the prerequisite for making the adjustment described in the following. 

<!-- #### Making adjustments for different levels of experience with NEMO -->
#### Customizations
[Adding output variables](https://github.com/jbladant/ORCA2-demonstrator/blob/main/Add%20new%20outputs.md) enables to expand the output fields and to change their frequency by modifying the xml files. An example is provided with the depth of the minimum oxygen cocentration.

[Changing ORCA2 vertical resolution](https://github.com/jbladant/ORCA2-demonstrator/blob/main/ORCA2L75_configuration.md) documents the steps for increasing the number of levels from 31 levels (default) to 75 levels by using the DOMAINcfg tool and provides tips for interpolating ORCA2_ICE reference configuration input fields.

[The Paleo Earth set-up for the early Eocene](https://github.com/jbladant/ORCA2-demonstrator/blob/main/Paleo_Earth.md) includes changes in location of continents, rotating the tripolar grid, and changing bathymetry.

This demonstrator is a contribution to the NEMO Zoo hackathon 2025.

Exeter, 20th June 2025

<img src="https://www.nemo-ocean.eu/wp-content/uploads/graphics.004-1024x576.jpeg" align="center" width="400"> 


