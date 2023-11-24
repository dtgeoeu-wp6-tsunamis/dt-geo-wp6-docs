# **Troubleshooting - Running HySEA**

If you have problems with Spack, see this [troubleshooting page](../spack-and-compss/troubleshooting.md)

### **Job fails - Error when converting SIF file to temporary sandbox**
You might get an error while converting the HySEA image to temporary sandbox similar to this:
```
INFO:    Converting SIF file to temporary sandbox...
FATAL:   while extracting /PATH/images_singularity/dt-geo_t-hysea_x86_64_openmpi_4.1.4_cuda_11.8_v_dt-geo.sif: root filesystem extraction failed: extract command failed: WARNING: While bind mounting '/sbin/unsquashfs:/sbin/unsquashfs': destination is already in the mount point list
WARNING: passwd file doesn't exist in container, not updating
...
Parallel unsquashfs: Using 32 processors
169807 inodes (311475 blocks) to write

^M[=========\                                                ]  53000/311475  17%^M[===============|                                          ]  83200/311475  26%: signal: killed
slurmstepd: error: Detected 1 oom-kill event(s) in StepId=2003702.batch. Some of your processes may have been killed by the cgroup out-of-memory handler.
```

This means that there is not enough memory for singularity to create the container. It also means that you are using the normal version of singularity instead of SingularityPRO.     
There are two solutions (solution 2 is recommended):   
1. Keep using the normal version of singularity, but add in your .sh script to submit the job the flag:
```
#SBATCH --exclusive
```
2. Use SingularityPRO. To do that, check that you do not have the normal version of singularity loaded as a module (see output of `module list` and, if needed, do `module unload singularity`). Now you should be able to use the default version of singularity installed in the system, which is SingulairtyPRO. 

### **Job fails - The value of the MCA parameter "plm_rsh_agent" was set to a path that could not be found**
```
INFO:    Converting SIF file to temporary sandbox...
--------------------------------------------------------------------------
The value of the MCA parameter "plm_rsh_agent" was set to a path
that could not be found:

  plm_rsh_agent: ssh : rsh

Please either unset the parameter, or check that the path is correct
--------------------------------------------------------------------------
[lrdn3422.leonardo.local:650123] [[INVALID],INVALID] FORCE-TERMINATE AT Not found:-13 - error plm_rsh_component.c(335)
INFO:    Cleaning up image...
```
This is likely due to the fact that the spack environment and some environment variables are not automatically activated when the container starts.    
To solve this issue, make sure you have this line as the first thing you do in the file with the list of commands to execute inside the container (e.g., run-inside-container.sh):
```
source /etc/profile.d/z10_spack_environment.sh
```