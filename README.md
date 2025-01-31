# Development Repo of Resilient Control-Flow Attestation #

Repo for the dev of ReCFA.

## Setup ##

### Build SPEC CPU2006 C benchmarks

**Note: For the artifact evaluation, please ignore this step and use the binaries in the directory `spec_llvm` and `spec_gcc` of the repository `ReCFA`.**

**Due to the copyright reason, we cannot release the SPEC CPU 2k6 benchmarks `sos_spec06.tar.xz`. However, we release the data on google drive for skipping this step.**


### Build and configure typearmor at Ubuntu 16.04

**Note: Because typearmor requires Ubuntu 16.04 and ReCFA requires Ubuntu 18.04, please use a virtualbox guest OS to install dyninst 9.3.1 and typearmor used by ReCFA. ReCFA should be deployed on the host Ubuntu 18.04 with dyninst 10.1.0.**

- OS: ubuntu 16.04.1, dyninst version: 9.3.1

- System configuration
  ```
  sudo apt-get update
  sudo apt-get upgrade
  sodo apt-get install build-essential cmake m4 texlive-latex-base flex bison zlib1g-dev libboost-all-dev libelf-dev libiberty-dev
  ```
  Note: Do not install libdwarf-dev using apt.
- Install dyninst
  ```
  cd ~/dyninst-9.3.1
  mkdir install
  cd install
  cmake .. -DCMAKE_INSTALL_PREFIX=`pwd`
  make -j2
  make install
  ```
  - Keep the network connected (This procedure will download several components)
  - To install dyninst 10.1 for ReCFA follows similar instructions.

- Install typearmor
  ```
  cd ~/typearmor
  mkdir out
  . ./envsetup
  cd static
  make
  make install
  cd ../di-opt
  make
  make install
  ```
  - make the directory "out" is important, otherwise typearmor reports segment fault.

- Patch typearmor
  - replace `typearmor/static/fcfi_pass.C` with the file `fcfi_pass.C` in this repository.
  - rebuild and install typearmor.

**Note: The following step is for deploying dyninst 10.1.0 on the host Ubuntu 18.04. The installation of dyninst 10.1.0 for ReCFA is similar to the installation of dyninst 9.3.1**

- Deploying dyninst-10.1.0 for ReCFA, add to `~/.bashrc`
  ```
  DYNINST_HOME=~/dyninst-10.1.0
  export DYNINST_INCLUDE=$DYNINST_HOME/install/include
  export DYNINST_LIB=$DYNINST_HOME/install/lib
  export DYNINSTAPI_RT_LIB=$DYNINST_LIB/libdyninstAPI_RT.so
  export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$DYNINST_LIB
  export PATH=$PATH:$DYNINST_INCLUDE:$DYNINSTAPI_RT_LIB
  ```

## Run the instrumented SPEC2k6 C benchmarks 

**To generate the control flow events of the instrumented SPEC binaries, the instrumented binaries should be run with the standard workload of SPEC CPU 2k6. For the artifact evaluation, please skip this step and use the control-flow events files at the following links:**

- https://drive.google.com/file/d/10WiR7L3w_sRVK1JG6Tu8OKVNexhmwhB6/view?usp=sharing
- https://drive.google.com/file/d/1aoc1BppBAKIRDSAT0wsxq9WbkZ_rz_jS/view?usp=sharing



## Contributor ##

* **Yumei Zhang** - School of Cyber-Engineering, Xidian University.
* **Xinzhi Liu** - School of Cyber-Engineering, Xidian University.
* **Cong Sun** - School of Cyber-Engineering, Xidian University. CONTACT: suncong AT xidian DOT edu DOT cn


