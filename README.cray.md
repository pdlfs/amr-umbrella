**This guide is written for our LANL collaborators that are kind enough to experiment with deltafs on their Cray systems.**

amr-umbrella
================
Download, build, and install amr and its dependencies in a single highly-automated step.

INSTALLATION
============
This guide is assuming a Linux Cray.

## STEP-1: prepare cray programming env

First, let's set cray link type to dynamic (required to compile amr)
```bash
export CRAYPE_LINK_TYPE="dynamic"
```
If `CRAYOS_VERSION` is not in the env, we have to explicitly set it.
On Nersc Edison, `CRAYOS_VERSION` is pre-set by the Cray system. On Nersc Cori, which has a newer version of Cray, it is not set.
```bash
export CRAYOS_VERSION=6
```
Make sure the desired processor-targeting module (such as `craype-sandybridge`, or `craype-haswell`, or `craype-mic-knl`, etc.) has been loaded. These targeting modules will configure the compiler driver scripts (`cc`, `CC`, `ftn`) to compile code optimized for the processors on the compute nodes.
```bash
module load craype-haswell  # Or module load craype-sandybridge if you want to run code on monitor nodes
```
Also make sure the desired compiler bundle (`PrgEnv-*` such as Intel, GNU, or Cray) has been configured, such as
```bash
module load PrgEnv-intel  # Or module load PrgEnv-gnu
```
Now, load a addition modules needed by amr umbrella.
```bash
module load cmake  # at least v3.x
```

## STEP-2: build amr suite

Assuming `$INSTALL` is a global file system location that is accessible from all compute, monitor, and head nodes, our plan is to build amr under `$HOME/amr/src`, and to install everything under `$INSTALL/amr`.

**NOTE**: after installation, the build dir `$HOME/amr/src` is no longer needed and can be safely discarded. `$INSTALL/amr` is going to be the only thing we need for running amr experiments.

**NOTE**: do not rename the install dir after installation is done. If the current install location is bad, simply remove the install dir and reinstall amr to a new place.
```
+ $INSTALL/amr
|  |- bin
|  |- decks (amr input decks)
|  |- include
|  |- lib
|  |- scripts
|  -- share
|
+ $HOME/amr
|  -- src
|      +- amr-umbrella
|          |- cache.0
|          |- cache
|          -- build
=
```

First, let's get a recent amr-umbrella release from github.  There are two options.  You can download amr-umbrella via "git clone" if you are connected to the internet (or can reach a web proxy that is allowed to access github):
```bash
mkdir -p $HOME/amr/src
cd $HOME/amr/src
git clone https://github.com/pdlfs/amr-umbrella.git
cd amr-umbrella
```

Alternately, you can download tar files of the latest amr-umbrella and source code cache tar file from the web and manaully transfer them to you target system.  The release web page is:
```
https://github.com/pdlfs/amr-umbrella/releases
```

So for the v0.0.0 release get these files:
```
wget https://github.com/pdlfs/amr-umbrella/archive/refs/tags/v0.0.0.tar.gz
wget https://github.com/pdlfs/amr-umbrella/releases/download/v0.0.0/cache.0.tar

```
and unpack them to match the scheme above.


Second, if you are using the cache.0.tar file (e.g. to skip the download step in the build):
```bash
cd $HOME/amr/src/amr-umbrella

# should create cache.0 directory with source file tars in it
tar xf cache.0.tar
cd cache
ln -fs ../cache.0/*.tar.gz .
cd ..
```

Now, kick-off the cmake auto-building process:
```bash
mkdir build
cd build
#
CC=cc CXX=CC cmake -DCMAKE_INSTALL_PREFIX=$INSTALL/amr \
      -DCMAKE_SYSTEM_NAME=CrayLinuxEnvironment \
      -DCMAKE_BUILD_TYPE=RelWithDebInfo ..

make
```
**NOTE**: after installation, the build dir `$HOME/amr/src` is no longer needed and can be safely discarded. `$INSTALL/amr` is going to be the only thing we need for running amr experiments.

**NOTE**: do not rename the install dir after installation is done. If the current install location is bad, simply remove the install dir and reinstall amr to a new place.

AMR BASELINE TEST
=================

We can run a minimal amr parthenon job with slurm to test the installation.

A minimal amr test script is installed in $HOME/amr/scripts/amr-minimal.sh.
Check the #SBATCH directives in amr-minimal.sh and then submit it to slurm using the sbatch command.

The amr-minimal.sh job only takes a few seconds to run.  The job log should end
 with a table of MPI metrics and not contain any obvious errors.  For example,
it should end with something like:
```
...
Number of MeshBlocks = 94; 78  created, 0 destroyed during this simulation.

walltime used = 4.38e-02
zone-cycles/wallsecond = 5.39e+06
        Metric:        Count          Avg          Std          Min          Ma
x
-------------------------------------------------------------------------------
-
      MPI_Bcast:           32          494          521            2         10
63
  MPI_Allgather:          416          148          509            2         39
37
  MPI_Allreduce:          336           43          136            3          6
62
 MPI_Allgatherv:           80           10            6            4           
28


Script complete!
start: Mon Mar 25 20:43:18 EDT 2024
  end: Mon Mar 25 20:43:19 EDT 2024
```

END
===
Thanks for trying amr-umbrella :-)
