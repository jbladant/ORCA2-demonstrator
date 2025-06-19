


### 0. System prerequisites

- NetCDF package installed on your HPC
- An arch file specific to your HPC to specify XIOS and NetCDF paths as well as compiling options
- Python or your preferred analysis software used for visualizing the outputs

Note that the full path to your HPC work directory will be referred to as $WORK in this tutorial.

### 1. Files provided

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

### 2. Compiling instructions

###### 2.1 XIOS

Outputs in NEMO are managed via an external library called XIOS. It allows a lot of flexibility in the choice of the output variables and output frequencies (amongst other things). XIOS needs to be compiled prior to compiling NEMO. This tutorial uses XIOS-2.5.
Connect to your HPC and download XIOS in your work directory:
```
svn co https://forge.ipsl.fr/ioserver/svn/XIOS/trunk your_XIOS_dirname
```
Setup your arch files (one for the environment (.env), another for the compiler (.fcm) and a last one for the path (.path)). Examples can be found in your_XIOS_dirname/arch/ and should be edited according to your HPC environement.
Once this is done, XIOS is ready be be compiled (here with an arch file for the TGCC Irene supercomputer, as an example):
```
cd $WORK/your_XIOS_dirname
./make_xios --arch X64_IRENE --full --prod --job 8
```
cd back to your workdir (cd $WORK) when the compilation is finished.

###### 2.2 NEMO

NEMO now needs to be compiled before any simulation can be run.
Download the NEMO source code in your work directory:
```
git clone --branch 5.0.1 https://forge.nemo-ocean.eu/nemo/nemo.git your_NEMO_dirname
cd your_NEMO_dirname
```
You can explore the tree structure of the main NEMO directory:
=> Add details as in WED025

As for XIOS, NEMO requires arch files specific to your HPC. See [**NEMO user guide**](https://sites.nemo-ocean.io/user-guide/install.html#download-and-install-the-nemo-code) for instructions on setting up the arch files.

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


### 2. Testing github


