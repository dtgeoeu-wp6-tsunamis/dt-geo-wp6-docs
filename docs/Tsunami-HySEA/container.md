# **Running Tsunami HySEA inside a singularity container on Leonardo**
These instructions describe how to run Tsunami HySEA simulations inside a singularity container on Leonardo.   

To run Tsunami HySEA inside a singularity container on Leonardo, you need:   
1. The singularity image of HySEA and   
2. A Spack environment with the same versions of openmpi and cuda used in the image   

## **1. Get the singularity image of HySEA**
The singularity image of HySEA was created with the eFlows4HPC image service creation and can downloaded from their [website](https://eflows4hpc.bsc.es/image_creation/home), if you have an account.   

Alternatively, the image can be found on Leonardo in the shared directory `/leonardo_work/DTGEO_T1_2/images` with the name `dt-geo_t-hysea_x86_64_openmpi_4.1.4_cuda_11.8_v_dt-geo.sif`.   

Note that this image was created with specific version of openmpi (4.1.4) and cuda (11.8) to match what is available on Leonardo. If you want to use different versions or use the image in a HPC cluster with a different architecture, then you need to create a new image.    

## **2. Create and activate the Spack environment**   
 Now we need to create a Spack environment with openmpi@4.1.4 and cuda@11.8. If you already have created a Spack environment to run HysEA on Leonardo outside a container, then you can simply activate that environment and move to the next point. If you don't have a Spack environment yet, you can either use an existing one, following the instructions in section 1 [here](leonardo.md) or create a new one.    
 
 To create a new Spack environment:   
 ```
 module load spack
 mkdir envhysea
 cd envhysea
 spack env create -d .
 spack env activate -p .
 spack add openmpi@4.1.4+cuda
 spack concretize
 spack -d install
 ```   
 This will install openmpi, cuda, and other packages that have dependencies with openmpi. You can see the installed modules with the command `spack find`. At the moment of writing, the version of cuda installed with this method is 11.8 (cuda@11.8.89 in the output of `spack find`). If the version is different, then you have to specify the version of cuda you want to use (11.8) when creating the spack environment to fit the one that is installed in the singularity image.  

Now you have a spack environment that you can activate any time you want with the command `spack env activate -p .`.      

???+ info

    [Here](../spack-and-compss/cheat-sheet.md) you can find some useful Spack commands.  

## **3. Run HySEA inside the singularity container**   
 **Spack environment**   
 Any time you want to run HySEA you need to activate the environment envhysea and load the openmpi module (which should automatically load also cuda).    
 ```
 module load spack
 spack env activate -p PATH-TO/envhysea
 spack load openmpi
 ```
 **Singularity**   
 To run HySEA inside a container we want to use SingularityPRO.   
 On Leonardo, both SingularityPRO and the normal version of Singularity are available. SingularityPRO is already installed in the system, so no action is needed to be able to use it, whereas the normal version of Singularity can be loaded as a module (`module load singularity`).   
 The benefit of using SingularityPRO instead of the normal Singularity is that SingularityPRO does not need a lot of memory to create the container from the image. Because the image we have of HySEA is quite large (>8GB), when using the normal version of Singularity, you would need to reserve an entire node when submitting the job, even though you might want to use only 1 GPU inside that node. Therefore, it is better, and faster, to use SingularityPRO.   
 If you type `singularity --version` you should see this output `SingularityPRO version 3.9-9.el8`.    

 **Copy test folder**   
 Copy the directory `/leonardo_work/DTGEO_T1_2/testruns_hysea` into your home directory. Here you can find input files for HySEA and shell scripts to run some tests. 

 **Set the right paths**   
 Open the file `runHySEA_singularity.sh` and write the full path to the testruns_hysea directory in this line:   
```
mpirun singularity exec --nv --no-home --bind <FULLPATH-TO-ROOT-OF>/testruns_hysea:/testruns_hysea /leonardo_work/DTGEO_T1_2/images/dt-geo_t-hysea_x86_64_openmpi_4.1.4_cuda_11.8_v_dt-geo.sif testruns_hysea/run-inside-container.sh
```     
 Do not change the path after the colon symbol (:/testruns_hysea) as this is the path used inside the container.   

 **Run 1 simulation**    
 The first example is to run one simulation inside the singularity container. The input files for this simulation are inside the directory `BS_Scenario0001`.   

 To run the simulation, make sure you stand in the directory where the file `runHySEA_singularity.sh` is and have the **spack environment active**, then submit the job to the queue:
 ```
 sbatch runHySEA_singularity.sh
 ```
 What this script does is launching the command `mpirun singularity exec ... run-inside-container.sh`. This command executes the script run-inside-container.sh inside the container. This is where the commands `get_load_balancing` to create the grid and `TsunamiHySEA` are called.         
 The `--nv` flag is necessary because we want to use GPUs. The `--bind` flag is to mount the folder needed to run the simulation inside the container.    

 When the job is done, you should be able to see a file called `out_ts.nc` inside `BS_Scenario0001`.    

 **Run 4 simulations**    
 To run more than one simulation we use the files inside `Step2_BS`. Here there are 4 directories, each of them contains all the input files needed by HySEA. Note that in this example the grid created by `get_load_balancing` is already there.   
 Open the the file `runHySEA_singularity.sh` and follow the instructions at the bottom of the file to ask for 4 GPUs instead of one and to use the right `mpirun singularity exec ...` call that mounts the Step2_BS directory.   

 Then submit the job to the queue:
 ```
 sbatch runHySEA_singularity.sh
 ``` 

 When the job is done, you should be able to see a file called `out_ts.nc` inside each subdirectory in `Step2_BS`.

