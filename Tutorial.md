This tutorial allows you to run an Early Eocene (~ 55 Ma) NEMO simulation.
The bathymetry follows that provided by the model intercomparison project focussing on the Early Eocene ([**DeepMIP**](https://www.deepmip.org/deepmip-eocene/)). Details about the boundary conditions can be found [**here**](https://gmd.copernicus.org/articles/10/889/2017/) and the first results of this exercise are documented [**here**](https://cp.copernicus.org/articles/17/203/2021/cp-17-203-2021.html).
This training guide is based on a modified global ORCA2 grid with displaced poles to accommodate the different global land/sea mask. The nominal resolution is 2° with enhancement in the equatorial band up to 0.5° resolution at the equator).


### 0. System prerequisites

- NetCDF package installed on your HPC
- An arch file 
Icepack will be installed through Git, therefore it is important that you have a running GitHub account and that you know its basic functionalities. If not, please follow [**this Git workflow guide**](https://github.com/CICE-Consortium/About-Us/wiki/Git-Workflow-Guide) provided by the CICE consortium. Large amount of information is provided on how to interact with the main code of Icepack; some of it is useful in the context of this project, other goes beyond the scope of this tutorial. I recommend reading them to familiarize yourself with the basic terminology, and to appreciate the complexity of running and maintaining a large geophysical model. The workflow guide is oriented toward setting up CICE rather than Icepack, but the same workflow applies to Icepack standalone.

You also need conda. If you are lucky, conda is already installed in your system, either through Anaconda or Miniconda. Miniconda is a minimal installation of Anaconda, which will be enough for the scope of our project. Check if you have conda installed in your system by typing "conda".
If conda is not installed, and you are on a cluster or HPC system try module load anaconda or module avail to see if there is a conda installation available for you. If conda is still not installed, you can easily install miniconda on your platform of choice (Windows, Linux, Mac) by following [**this guide**](https://cice-consortium-icepack.readthedocs.io/en/main/user_guide/ug_running.html#porting-to-laptop-or-personal-computers). You might need to restart your shell after installing conda.


### The first steps with Icepack: creating a case stuy

- Clone the github directory from **https://github.com/CICE-Consortium/Icepack**, check step 0.
- Enter the Icepack directory, create and activate your conda environment from the .yml file provided in ~/configuration/scripts/machines/environment.yml by using the command
```
conda env create -f environment.yml
conda activate icepack
```
- If you work on linux cluster, create a case of study by running
```
./icepack.setup --case test0 --mach conda --env linux
```
  If you work on a different machine, please check the environment that suits it better in ~/configuration/scripts/machines/.
- A folder with the name of your test (test0 in this example) is created in the Icepack folder. The environmental variables are included in icepack.setting and icepack_in contains the Icepack namelist. In icepack.settings, define the folder where Icepack runs by using the variable **ICE_RUNDIR**, you will check the results of simulations there.

