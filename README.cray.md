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

**NOTE**: after installation, the build dir `$HOME/amr/src` is no longer needed and can be safely discarded. `$INSTALL/amr` is going to be the only thing we need for running deltafs experiments.

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
First, let's get a recent amr-umbrella release from github:
```bash
mkdir -p $HOME/amr/src
cd $HOME/amr/src
git lfs clone https://github.com/pdlfs/amr-umbrella.git
cd amr-umbrella
```
Second, prepolute the cache directory:
```bash
cd cache
ln -fs ../cache.0/* .
cd ..
```
Now, kick-off the cmake auto-building process:

```bash
mkdir build
cd build
#
CC=cc CXX=CC cmake -DUMBRELLA_SKIP_TESTS=ON -DCMAKE_INSTALL_PREFIX=$INSTALL/amr \
      -DCMAKE_SYSTEM_NAME=CrayLinuxEnvironment \
      -DCMAKE_BUILD_TYPE=RelWithDebInfo ..

make
```
**NOTE**: after installation, the build dir `$HOME/amr/src` is no longer needed and can be safely discarded. `$INSTALL/amr` is going to be the only thing we need for running deltafs experiments.

**NOTE**: do not rename the install dir after installation is done. If the current install location is bad, simply remove the install dir and reinstall deltafs to a new place.

AMR BASELINE TEST
=================
// *TODO*

END
===
Thanks for trying amr-umbrella :-)
