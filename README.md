**Download, build, and install amr and its minimum dependencies in a single step.**

amr-umbrella
============

## Overview

The amr-umbrella package can quickly set up amr on various computing platforms ranging from commodity NFS PRObE clusters to highly-optimized HPC systems used in national labs. The package features an automated process that downloads, builds, and installs amr (including its software dependencies) in a single step.  The amr-umbrella package also optionally supports building amr from a cache of distribution tar.gz files for systems that cannot download files from the internet.

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

As a prerequisite, your system should have a C/C++ compiler, make, cmake, and MPI.

### Ubuntu

On Ubuntu systems, these software requirements can be met by:

```bash
sudo apt-get install gcc g++ make cmake git mpich
```

To build amr and install it under a specific prefix (e.g., $HOME/amr):

```bash
mkdir -p $HOME/amr
cd $HOME/amr

# After installation, we will have the following:
#
# $HOME/amr
#  -- bin
#  -- decks
#  -- include
#  -- lib
#  -- scripts
#  -- share
#  -- src
#      -- amr-umbrella
#      -- amr-umbrella-build
#

mkdir -p src
cd src
git clone https://github.com/pdlfs/amr-umbrella.git
mkdir -p amr-umbrella-build
cd amr-umbrella-build

cmake -DCMAKE_INSTALL_PREFIX=$HOME/amr -DCMAKE_BUILD_TYPE=RelWithDebInfo \
../amr-umbrella

# you can add "-j" flag to make to build in parallel
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

# hugepages caused problems with sparse memory and vpic, but
# may be ok with amr.  try unloading it if you have out of
# memory issues with amr.
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
#  -- decks
#  -- include
#  -- lib
#  -- scripts
#  -- share
#  -- src
#      -- amr-umbrella
#      -- amr-umbrella-build
#

mkdir -p src
cd src

# may need to set http_proxy/https_proxy in order to 'git clone' from github
git clone https://github.com/pdlfs/amr-umbrella.git
mkdir -p amr-umbrella-build
cd amr-umbrella-build

env CC=cc CXX=CC cmake -DCMAKE_INSTALL_PREFIX=$HOME/amr \
  -DCMAKE_BUILD_TYPE=RelWithDebInfo \
../amr-umbrella

make

```

#### KNL

Each Trinity/Trinitite KNL node has 68 CPU cores, 272 hardware threads, and 96GB RAM. To build amr on such nodes, change `module load craype-haswell` to `module load craype-mic-knl`.

### LANL Grizzly

**High-level summary: module/slurm/srun/psm2**

LANL Grizzly is a Penguin machine. User-level software packages can be configured via a `module` command. Jobs are scheduled through SLURM and jobs directly run on compute nodes (no "MOM" nodes). MPI jobs should be launched using `srun`. Each Grizzly node has 36 CPU cores and 64GB RAM. Grizzly compute nodes are interconnected via Intel Omni-Path.

To build amr on LANL Grizzly:

```bash
module add cmake gcc intel-mpi

mkdir -p $HOME/amr
cd $HOME/amr

# After installation, we will have the following:
#
# $HOME/amr
#  -- bin
#  -- decks
#  -- include
#  -- lib
#  -- scripts
#  -- share
#  -- src
#      -- amr-umbrella
#      -- amr-umbrella-build
#

mkdir -p src
cd src

git clone https://github.com/pdlfs/amr-umbrella.git
mkdir -p amr-umbrella-build
cd amr-umbrella-build

cmake -DCMAKE_INSTALL_PREFIX=$HOME/amr -DCMAKE_BUILD_TYPE=RelWithDebInfo \
../amr-umbrella

make
```

## Run a minimal amr parthenon job with MPI to test installation

A minimal amr test script is installed in $HOME/amr/scripts/amr-minimal.sh.

For slurm-based systems check the #SBATCH directives in amr-minimal.sh and then submit it to slurm using the sbatch command.

For CMU narwhal/emulab just run amr-minimal.sh directly from the command line.

The amr-minimal.sh job only takes a few seconds to run.  The job log should end with a table of MPI metrics and not contain any obvious errors.  For example, it should end with something like:
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

**Enjoy** :-)
