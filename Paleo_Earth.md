# Tutorial for creating Paleo Earth

This tutorial allows you to run an Early Eocene (~ 55 Ma) NEMO simulation.
The bathymetry follows that provided by the model intercomparison project focussing on the Early Eocene ([**DeepMIP**](https://www.deepmip.org/deepmip-eocene/)). Details about the boundary conditions can be found [**here**](https://gmd.copernicus.org/articles/10/889/2017/) and the first results of this exercise are documented [**here**](https://cp.copernicus.org/articles/17/203/2021/cp-17-203-2021.html).
This training guide is based on a modified global ORCA2 grid with displaced poles to accommodate the different global land/sea mask. The nominal resolution is 2° with enhancement in the equatorial band up to 0.5° resolution at the equator).


## 2. Files provided

All of the boundary conditions files, adapted for the Early Eocene when necessary, are provided.
All necessary namelists to run the model are provided.
(if time allows it) Modified sources (to run with PISCES adapted for the paleo) are provided.

These files are archived in PALEORCA2_demonstrator_forcings.tar.gz.
Copy this zipped tarball to your HPC and extract the content

```
scp PALEORCA2_demonstrator_forcings.tar.gz $WORK
(after logging into your HPC)
cd $WORK
gunzip PALEORCA2_demonstrator_forcings.tar.gz
tar -xvf PALEORCA2_demonstrator_forcings.tar
```

You should now have a folder PALEORCA2_demonstrator_forcings/ with all the necessary files to run the demonstrator in your $WORK.


## 3. Paleoclimate instructions

### 3.0 Compiling XIOS and NEMO

Please refer to [**Running ORCA2_ICE_PISCES**](https://github.com/jbladant/ORCA2-demonstrator/blob/main/Running%20ORCA2_ICE_PISCES.md) and use "PALEORCA2" as configuration name (option -n) when compiling NEMO.

### 3.1 Create the new DOMAIN

First, you need to compile the DOMAINcfg tool located in $WORK/your_NEMO_dirname/tools
To do this, you need to follow the same logic than for compiling NEMO (notably use the same arch files) and execute the maketools command.
Using the Irene supercomputer as an example (thus the arch files would be X64_IRENE.fcm, X64_IRENE.env and X64_IRENE.path), do the following:
```
cd $WORK/your_NEMO_dirname/tools
./maketools -m X64_IRENE -n DOMAINcfg
```
Note that the same logic is to be followed if you need to compile other tools provided by NEMO, like WEIGHTS for instance.

Create a DOMAIN_PALEORCA2 folder in your PALEORCA2_demonstrator_forcings directory:
```
cd $WORK/PALEORCA2_demonstrator_forcings
mdkir DOMAIN_PALEORCA2
cd DOMAIN_PALEORCA2
```

Then link the reference namelist and executables from the $WORK/your_NEMO_dirname/tools/DOMAINcfg into your DOMAIN_PALEORCA2 folder:
```
ln -s $WORK/your_NEMO_dirname/tools/DOMAINcfg/BLD/bin/make_domain_cfg.exe .
ln -s $WORK/your_NEMO_dirname/tools/DOMAINcfg/BLD/bin/dom_doc.exe .
ln -s $WORK/your_NEMO_dirname/tools/DOMAINcfg/namelist_ref . 
```

From the PALEORCA2_demonstrator_forcings folder, link files specific for the paleo (Early Eocene) configuration:
```
ln -s $WORK/PALEORCA2_demonstrator_forcings/PALEORCA2_coordinates.nc coordinates.nc
ln -s $WORK/PALEORCA2_demonstrator_forcings/PALEORCA2_bathy_meter.nc bathy_meter.nc
ln -s $WORK/PALEORCA2_demonstrator_forcings/PALEORCA2_namelist_cfg_dom namelist_cfg
```

The configuration namelist provided is already populated with the appropriate information.
The major changes from the reference namelist (namelist_ref) are as follows:
- &namrun namelist
  - the experience name cn_exp has been changed to "domaincfg"
- &namdom namelist
  - the nn_msh flag is set to 1 to create the NEMO mesh_mask adapted to this specific bathymetry
  - ppglam0, ppgphi0, ppe1_deg, ppe2_deg, ppe1_m and ppe2_m are set to 999999.0 because these values are used when jphgr_msh = 1, which is not the case here.
  - ppdzmin and pphmax are set to 999999.0 because they are not used as ppsur, ppa0 and ppa1 are defined.
  - ldbletanh is set to .false. and coefficients ppa2, ppkth2 and ppacr2 to 999999. as appropriate for a standard, 31 levels, ORCA2-like grid.
- &namcfg namelist
  - cn_cfg = "paleorca"
  - jpjdta = 173 and Nj0glo = 173 because the PALEORCA2 grid has more j-levels to extend the grid towards the South Pole.
  - jperio = 6 (i.e. F-point pivot) instead of standard T-point pivot of the original ORCA2 grid (jperio = 4)
- &namzgr namelist
  - ln_zps = .true.
- &namlbc namelist
  - rn_shlat = 2 => This is to be consistent with what is set for the dynamics later but really, it does not seem to matter really here.
 
Then execute make_domain_cfg.exe and dom_doc.exe (the latter adding the namelist_cfg to the domain_cfg.nc file as a variable, for tracability):
```
./make_domain_cfg.exe
./dom_doc.exe
```
The outputs are named mesh_mask.nc and domain_cfg.nc.

To comply with NEMOv5.0.1 formalism, a few manipulations are done:
- Rename the z axis:
```
ncrename -d nav_lev,z domain_cfg.nc
```
- Remove no longer necessary variables:
```
ncks -O --no_abc -C -x -v jpiglo,jpjglo,jpkglo,jperio,ln_zco,ln_zps,ln_sco,ln_isfcav,time_counter domain_cfg.nc PALEORCA2_domain_cfg.nc
```
- Edit the global attributes as per instructed in the migration document from 4.0 to 4.2 and later:
```
ncatted -O -a Iperio,global,c,l,1 \
           -a Jperio,global,c,l,0 \
           -a NFold,global,c,l,1 \
           -a NFtype,global,c,c,'F' \
           -a CfgName,global,c,c,'PALEORCA' \
           -a CfgIndex,global,c,l,2 \
           -a VertCoord,global,c,c,'zps' \
           -a IsfCav,global,c,l,0 \
           PALEORCA2_domain_cfg.nc
```
The same can be done for the mesh_mask.nc file.

### Run a PALEORCA2 simulation of the Early Eocene

You are now ready to run a NEMO simulation of the Early Eocene. 
Edit: for now, the tutorial does not include PISCES or TOP because Early Eocene surface and bathymetric forcings for PISCES do not yet exist; however, the namelist and xml files required to run with TOP/PISCES are included.
Essentially, this is done by setting the flag ln_top to false in namelist_cfg (see below).

Go the the PALEORCA2 folder that has been created as part of the compilation step:
```
cd $WORK/your_NEMO_dirname/cfgs/PALEORCA2
```

Copy the reference experiment EXP00 to your preferred experiment name (EXPTEST for the purpose of this tutorial) and cd there:
```
cp -r EXP00 EXPTEST
cd EXPTEST
```

In EXPTEST are present:
- field_def and file_def xml files for handling inputs/outputs. See [**here**](https://github.com/jbladant/ORCA2-demonstrator/blob/main/Add%20new%20outputs.md) to for an example on how to add new outputs.
- reference and standard configuration namelists. The configuration namelists will be overwritten by those provided in the PALEORCA2_demonstrator_forcings folder.
- Other xml files required to XIOS and a link to the nemo executable.
- Depending on you HPC, you may need to copy the XIOS executable to EXPTEST.

Now is the time to copy (or ln -s) the forcing and namelist files located in the PALEORCA2_demonstrator_forcings folder.
Let's start with I/O and namelist files.
```
ln -fs $WORK/PALEORCA2_demonstrator_forcings/PALEORCA2_namelist_clim_cfg namelist_cfg
ln -fs $WORK/PALEORCA2_demonstrator_forcings/PALEORCA2_file_def_nemo-oce.xml file_def_nemo-oce.xml
ln -fs $WORK/PALEORCA2_demonstrator_forcings/PALEORCA2_file_def_nemo-ice.xml file_def_nemo-ice.xml
ln -fs $WORK/PALEORCA2_demonstrator_forcings/PALEORCA2_namelist_top_cfg namelist_top_cfg
ln -fs $WORK/PALEORCA2_demonstrator_forcings/PALEORCA2_namelist_pisces_cfg namelist_pisces_cfg
ln -fs $WORK/PALEORCA2_demonstrator_forcings/PALEORCA2_namelist_trc_cfg namelist_trc_cfg
ln -fs $WORK/PALEORCA2_demonstrator_forcings/PALEORCA2_file_def_nemo-pisces.xml file_def_nemo-pisces.xml
```

Now add the modified boundary conditions (domain_cfg, internal wave-driven mixing, eddy viscosity, geothermal heating, ...), surface forcing from the atmosphere and initial conditions:
```
ln -s $WORK/PALEORCA2_demonstrator_forcings/DOMAIN_PALEORCA2/PALEORCA2_domain_cfg.nc domain_cfg.nc
ln -s $WORK/PALEORCA2_demonstrator_forcings/PALEORCA2_HF_atm_forcing.nc HF_atm_forcing.nc
ln -s $WORK/PALEORCA2_demonstrator_forcings/PALEORCA2_1D_atm_forcing.nc 1D_atm_forcing.nc
ln -s $WORK/PALEORCA2_demonstrator_forcings/PALEORCA2_1M_atm_forcing.nc 1M_atm_forcing.nc
ln -s $WORK/PALEORCA2_demonstrator_forcings/PALEORCA2_weights_LMD9695_paleorca2_bilin.nc weights_bilinear.nc
ln -s $WORK/PALEORCA2_demonstrator_forcings/PALEORCA2_weights_LMD9695_paleorca2_bicub.nc weights_bicubic.nc
ln -s $WORK/PALEORCA2_demonstrator_forcings/PALEORCA2_subbasins.nc subbasins.nc
ln -s $WORK/PALEORCA2_demonstrator_forcings/PALEORCA2_zdfiwm_forcing.nc zdfiwm_forcing.nc
ln -s $WORK/PALEORCA2_demonstrator_forcings/PALEORCA2_geothermal_heating.nc geothermal_heating.nc
ln -s $WORK/PALEORCA2_demonstrator_forcings/PALEORCA2_eddy_viscosity_3D.nc eddy_viscosity_3D.nc
ln -s $WORK/PALEORCA2_demonstrator_forcings/PALEORCA2_oce_forcing_and_istate.nc 1M_oce_forcing_and_istate.nc
ln -s $WORK/PALEORCA2_demonstrator_forcings/PALEORCA2_sali_ref_clim_monthly.nc sali_ref_clim_monthly.nc
```

Finally, link the namelist_trc_ref from the cfgs/SHARED folder because this is not done by default. This can be useful if you need to run PISCES or other passive tracers in NEMO. The configuration namelist for tracers is provided in the PALEORCA2_demonstrator_forcings folder.
```
ln -fs $WORK/your_NEMO_dirname/cfgs/SHARED/namelist_trc_ref namelist_trc_ref
```

Should you have the necessary forcing files for PISCES, simply add them to the EXPTEST folder as other forcings and set the ln_top flag to true to re-activate the biogeochemistry.

You now have everything to run the simulation.
Depending on your local HPC, building the main batch job may differ. Ask friends or look into the HPC documentation on how to do this.

### After the run

To be edited...
