# **Troubleshooting**

### **COMPSs installation fails**
This can be something related to the sha256. Check the log of the spack install command. If you ran it with the -d flag, you should be able to see an error that says that the sha256 has changed and it says which sha256 you should use now. If this is the case, you can copy that long combination of numbers and letters and paste it inside the file `/<FULLPATH-TO-SPACK-COMPSS-REPO>/var/spack/repos/builtin/packages/compss/package.py`. Then try `spack install` again.

### **Module not found when running a python script with COMPSs**
This could be related to pyCOMPSs not knowing where to look for the python routines defined bynthe user. Make sure you set up the variable $PYTHONPATH correctly. For instance, if you have a folder /mycode/py/ with some routines that the main script needs to use, you need to change the PYTHONPATH to (make sure you write the full paths):
```
export PYTHONPATH=$PYTHONPATH:/mycode:/mycode/py
```
If you are running a job on a cluster with the command `enqueue_compss`,  make sure you specify the PYTHONPATH with the flag --pythonpath
  
### **Problem with geos (cannot find libraries)**
  This error is specific for running the PTF. Even though geos is installed as a package in the spack environment, in some cases, it seems that python does not see it. Try `spack load geos` and run the PTF again.   

### **Job submission on Galileo fails**
If the job submission fails and you get this error:   
`/.../spackptf/.spack-env/view/compss/Runtime/scripts/queues/commons/submit.sh: line 19: bsub: command not found
cat: /scratch_local/tmp.VJR55zlrin.err: No such file or directory   
Error submiting script to queue system`   
This is related to COMPSs using the wrong commands to submit a job to the queue in Galielo. Make sure you have copied the right configuration files specific for Galileo in the spack environment COMPSs folder, as described [here](galileo.md).    

### **Installation of Spack environment fails - Problem with compilers**
If the installation of a spack environment fails, it can be because spack is using a version of the C compilers that have only gcc, but not g++. The error message would read something like *"Checking whether the C++ compiler supports templates ... configure: error: no"*.   
Open the spack.yaml file of the environment where the installation failed. There should be a section that Spack created called "compilers". Check what version of gcc is listed. If there is no path specified for some of the compilers (e.g., cxx), then you need to use a different version of gcc.

In this case you can force spack to use the version of the compiler that has both gcc and g++. You can check what compilers you have available with the command
```
spack compiler find
```
The output is saved in a file called compilers.yaml (the path of this file is printed out by spack when you use the command above). Check the list of compilers. There should be at least one version of gcc that has paths for both gcc (cc) and g++ (cxx) compilers. Copy the entire section of the gcc compiler that has all the paths specified and paste it in the spack.yaml file (replacing what is already there). If your spack.yaml file did not have any compiler section, then just paste it at the bottom of the file. The spack.yaml file should look something like this:   
```
# This is a Spack Environment file.
# It describes a set of packages to be installed, along with
# configuration settings.
spack:
  # add package specs to the `specs` list
  specs: [geos, py-pip, compss]
  view: true
  concretization: together
  compilers:
  - compiler:
      paths:
        cc: /usr/bin/gcc
        cxx: /usr/bin/g++
        f77: /usr/bin/gfortran
        fc: /usr/bin/gfortran
      operating_system: centos7
      target: x86_64
      modules: []
      environment: {}
      extra_rpaths: []
      flags: {}
      spec: gcc@4.8.5
```

### **Updating the version of COMPSs**
If a new version of COMPSs is released (note that the tag of the version might stay the same, e.g., 3.2, but the sha256 changes) and you want to update it in your spack environment, you need to follow these steps:   

- Inside your spack environemnt, uninstall compss   
```
spack uninstall compss
```   

- Clean the cache. This is needed otherwise spack will not fetch a new version of COMPSs because it sees that it has a version with the same tag (e.g., 3.2) in the cache   
```
spack clean -m   
```

- If you alread know the new sha256, then update it in the package.py file as exaplined [here](#compss-installation-fails). If not, you should still follow the next steps, from which you will get an error message during the spack install that will give you the new sha256.   
- Re-install compss and update the environment   
```
spack add compss   
spack concretize   
spack -d install
```

It might be necessary to deactivate the environment and activate it again, before using it.   
If you had some python pacakages installed with py-pip, it might also be necessary to re-install them. You can check with `pip list` if they are sill there or not.   
If you are using Spack+COMPSs on a supercomputer, now that a new version of COMPSs is installed, the configuration files for running jobs with COMPSs are back to being the default ones and not anymore the ones specific for that supercomputer (e.g. Galileo). Therefore, you need to copy the configuration files again following the instructions [here](galileo.md)