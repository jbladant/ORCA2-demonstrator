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


### 3. Paleoclimate instructions

###### 3.1 Create the new DOMAIN

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

The configuration namelist provided is already populated with the appropriate information but in the following are reviewed the major changes that have been made.

<mark/>!---------------------------------------------  <mark/>  
<mark/>&namrun        !   parameters of the run  <mark/>  
<mark/>!---------------------------------------------  <mark/>  
<mark/>  cn_exp      =  "domaincfg"  !  experience name  <mark/>  

