# **Run a pyCOMPSs job in a Spack environment on Mercalli**
These instructions describe how to set up a Spack environment that includes COMPSs and py-pip, and how to run a pyCOMPSs script on Mercalli.

???+ warning
    WORK IN PROGRESS: it works for queues with cpus, but not for the queue with gpus.

???+ info
    To know more about Spack and COMPSs:    
    [Spack documentation](https://spack.readthedocs.io/en/latest/)   
    [COMPSs documentation](https://compss-doc.readthedocs.io/en/stable/)

## **1. Download Spack and create an environment**

1. Choose the directory on your machine where you want to download Spack (<FULLPATH-TO-SPACK-ROOT\>), then run the following commands: 
```
cd /<FULLPATH-TO-SPACK-ROOT>
git clone --depth=100 --branch=releases/v0.17 https://github.com/spack/spack.git
. /<FULLPATH-TO-SPACK-ROOT>/spack/share/spack/setup-env.sh
```   
Note that I use version 17.0 of Spack because it is the same version used on Galileo, but this can change. If you download a different version of Spack, some changes might be needed in the COMPSs and .yaml files.   

    ???+ tip
        To avoid running the same command every time you open a new terminal, you might want to add this line to your ~/.bashrc file:
        ```
        . /<FULLPATH-TO-SPACK-ROOT>/spack/share/spack/setup-env.sh
        ```

2. Clone our repository. The `git submodule` command is to pull a secondary git repo within our main one.
```
cd /<FULLPATH-TO-REPOSITORY-ROOT>
git clone git@github.com:dtgeoeu-wp6-tsunamis/spack-compss.git
cd spack-compss
git submodule update --init --recursive
```
3. Copy the `compss` directory in spack-dt-geo to your local spack directory:
```
cp -r spack-dt-geo/var/spack/repos/builtin/packages/compss /<PATH-TO-SPACK-ROOT>/spack/var/spack/repos/builtin/packages
```

4. Create and activate the Spack environment:
```
cd mercalli/spackenv
spack env create -d .
spack env activate -p .
```
Note that the -p flag is simply to visualize that the environment is active.

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

## **3. Configure COMPSs for Mercalli**
Now we need to tell COMPSs which submission scripts to use that are specific for Mercalli. This is defined by the two files mercalli_cpu.cfg and pbs_mercalli.cfg, which we need to copy in the COMPSs folders of our Spack environment:
```
cp mercalli/mercalli_cpu.cfg /<FULLPATH-TO-REPOSITORY-ROOT>/spack-compss/mercalli/spackenv/.spack-env/view/compss/Runtime/scripts/queues/supercomputers/   
cp mercalli/pbs_mercalli.cfg /<FULLPATH-TO-REPOSITORY-ROOT>/spack-compss/mercalli/spackenv/.spack-env/view/compss/Runtime/scripts/queues/queue_systems/
```   
???+ warning
    At the moment, this works only if you run the job in the queues on Mercalli with CPUs (not for the GPUs queue).

## **4. Submit a job on Mercalli with enqueue_compss**
WORK IN PROGRESS FOR GPU QUEUE   

The file `run_compss_mercalli_cpu.sh` in the directory `mercalli/` is an example of how to submit a job on Mercalli with enqueue_compss.        
The file `myenv.sh` is used by compss to set the right environment variables in the computing nodes. In particular, JAVA_HOME needs to be specified there. The path to this file is then passed as a flag to enqueue_compss when we submit the job to the queue.   

1. Copy the example scripts to the directory you want to run the script from:
```
cp mercalli/run_compss_mercalli_cpu.sh /<PATH-TO-DIR-FOR-LAUNCHING-JOB>/run_compss.sh
cp mercalli/myenv.sh /<PATH-TO-DIR-FOR-LAUNCHING-JOB>/
```   
2. Open the file `run_compss.sh` and change the paths (where you find 'fullpath') and name of the script to run. Some of the flags here are optional, but they might be useful. However, it is important to give the python_interpreter and pythonpath flags to make sure that compss uses the version of python inside the spack environment and that it knows the PYTHONPATH. And it is important to use the flag `--env_script` give the path to `myenv.sh`.      

3. Open the file `myenv.sh` and set the right paths, especially for the variable JAVA_HOME.    

4. Submit the job with the **spack environment active**:
```
cd /<PATH-TO-DIR-FOR-LAUNCHING-JOB>/
./run_compss.sh
```


## **Problems?** 

Check our [troubleshooting guide](troubleshooting.md).