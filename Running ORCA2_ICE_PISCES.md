Blurp? NEMO v5.0.1

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
compile nemo ./makenemo 
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
## 3.1 XIOS
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


## 3.2 NEMO
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
`-r` defines the reference configuration found in `your_NEMO_dir/cfgs/. `-n` is the name you want to give your configuration. `-m` defines the machine architecture, i.e. the file in `arch/` specific to your HPC, which in this example is the Irene HPC again.

**Note** You may have to `module load` additional modules for the compilation to succeed, but this is dependent on your HPC. 

___ 
# <a name="runnemo"></a> 4. Running ORCA2_ICE_PISCES

If the compilation of NEMO was successful, there will be 

Collect all files
Run
Tips: monitor (qstat, time.step, ocean.output, e/o files if not running at all)

___
# <a name="outputs"></a> 5. View outputs
