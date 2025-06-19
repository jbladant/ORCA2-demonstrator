## 1. System prerequisites

- NetCDF package installed on your HPC
- An arch file specific to your HPC to specify XIOS and NetCDF paths as well as compiling options
- Python or your preferred analysis software used for visualizing the outputs

Note that the full path to your HPC work directory will be referred to as `$WORK` in this tutorial.

Input files: download to PC, or through command line, desription on the page
compile xios
git clone nemo
basic info..? 
make changes: keys, what else
compile nemo ./makenemo 
collect files 
run nemo 
Tips: monitor (qstat, time.step, ocean.output, e/o files if not running at all)
View outputs... 


____

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


