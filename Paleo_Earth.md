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

- Put compiling instructions here? or in the main readme?

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
The same can be done on the mesh_mask.nc file.

### Run a PALEORCA2 simulation of the Early Eocene

You are now ready to run a NEMO simulation of the Early Eocene. 
Edit: for now, the tutorial does not include PISCES or TOP, for simplicity. It will be updated later.

Go the the PALEORCA2 folder that has been created as part of the compilation step:
```
cd $WORK/your_NEMO_dirname/cfgs/PALEORCA2
```

Copy the reference experiment EXP00 to your preferred experiment name (EXPTEST for the purpose of this tutorial) and cd there:
```
cp -r EXP00 EXPTEST
cd EXPTEST
```




<mark/>!---------------------------------------------  <mark/>  
<mark/>&namrun        !   parameters of the run  <mark/>  
<mark/>!---------------------------------------------  <mark/>  
<mark/>cn_exp      =  "domaincfg"  !  experience name  <mark/>  

<mark/>!---------------------------------------------  <mark/>  
<mark/>&namdom        !   space and time domain (bathymetry, mesh, timestep) <mark/>  
<mark/>!---------------------------------------------  <mark/>  
<mark/>nn_msh      =    1      !  create (=1) a mesh file or not (=0) <mark/>  

