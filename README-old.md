# Development Repo of Resilient Control-Flow Attestation #

Repo for the dev of ReCFA.

## Setup ##

### Build SPEC CPU2006 C benchmarks

- `tar xvf sos_spec06.tar.xz` at this directory.
- `cd spec2006`
- `./install.sh` and type `yes` to confirm the installation.
- `cp ../spec_configs/*.cfg config/`
- `cd ..`
- `./batch-make-on-spec.sh gcc` or `./batch-make-on-spec.sh llvm`
- `./patch-spec-src.sh`

### Build and configure typearmor at Ubuntu 16.04

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

- Deploying dyninst-10.1.0 for ReCFA, add to `~/.bashrc`
  ```
  DYNINST_HOME=~/dyninst-10.1.0
  export DYNINST_INCLUDE=$DYNINST_HOME/install/include
  export DYNINST_LIB=$DYNINST_HOME/install/lib
  export DYNINSTAPI_RT_LIB=$DYNINST_LIB/libdyninstAPI_RT.so
  export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$DYNINST_LIB
  export PATH=$PATH:$DYNINST_INCLUDE:$DYNINSTAPI_RT_LIB
  ```
- Note: the environment of typearmor is different from the environment of ReCFA. So it's better to use ubuntu-16.04 and dyninst 9.3.1 in virtualbox to deploy typearmor and generate the information needed by ReCFA.

## Run the instrumented SPEC2k6 C benchmarks 

- Assume in the repository `ReCFA`, the prover binaries has been generated by instrumenting the original binaries of SPEC2k6 benchmarks.
- Run `./prepare-instrumented.sh`. This script copies the instrumented binaries from the repository `ReCFA` to the directory `spec2006`, generates the configuration file `gcc-O0-instru.cfg` and `llvm-O0-instru.cfg` for running the instrumented binaries with standard workloads.
- Run instrumented binary.
  ```
  cd spec2006
  source ./shrc
  cd ..
  ./run_instrumented.sh
  ```

## Patch typearmor, generate policy files with typearmor
  - replace `typearmor/static/fcfi_pass.C` with the file `fcfi_pass.C` in this repository.
  - rebuild and install typearmor.
  - Put the original (un-instrumented) binaries of SPEC2k6 in `typearmor/server-bins`. Then,
  ```
  cd typearmor/server-bins
  ../run-ta-static.sh ./bzip2_base.gcc_O0
  ```
  - The policy file will be generated into `typearmor/out/`, e.g. `typearmor/out/binfo.bzip2_base.gcc_O0`.
  - Move all the policy files into the repository `ReCFA` for use by the verifier.

## Contributor ##

* **Yumei Zhang** - School of Cyber-Engineering, Xidian University.
* **Xinzhi Liu** - School of Cyber-Engineering, Xidian University.
* **Cong Sun** - School of Cyber-Engineering, Xidian University. CONTACT: suncong AT xidian DOT edu DOT cn


