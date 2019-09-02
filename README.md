# Parflow Singularity Definition File
Get Parflow built in a singularity container for distribution

## Prerequisits
- Host OS must have OPENMPI installed
- Host OS must have Singularity installed
- To build container from recipe file, user must have root access

## To Build Container
```bash
$ sudo singularity build /tmp/parflow.sif parflow_singularity
```

## To Use Parflow in Container
example of running the LW test case in `parflow/test/washita/tcl_scripts` directory
```bash
$ singularity run /tmp/parflow.sif LW_Test.tcl
```

## Pull from Singularity Hub

```bash
$ singularity pull shub://arezaii/pf_singularity
```
then to use it:
```bash
singularity run pf_singularity_latest.sif LW_Test.tcl
```
