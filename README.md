# Parflow Singularity Definition Files
A set of singularity definition files that allow for building Singularity containers for ParFlow with
either OMPI or MPICH mpi layers.

Each ParFlow container is built as a sci-app container, providing access to both sequential and parallel 
builds of ParFlow

## About Apps
- par = distributed build of ParFlow, -DPARFLOW_AMPS_SEQUENTIAL_IO=False
- seq = sequential build of ParFlow, -DPARFLOW_AMPS_SEQUENTIAL_IO=True

to run either:
```bash
$ singularity run --app <app_name> <destination/path/to/singularity_container.sif> <.tcl input file>
```
See more here:[https://sylabs.io/guides/3.3/user-guide/definition_files.html?highlight=apps#apps](https://sylabs.io/guides/3.3/user-guide/definition_files.html?highlight=apps#apps)
for additional information about Apps in Singularity.

## Prerequisits
- Host OS must have Singularity installed
- To build container from recipe file, user must have root access

## To Build Container
General build command is of the form:
```bash
$ sudo singularity build <destination/path/to/singularity_container.sif> <Singularity definition file>
```

as a specific example:
```bash
$ sudo singularity build ~/pf_singularity_ompi Singularity.parflow_ompi
```

## To Use Parflow in Container
example of running the LW test case in `parflow/test/washita/tcl_scripts` directory
```bash
$ singularity run --app par ~/pf_singularity_ompi LW_Test.tcl
```

## Pull from Singularity Hub

```bash
$ singularity pull shub://arezaii/pf_singularity:parflow_ompi
```
then to use it:
```bash
singularity run --app par pf_singularity_parflow_ompi.sif LW_Test.tcl
```


## Testing

Because singularity containers are immutable and ParFlow tests write to disk, you must expand the image to a writable sandbox.
Unfortunately this requires super user access to do...

First, create a writable sandbox from the immutable container using Singularity's build command:
```bash
sudo singularity build --sandbox <directory_to_create_for_sandbox/> <singularity_container>
```

as an example, if you had pulled the parflow_ompi image from shub:
```bash
sudo singularity build --sandbox pf_singularity_parflow_ompi_test/ pf_singularity_parflow_ompi.sif
```

There will now be a new directory pf_singularity_parflow_ompi_test/ that is the root of the container.
Editing any of the folder contents will require super user permissions.


You can enter a console into the container now by using the Singularity shell command:
```bash
sudo singularity shell --writable <directory_to_create_for_sandbox/>
```
