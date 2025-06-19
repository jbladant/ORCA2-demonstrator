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
The namelist used for running the DOMAINcfg tool is provided in this github repository (cf. *namelists/namelist_DOMAINcfg*).
In DOMANIcfg folder, run
```
./make_domain_cfg.exe 
./dom_doc.exe
```
In addition, the following steps allows you to achieve a NEMOv5-compliant domaing_cfg file
```
ncks -O --no_abc -C -x -v jpiglo,jpjglo,jpkglo,ORCA,ORCA_index,jperio,ln_zco,ln_zps,ln_sco,ln_isfcav,time_counter domain_cfg.nc domain_cfg_tmp.nc
ncatted -a Iperio,global,c,l,1 -a NFtype,global,c,c,T -a CfgName,global,c,c,ORCA -a CfgIndex,global,c,l,2 -a Jperio,global,c,l,0 -a NFold,global,c,l,1 -a NFType,global,c,c,T -a VertCoord,global,c,c,zps -a IsfCav,global,c,l,0 -a NEMOversion,global,c,c,5.f domain_cfg_tmp.nc domain_cfg.nc
```

