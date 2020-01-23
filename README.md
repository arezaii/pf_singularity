# Parflow Singularity Definition File
Get Parflow built in a singularity container for distribution

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
$ singularity run ~/pf_singularity_ompi LW_Test.tcl
```

## Pull from Singularity Hub

```bash
$ singularity pull shub://arezaii/pf_singularity:parflow_ompi
```
then to use it:
```bash
singularity run pf_singularity_parflow_ompi.sif LW_Test.tcl
```
