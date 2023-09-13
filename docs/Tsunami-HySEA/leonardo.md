# **Running Tsunami HySEA on Leonardo**
These instructions describe how to set up a Spack environment on Leonardo to run Tsunami HySEA.    
A compiled version of T-HySEA 3.9.0MC is available on Leonardo in the shared directory `/leonardo_work/DTGEO_T1_2/T-HySEA-leonardo`.    



## **1. Create the Spack environment**
1. Create a directory called `envhysea` in your home directory (or choose any location you prefer) and copy the spack.yaml file from the T-HySEA-leonardo directory:
```
mkdir ~/envhysea
cp /leonardo_work/DTGEO_T1_2/T-HySEA-leonardo/spack.yaml ~/envhysea
```
2. Load spack module and activate the environment:
```
module load spack
spack env activate -p ~/envhysea/
```
3. Install the modules specified in spack.yaml inside the environment:
```
spack concretize
spack -d install
```
This might take a while. If everything worked fine, you should be able to see the installed modules with the command `spack find`.    

Now you have a spack environment that you can activate any time you want with the commands in step 2. You do not need to repeat step 3 unless you change something in the spack.yaml file.     

???+ info

    [Here](../spack-and-compss/cheat-sheet.md) you can find some useful Spack commands.   


## **2. Run HySEA**
Any time you want to run HySEA you need to activate the environment envhysea and load the netcdf-c module. 
```
module load spack
spack env activate -p ~/envhysea/
spack load netcdf-c
```
Then you can use the executables get_load_balancing in T-HySEA_3.9.0_MC/bin_lb and TsunamiHySEA in T-HySEA_3.9.0_MC/bin.
For example, if you have an input file called test-hysea.txt, you can do:
```
/leonardo_work/DTGEO_T1_2/T-HySEA-leonardo/T-HySEA_3.9.0_MC/bin_lb/get_load_balancing test-hysea.txt 1 1
echo test-hysea.txt > problems.txt
```
Copy the script `run_hysea_spack.sh` to the directory where you want to launch the job from, check if you want to change anything in that file, then submit the job to the queue:
```
cp /leonardo_work/DTGEO_T1_2/T-HySEA-leonardo/run_hysea_spack.sh </PATH-TO-WORK-DIR/>
sbatch run_hysea_spack.sh
```
   

## **If you want to have the whole repository locally**
If you prefer to have the whole repo in your home directory, then you can clone it with
```
git clone git@github.com:dtgeoeu-wp6-tsunamis/T-HySEA-leonardo.git
```
then follow the instructions above, starting from step 1.2 and using "T-HySEA-leonardo" instead of "envhysea".   