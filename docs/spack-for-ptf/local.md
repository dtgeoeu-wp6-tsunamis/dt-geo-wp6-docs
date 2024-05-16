# **Create a Spack environment to run the PTF on your local machine**
These instructions describe how to set up a Spack environment that includes py-pip on your local machine.

???+ info
    To know more about Spack: [Spack documentation](https://spack.readthedocs.io/en/latest/)   
    [Here](../spack-and-compss/cheat-sheet.md) you can find some useful Spack commands.  

## **1. Download Spack and create an environment**
Before installing Spack, check the [system prerequisites](https://spack.readthedocs.io/en/latest/getting_started.html) that are needed to be present on your machine. Most of them are basic things that you likely already have (e.g., python>3.6, C/C++ compilers, make, tar, git,...), but still worth checking the full list.

1. Choose the directory on your machine where you want to download Spack (<FULLPATH-TO-SPACK-ROOT\>), then run the following commands: 
```
cd /<FULLPATH-TO-SPACK-ROOT>
git clone -c feature.manyFiles=true https://github.com/spack/spack.git
. spack/share/spack/setup-env.sh
```    
To avoid running the same command every time you open a new terminal, you might want to add this line to your ~/.bashrc file:
```
. /<FULLPATH-TO-SPACK-ROOT>/spack/share/spack/setup-env.sh
```   
2. Create and activate the Spack environment:
```
spack env create -d spack_env_name
spack env activate -p spack_env_name
```
Note that the -p flag is simply to visualize that the environment is active.

## **2. Add Python and py-pip to the environment and install them**
```
spack add python
spack add py-pip
spack -d install
```
In this way, the (last) preferred version of Python will be installed. In case you want to install a specific version, you can use the command
```
spack info python
```
and choose the version
```
spack add pyhton@version
```
Now you should have an environment with py-pip installed. You can do `spack find` to see which packages have been installed.
If something failed, check our [troubleshooting guide](../spack-and-compss/troubleshooting.md).    

## **3. Install Python packages needed to run the PTF workflow in the environment**

Download the file [requirements.txt](requirements.txt){:download} and type the following command
```
pip install -r requirements.txt
```

A requirements file with specific versions of each python package installed with python 3.9.15 can be downloaded [here](requirements_versions.txt){:download}.   

Note that to install the python pacakge cartopy, GEOS needs to be installed as well. This is not a python package and it is possible that it is already installed locally. If so, then you do not need to do anything and the installation of cartopy should work. If, on the other hand, GEOS is not installed, then the installation of cartopy will fail. In this case, you can add GEOS as a spack package: ```spack add geos```, then ```spack install``` 

## **Problems?** 

Check our [troubleshooting guide](../spack-and-compss/troubleshooting.md).
