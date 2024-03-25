**Download, build, and install amr and its minimum dependencies in a single step.**

amr-umbrella
================

## Overview

This package is designed for quickly setting up amr on various computing platforms ranging from commodity NFS PRObE clusters to highly-optimized HPC systems used in national labs. The package features an automated process that downloads, builds, and installs amr (including its software dependencies) in a single step.

Written atop cmake, amr-umbrella is expected to work with major computing platforms. 

## Modules

* external dependencies
  * glog (https://github.com/google/glog.git)
  * kokkos (https://github.com/kokkos/kokkos.git)
  * parthenon (https://github.com/anku94/parthenon.git)
* pdlfs dependencies
  * pdlfs-common (https://github.com/pdlfs/pdlfs-common.git)
  * pdlfs-scripts (https://github.com/pdlfs/pdlfs-scripts.git)
  * amr-tools (https://github.com/anku94/amr.git XXX)

## Installation

A recent CXX compiler (e.g., gcc 5 or later) with standard building tools including make, cmake (used by amr), and automake (used by some of our dependencies).

### Ubuntu

On Ubuntu systems, these software requirements can be met by:

```bash
sudo apt-get install gcc g++ make cmake autoconf automake libtool pkg-config git
```

To build amr and install it under a specific prefix (e.g., $HOME/amr):

```bash
mkdir -p $HOME/amr
cd $HOME/amr

# After installation, we will have the following:
#
# $HOME/amr
#  -- bin
#  -- include
#  -- lib
#  -- src
#      -- amr-umbrella
#      -- amr-umbrella-build
#  -- share
#

mkdir -p src
cd src
git clone https://github.com/pdlfs/amr-umbrella.git
mkdir -p amr-umbrella-build
cd amr-umbrella-build

cmake -DCMAKE_INSTALL_PREFIX=$HOME/amr -DCMAKE_BUILD_TYPE=RelWithDebInfo \
  -DUMBRELLA_BUILD_TESTS=OFF -DUMBRELLA_SKIP_TESTS=ON \
../amr-umbrella

make
```

### LANL Trinity/Trinitite

**High-level summary: module/slurm/srun/gni**

LANL Trinity/Trinitite is a Cray machine. User-level software packages can be configured via a `module` command. Jobs are scheduled through SLURM and jobs directly run on compute nodes (no "MOM" nodes). MPI jobs should be launched using `srun`. Trinity/Trinitite features two types of compute nodes: Haswell and KNL. All Trinity/Trinitite nodes are interconnected via Cray Aries. 

#### Haswell

Each Trinity/Trinitite Haswell node has 32 CPU cores, 64 hardware threads, and 128GB RAM.

To build amr on such nodes:

```bash
export CRAYPE_LINK_TYPE="dynamic"
export CRAYOS_VERSION=6

module unload craype-hugepages2M

module load craype-haswell
module load PrgEnv-gnu
module load cmake

mkdir -p $HOME/amr
cd $HOME/amr

# After installation, we will have the following:
#
# $HOME/amr
#  -- bin
#  -- include
#  -- lib
#  -- src
#      -- amr-umbrella
#         -- cache
#         -- cache.0
#      -- amr-umbrella-build
#  -- share
#

mkdir -p src
cd src

git clone https://github.com/pdlfs/amr-umbrella.git
cd amr-umbrella
cd cache
ln -fs ../cache.0/* .
cd ..
cd ..

mkdir -p amr-umbrella-build
cd amr-umbrella-build

env CC=cc CXX=CC cmake -DCMAKE_INSTALL_PREFIX=$HOME/amr \
  -DCMAKE_BUILD_TYPE=RelWithDebInfo \
  -DUMBRELLA_BUILD_TESTS=OFF -DUMBRELLA_SKIP_TESTS=ON \
../amr-umbrella

make

make install

```

#### KNL

Each Trinity/Trinitite KNL node has 68 CPU cores, 272 hardware threads, and 96GB RAM. To build amr on such nodes, change `module load craype-haswell` to `module load craype-mic-knl`.

### LANL Grizzly

**High-level summary: module/slurm/srun/psm2**

LANL Grizzly is a Penguin machine. User-level software packages can be configured via a `module` command. Jobs are scheduled through SLURM and jobs directly run on compute nodes (no "MON" nodes). MPI jobs should be launched using `srun`. Each Grizzly node has 36 CPU cores and 64GB RAM. Grizzly compute nodes are interconnected via Intel Omni-Path.

To build amr on LANL Grizzly:

```bash
module add cmake gcc intel-mpi

mkdir -p $HOME/amr
cd $HOME/amr

# After installation, we will have the following:
#
# $HOME/amr
#  -- bin
#  -- include
#  -- lib
#  -- src
#      -- amr-umbrella
#         -- cache
#         -- cache.0
#      -- amr-umbrella-build
#  -- share
#

mkdir -p src
cd src

git clone https://github.com/pdlfs/amr-umbrella.git
cd amr-umbrella
cd cache
ln -fs ../cache.0/* .
cd ..
cd ..

mkdir -p amr-umbrella-build
cd amr-umbrella-build

cmake -DCMAKE_INSTALL_PREFIX=$HOME/amr -DCMAKE_BUILD_TYPE=RelWithDebInfo \
  -DUMBRELLA_BUILD_TESTS=OFF -DUMBRELLA_SKIP_TESTS=ON \
../amr-umbrella

make
```

## Run parthenon with mpi on top of amr

TODO

**Enjoy** :-)
