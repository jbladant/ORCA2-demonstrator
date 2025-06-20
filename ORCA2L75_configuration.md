## A new ORCA2 reference configuration with 75 vertical levels

This how-to page describes how to build an ORCA2-based configuration with a different number of vertical levels.
Here, we build a 75 levels configuration, hereafter called ORCA2L75 configuration.
The tutorial relies on the DOMAINcfg tool from NEMOv5 and [*SOSIE interpolator*](https://github.com/brodeau/sosie).

### Build the domain file
To create the domain file for the ORCA2L75 configuration, DOMAINcfg tool requires as input the coordinates and bathymetry of the new configuration. 
As the horizontal grid is shared with the ORCA2 reference configuration, latitute and longitude (i.e. nav_lat, nav_lon) can be retrieved from its I/O files.
Differently, the bathymetry file used here is a modified version of the reference configurantion. Changes have been made in the shallower regions to take advantage of the improved vertical resolution.

DOMAINcfg tool is compiled in the folder ~/nemo_5.0.1/tools with the command
```
./maketools -m ARCH_FILE -j N_PROCS -n DOMAINcfg
```
If it does not work, upload the architecture environment manually by running *source* ~/nemo_5.0.1/arch/your_architecture.env.

The namelist used for DOMAIN_cfg is provided [*here*](https://github.com/jbladant/ORCA2-demonstrator/blob/main/namelists/namelist_DOMAINcfg).
To run the tool, execute
```
./make_domain_cfg.exe
```
Afterwards, the following steps allows you to achieve a NEMOv5-compliant domaing_cfg file
```
./dom_doc.exe

ncks -O --no_abc -C -x -v jpiglo,jpjglo,jpkglo,ORCA,ORCA_index,jperio,ln_zco,ln_zps,ln_sco,ln_isfcav,time_counter domain_cfg.nc domain_cfg_tmp.nc

ncatted -a Iperio,global,c,l,1 -a NFtype,global,c,c,T -a CfgName,global,c,c,ORCA -a CfgIndex,global,c,l,2 -a Jperio,global,c,l,0 -a NFold,global,c,l,1 -a NFType,global,c,c,T -a VertCoord,global,c,c,zps -a IsfCav,global,c,l,0 -a NEMOversion,global,c,c,5.f domain_cfg_tmp.nc domain_cfg.nc
```
In addition, the straits are taken into account with the command
```
ncap2 -O --ftn -s "/* create strait shlat: */ ; strait_shlat=array(-1.,0.,glamt) ;" \
               -s "/* Gibaltar: */ ; e2u(:,102,138:139)=20.e3 ; strait_shlat(:,101:102,138:139)=0.5 ;" \
               -s "/* Bab el Mandeb: */ ; e2u(:,88,158:159)=18.e3 ; e1v(:,87:88,159)=18.e3 ; strait_shlat(:,88,158:159)=1.0 ; strait_shlat(:,87,159)=1.0 ;" \
               -s "/* Danish Straits: */ ; e2u(:,116,144:145)=10.e3 ;" \
               domain_cfg.nc domain_cfg_ORCA2L75.nc
```
The generated domain file is domain_cfg_ORCA2L75.nc. 

### Interpolate initial conditions and forcing files
Three-dimensional files provided for the ORCA2_ICE reference configuration (https://gws-access.jasmin.ac.uk/public/nemo/sette_inputs/) must be interpolated.
The used tool is Sosie, which enables the interpolation between ORCA grids.
Please, refer to https://github.com/brodeau/sosie/blob/master/README.md for software compilation and run.
An example of the namelist used for interpolating the initial salinity is provided in *namelists/namelist.sosie.salinity*.

The fields of reference configuration that requires interpolation are 
- initial potential temperature and salinity
- 3D eddy viscosity, monthly reference salinity
- restoring.

Since it does not comply the sosie requirements, the initial potential temperature file needs some manipulation before being interpolated. 
The strainghforward way to solve the problem is to extract the variable from the netcdf file and add it to the initial salinity file.
The reader shoud note that running the configuration ORCA2_ICE_PISCES requires extra interpolations.

### Modifications of the NEMO namelist for ORCA2L75
The ORCA2_ICE_PISCES configuration namelist has been modified to avoid initial numerical instabilities by defining a time step of 5400 seconds.
Furthermore, the temperature and salinity Newtonian dumping has been deactivated (ln_tradmp).


