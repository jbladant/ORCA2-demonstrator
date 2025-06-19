# Contents
- [1. System prerequisites](#prerequisites)
- [2. Input files](#inputs)
- [3. Compiling XIOS and NEMO](#xiosnemo)
- [4. Running NEMO](#runnemo)
- [5. Look at outputs](#outputs)

# <a name="prerequisites"></a> 1. System prerequisites

- NetCDF package installed on your HPC
- An arch file specific to your HPC to specify XIOS and NetCDF paths as well as compiling options
- Python or your preferred analysis software used for visualizing the outputs

Note that the full path to your HPC work directory will be referred to as `$WORK` in this tutorial.

Other info here: "Glossary", relevant links, ...?


basic info..? 
make changes: keys, what else
collect files 
run nemo 


View outputs... 


____

# 2. <a name="inputs"></a> Input files
The input files for the ORCA2_ICE_PISCES configuration are provided through JASMIN [on this webpage](https://gws-access.jasmin.ac.uk/public/nemo/sette_inputs/). Download `ORCA2_ICE_v5.0.0.tar.gz` and `ORCA2_INPUTS_PISCES_v5.0.0.tar.gz`. 

These files need to be on the HPC where NEMO is run. There are multiple ways to achieve this: 
### Command line 
On the HPC, cd to `$WORK` and run
```
wget "https://gws-access.jasmin.ac.uk/public/nemo/sette_inputs/r5.0.0/ORCA2_ICE_v5.0.0.tar.gz"
wget "https://gws-access.jasmin.ac.uk/public/nemo/sette_inputs/extras/ORCA2_INPUTS_PISCES_v5.0.0.tar.gz"
``` 
Unzip the folder using 
```
tar -xf ORCA2_ICE_v5.0.0.tar.gz
tar -xf ORCA2_INPUTS_PISCES_v5.0.0.tar.gz
```
### Via your personal computer 
Download `ORCA2_ICE_v5.0.0.tar.gz` and `ORCA2_INPUTS_PISCES_v5.0.0.tar.gz` from [this webpage](https://gws-access.jasmin.ac.uk/public/nemo/sette_inputs/). 
Move the files to the HPC and into `$WORK`
```
scp ...
```
Unzip the folder using 
```
tar -xf ORCA2_ICE_v5.0.0.tar.gz
tar -xf ORCA2_INPUTS_PISCES_v5.0.0.tar.gz
```

___

# <a name="xiosnemo"></a> 3. Compiling XIOS and NEMO
##  <a name="XIOScomp"></a>  3.1 XIOS
NEMO uses the external library XIOS for creating outputs. It allows a lot of flexibility in the choice of the output variables and output frequencies (amongst other things). XIOS needs to be compiled prior to compiling NEMO. This tutorial uses <mark>XIOS-2.5</mark>.

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
cd back to `$WORK` when the compilation is finished.


## <a name="NEMOcomp"></a>3.2 NEMO 
Now you download and compile NEMO. In this tutorial we use [NEMO <mark>v5.0.1</mark>](https://forge.nemo-ocean.eu/nemo/nemo/-/releases/5.0.1)

Download the NEMO source code in `$WORK`:
```
git clone --branch 5.0.1 https://forge.nemo-ocean.eu/nemo/nemo.git your_NEMO_dir
cd your_NEMO_dirname
```
For an explanation of the folder structure in `your_NEMO_dirname`, see [this section](https://sites.nemo-ocean.io/user-guide/install.html#description-of-main-nemo-directories) of the NEMO user guide.

As for XIOS, NEMO requires arch files specific to your HPC. See [NEMO user guide](https://sites.nemo-ocean.io/user-guide/install.html#download-and-install-the-nemo-code) for instructions on setting up the arch files. <mark> Equivalent for Irene</mark>

In `$WORK/your_NEMO_dir`, run the following:

```
./makenemo -r ORCA2_ICE_PISCES -n your_config_name -m X64_IRENE
```
`-r` defines the reference configuration found in `your_NEMO_dir/cfgs/`. `-n` is the name you want to give your configuration. `-m` defines the machine architecture, i.e. the file in `arch/` specific to your HPC, which in this example is the Irene HPC again.

**Note** You may have to `module load` additional modules for the compilation to succeed, but this is dependent on your HPC. 

If the compilation of NEMO was successful, there will be a new directory `your_NEMO_dir/cfgs/your_config_name/`. Inside this directory in `BLD/bin/`, there will be a file named `nemo.exe` which is the executable to run your NEMO configuration. 

___ 
# <a name="runnemo"></a> 4. Running ORCA2_ICE_PISCES

To run the NEMO executable, cd into `cfgs/your_config_name/EXP00`. In this directory, you need to collect all the files that are required for running. You can copy `cp` or link `ln -s` the files. 
* the NEMO executable `nemo.exe` from [compiling NEMO](#NEMOcomp) 
* the XIOS executable `xios.exe` from [compiling XIOS](#XIOScomp)
* all [input files](#inputs)

**Tip** You can copy the original `EXP00` from the compilation (e.g. run `cp -r EXP00 EXPREF`) and keep it as a backup or for further experiments. 

You run your NEMO configuration by submitting a job to your HPC. The precise procedure will dependent on your HPC. It most likely will require creating a bash script containing information such as
``` 
#!/bin/bash
#
#PBS -N your_config_name
#PBS -q normal
#PBS -l walltime=00:30:00
#
#PBS -e nemo_err.out 
cd $WORK/your_NEMO/dir/cfgs/your_config_name/EXP00
mpiexec  --np 64 --ppn 64 ./nemo.exe : --np 8 --ppn 8 ./xios.exe
```
and submitting it by running
```
qsub zour_bash_script.sh
```
You may have to play around with number of processors and walltime so that you ask for reasonable resources and so that NEMO the run does not time out. 

**Tip** If you want to monitor the progress of your run,  
```
cat time.step
```
will show the current time step of the iteration. The total number of iterations is defined in `namelist_cfg` as `nn_itend`. 
The submission to the HPC can be monitored using `qstat` or equivalent commands depending on the way you submit jobs to your HPC. 

If NEMO runs successfully, there will be a number of output files in netCDF format in `EXP00`, such as `ORCA2_5d_00010101_00021231_grid_T.nc`.

**Tip** Once NEMO has finished, have a look at `ocean.output`to see what

___
# <a name="outputs"></a> 5. View outputs
