This page describes how to build an ORCA2-based configuration with a different number of vertical levels.
Here, we build a 75 levels configuration, hereafter ORCA2L75 configuration.
The tutorial relies on DOMAINcfg tool (NEMO version 5) and [**SOSIE interpolator**](https://github.com/brodeau/sosie).

### Build the domain file
To create the domain file for the ORCA2L75 configuration, DOMAINcfg tool requires as input the coordinates and bathymetry of the new configuration. 
As the horizontal grid is shared with the ORCA2 reference configuration, latitute and longitude can be retrieved from it.
Differently, the bathymetry file used here is a modified version of the reference configurantion (check this with Seb).

DOMAINcfg tool is compiled in the folder ~/nemo_5.0.1/tools with the command
```
./maketools -m ARCH_FILE -j N_PROCS -n DOMAINcfg
```
If it does not work, try uploading the architecture environment manually with the command *source* ~/nemo_5.0.1/arch/your_architecture.env.
The 
