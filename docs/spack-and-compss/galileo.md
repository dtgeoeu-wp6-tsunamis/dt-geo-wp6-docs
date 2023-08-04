# **Run a pyCOMPSs job in a Spack environment on Galileo**
These instructions describe how to set up a Spack environment that includes COMPSs and py-pip, and how to run a pyCOMPSs script on Galileo100.

???+ info
    To know more about Spack and COMPSs:    
    [Spack documentation](https://spack.readthedocs.io/en/latest/)   
    [COMPSs documentation](https://compss-doc.readthedocs.io/en/stable/)

## **1. Create the spack environment**
1. Load the spack module:
```
module load spack
```   

2. Clone our repository. The `git submodule` command is to pull a secondary git repo within our main one.   
```
git clone git@github.com:dtgeoeu-wp6-tsunamis/spack-compss.git
cd spack-compss
git submodule update --init --recursive
```

3. Create and activate the Spack environment .   
```
cd spackenv
spack env create -d .
spack env activate -p .
```
Note that the -p flag is simply to visualize that the environment is active.

4. The first time, you need to tell Spack where to find the compss package. This is done by adding a new spack repo.
```
spack repo add /<FULLPATH-TO-REPOSITORY-ROOT>/spack-compss/spack-dt-geo/var/spack/repos/builtin
```
5. Install the packages specified in the spack.yaml file:
```
spack concretize
spack -d install
```
Now you should have an environment with compss and py-pip installed. If something fails, check our [troubleshooting guide](troubleshooting.md).    

You can do `spack find` to see which packages are now installed in the environment.
To see if the installation of compss worked you can do `runcompss --version`, but the first time you do this you might need to deactivate the environment (`spack env deactivate`) and activate it again. If the command runcompss is not known, then something went wrong with the installation.   

Once you have this basic environment set up, you can add and install more packages with `spack add packagename`, then repeating step 5.

???+ info

    [Here](cheat-sheet.md) you can find some useful Spack commands.   

## **2. Installing Python packages in the environment (optional)**
You can use py-pip to install the python packages you need. Note that Spack already has many python packages available, but not all of them are included. Therefore, to be consistent, we have installed py-pip so that we can install the python packages with the pip install command. And because in the .yaml file we put the flag `concretization: together`, we are sure that COMPSs and pip will use the same python version.

With the **spack environment active**, you can install any python package with pip:
```
pip install name_of_python_package
```
Or, if you have a requirements.txt file with a list of packages to install, you can do:
```
pip install -r requirements.txt
```

If your python script needs to import local modules, then you need to add the directories where they can be found to the variable PYTHONPATH so that COMPSs knows where to look. For instance, if you have a folder /mycode/py/ with some routines that the main script needs to use, you need to set or update the PYTHONPATH to (make sure you write the full paths):
```
export PYTHONPATH=$PYTHONPATH:/mycode:/mycode/py
```

## **3. Configure COMPSs for Galileo**
Now we need to tell COMPSs which submission scripts to use that are specific for Galileo. This is defined by the two files g100.cfg and slurm.cfg, which we need to copy in the COMPSs folders of our Spack environment:
```
cp galileo/g100.cfg /<FULLPATH-TO-REPOSITORY-ROOT>/spack-compss/spackenv/.spack-env/view/compss/Runtime/scripts/queues/supercomputers/default.cfg   
cp galileo/slurm.cfg /<FULLPATH-TO-REPOSITORY-ROOT>/spack-compss/spackenv/.spack-env/view/compss/Runtime/scripts/queues/queue_systems/
```

## **4. Submit a job on Galileo with enqueue_compss**
   
The file `run_compss_galileo.sh` in the directory `galileo/` is an example of how to submit a job on Galileo with enqueue_compss. It is important to give the python_interpreter and pythonpath flags to make sure that compss uses the version of python inside the spack environment and that it knows the PYTHONPATH. 

1. Copy the example script to the directory you want to run the script from:
```
cp galileo/run_compss_galileo.sh /<PATH-TO-DIR-FOR-LAUNCHING-JOB>/run_compss.sh
```   
2. Open the file `run_compss.sh` and change the paths and name of the script to run.

3. Submit the job with the **spack environment active**:
```
cd /<PATH-TO-DIR-FOR-LAUNCHING-JOB>/
./run_compss.sh
```


## **Problems?** 

Check our [troubleshooting guide](troubleshooting.md).