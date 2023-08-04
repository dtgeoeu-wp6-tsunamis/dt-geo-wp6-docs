# Cheat sheet 

## Useful COMPSs commands 

fill in more stuff here

## Useful spack commands
Below is a short list of some useful commands. For the complete list of Spack commands check this [link](https://spack.readthedocs.io/en/latest/command_index.html)      

| Command                        |      Comment      |
|--------------------------------|---------------------------------------|
| `spack env create -d .`        | To create a new environment in the current folder |
| `spack env activate -p .`      | To activate an environment in the current folder |
| `spack env deactivate`         | To deactivate the environment |
| `spack install`                | To install packages added to the environment |
| `spack find`                   | To see what packages are installed in the environment |
| `spack list packagename`       | To see available packages that contain "packagename" on spack |
| `spack add packagename`        | To add a new package (then do `spack concretize` and `spack install`) |
| `spack add packagename@x.x.x`  | To add a new package, where x.x.x defines a specific version  |
| `spack info packagename`       | To see which versions of a specific package are available |
| `spack find --paths`           | To see where packages are installed within the spack environment |
| `spack repo add /path/`        | To add a new repository (in addition to the default one from spack) |
      