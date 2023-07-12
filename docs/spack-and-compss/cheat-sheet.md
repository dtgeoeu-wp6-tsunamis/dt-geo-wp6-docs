# Cheat sheet 

## Useful COMPSs commands 

fill in more stuff here

## Useful spack commands

`spack find`           To see what packages are installed in the environment.   
`spack env deactivate`    To deactivate the environment.   
`spack list packagename`  To see available packages that contain "packagename" on spack.   
`spack add packagename`    To add a new package (then you need to do `spack concretize` and `spack install` again). You can also specify which version to use adding @x.x.x after the package name.   
`spack info packagename`  To see which versions of a specific package are available.   
`spack find --paths`   To see where packages are installed within the spack environment.         