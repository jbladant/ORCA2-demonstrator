This tutorial allows you to run an Early Eocene (~ 55 Ma) NEMO simulation.
The bathymetry follows that provided by the model intercomparison project focussing on the Early Eocene ([**DeepMIP**](https://www.deepmip.org/deepmip-eocene/)). Details about the boundary conditions can be found [**here**](https://gmd.copernicus.org/articles/10/889/2017/) and the first results of this exercise are documented [**here**](https://cp.copernicus.org/articles/17/203/2021/cp-17-203-2021.html).
This training guide is based on a modified global ORCA2 grid with displaced poles to accommodate the different global land/sea mask. The nominal resolution is 2° with enhancement in the equatorial band up to 0.5° resolution at the equator).


### 0. System prerequisites

- NetCDF package installed on your HPC
- An arch file specific to your HPC to specify XIOS and NetCDF paths as well as compiling options
- Python or your preferred analysis software used for visualizing the outputs

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


