This tutorial allows you to run an Early Eocene (~ 55 Ma) NEMO simulation.
The bathymetry follows that provided by the model intercomparison project focussing on the Early Eocene ([**DeepMIP**](https://www.deepmip.org/deepmip-eocene/)). Details about the boundary conditions can be found [**here**](https://gmd.copernicus.org/articles/10/889/2017/) and the first results of this exercise are documented [**here**](https://cp.copernicus.org/articles/17/203/2021/cp-17-203-2021.html).
This training guide is based on a modified global ORCA2 grid with displaced poles to accommodate the different global land/sea mask. The nominal resolution is 2° with enhancement in the equatorial band up to 0.5° resolution at the equator).


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
scp PALEORCA2_demonstrator_forcings.tar.gz $path_to_your_HPC_directory
(after logging into your HPC)
gunzip PALEORCA2_demonstrator_forcings.tar.gz
tar -xvf PALEORCA2_demonstrator_forcings.tar
```

You should now have a folder PALEORCA2_demonstrator_forcings/ with all the necessary files to run the demonstrator.

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

### 2. Testing github


