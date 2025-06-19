Blurp? NEMO v5.0.1


# 1. System prerequisites

- NetCDF package installed on your HPC
- An arch file specific to your HPC to specify XIOS and NetCDF paths as well as compiling options
- Python or your preferred analysis software used for visualizing the outputs

Note that the full path to your HPC work directory will be referred to as `$WORK` in this tutorial.

Other info here: "Glossary", relevant links, ...?
TOC

basic info..? 
make changes: keys, what else
compile nemo ./makenemo 
collect files 
run nemo 

Tips: monitor (qstat, time.step, ocean.output, e/o files if not running at all)
View outputs... 


____

# 2. Input files
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

# 3. Compiling XIOS and NEMO
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
Now you download and compile NEMO. 

Download the NEMO source code in `$WORK`:
```
git clone --branch 5.0.1 https://forge.nemo-ocean.eu/nemo/nemo.git your_NEMO_dirname
cd your_NEMO_dirname
```
For an explanation of the folder structure in `your_NEMO_dirname`, see [this section](https://sites.nemo-ocean.io/user-guide/install.html#description-of-main-nemo-directories) of the NEMO user guide.

As for XIOS, NEMO requires arch files specific to your HPC. See [NEMO user guide](https://sites.nemo-ocean.io/user-guide/install.html#download-and-install-the-nemo-code) for instructions on setting up the arch files. 




___ 
# Running ORCA2_ICE_PISCES

Collect all files

