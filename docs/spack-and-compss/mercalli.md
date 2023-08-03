# **Run a pyCOMPSs job in a Spack environment on Mercalli**
Follow these instructions if you want to create a Spack environment with COMPSs on Mercalli.

???+ warning
    STILL WORK IN PROGRESS: no configuration files and job submission script yet!

## **1. Download Spack and create an environment**

1. Choose the directory on your machine where you want to download Spack (<FULLPATH-TO-SPACK-ROOT\>), then run the following commands: 
```
cd /<FULLPATH-TO-SPACK-ROOT>
git clone --depth=100 --branch=releases/v0.17 https://github.com/spack/spack.git
. /<FULLPATH-TO-SPACK-ROOT>/spack/setup-env.sh
```   
Note that I use version 17.0 of Spack because it is the same version used on Galileo, but this can change. If you download a different version of Spack, some changes might be needed in the COMPSs and .yaml files.   

    ???+ tip
        To avoid running the same command every time you open a new terminal, you might want to add this line to your ~/.bashrc file:
        ```
        . /<PATH-TO-SPACK-ROOT>/spack/share/spack/setup-env.sh
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
cd spackenv
spack env create -d .
spack env activate -p .
```
Note that the -p flag is simply to visualize that the environment is active.

5. Install the packages specified in the spack.yaml file:
```
spack concretize
spack -d install
```
Now you should have an environment with compss and py-pip installed.    

You can do `spack find` to see which packages are now installed in the environment.
To see if the installation of compss worked you can do `runcompss --version`, but the first time you do this you might need to deactivate the environment (`spack env deactivate`) and activate it again. If the command runcompss is not known, then something went wrong with the installation. 

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
WORK IN PROGRESS   

## **4. Submit a job on Mercalli with enqueue_compss**
WORK IN PROGRESS      
The file `run_compss_mercalli.sh` in the directory `mercalli/` is an example of how to submit a job on Mercalli with enqueue_compss. It is important to give the python_interpreter and pythonpath flags to make sure that compss uses the version of python inside the spack environment and that it knows the PYTHONPATH. 

1. Copy the example script to the directory you want to run the script from:
```
cp mercalli/run_compss_mercalli.sh /<PATH-TO-DIR-FOR-LAUNCHING-JOB>/run_compss.sh
```   
2. Open the file `run_compss.sh` and change the paths and name of the script to run.

3. Submit the job with the **spack environment active**:
```
cd /<PATH-TO-DIR-FOR-LAUNCHING-JOB>/
./run_compss.sh
```


## **Problems?** 

Check our [troubleshooting guide](troubleshooting.md).