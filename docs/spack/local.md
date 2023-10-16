# **Create a Spack environment for PTF on your local machine**
These instructions describe how to set up a Spack environment that includes py-pip on your local machine.

To know more about Spack:    
[Spack documentation](https://spack.readthedocs.io/en/latest/)

[Here](cheat-sheet.md) you can find some useful Spack commands.  

## **1. Download Spack and create an environment**
Before installing Spack, check the [system prerequisites](https://spack.readthedocs.io/en/latest/getting_started.html) that are needed to be present on your machine. Most of them are basic things that you likely already have (e.g., python>3.6, C/C++ compilers, make, tar, git,...), but still worth checking the full list.

1. Choose the directory on your machine where you want to download Spack (<FULLPATH-TO-SPACK-ROOT\>), then run the following commands: 
```
cd /<FULLPATH-TO-SPACK-ROOT>
git clone -c feature.manyFiles=true https://github.com/spack/spack.git
. spack/setup-env.sh
```   

To avoid running the same command every time you open a new terminal, you might want to add this line to your ~/.bashrc file:
```
. /<PATH-TO-SPACK-ROOT>/spack/share/spack/setup-env.sh
```

2. Create and activate the Spack environment:
```
spack env create -d spack_env_name
spack env activate -p spack_env_name
```
Note that the -p flag is simply to visualize that the environment is active.

3. Add Python and py-pip to the environment and install them:
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
If something failed, check our [troubleshooting guide](troubleshooting.md).    

4. Install Python packages needed to run the PTF workflow in the environment:

Download the file requirements.txt from this folder and type the following command
```
pip install -r requirements.txt
```

## **Problems?** 

Check our [troubleshooting guide](troubleshooting.md).
