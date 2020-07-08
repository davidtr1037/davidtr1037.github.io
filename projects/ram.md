---
layout: page
title: Relocatable Addressing Model
permalink: /ram/
---
___
### Source Code
The source code is available here: <https://github.com/davidtr1037/klee-ram>.

### Replication Package

#### Environment

The experiments were run on a Ubuntu 16.04 machine, with Intel i7-6700 processor (8 cores) and 32GB of RAM.
Note that building LLVM takes usually 15-30 minutes (depending on your hardware and how many cores you use),
so please take that into consideration.
The rest of the dependencies are built relatively quickly.

#### Build & Install
Install the following packages, using:
```
sudo apt-get install cmake bison flex libboost-all-dev python perl zlib1g-dev build-essential curl libcap-dev git cmake libncurses5-dev python-minimal python-pip unzip libtcmalloc-minimal4 libgoogle-perftools-dev libsqlite3-dev doxygen
pip3 install tabulate wllvm
```

_LLVM 7.0_

```
wget https://releases.llvm.org/7.0.0/llvm-7.0.0.src.tar.xz
wget https://releases.llvm.org/7.0.0/cfe-7.0.0.src.tar.xz
wget https://releases.llvm.org/7.0.0/compiler-rt-7.0.0.src.tar.xz
tar xJf llvm-7.0.0.src.tar.xz
tar xJf cfe-7.0.0.src.tar.xz
tar xJf compiler-rt-7.0.0.src.tar.xz
mv cfe-7.0.0.src llvm-7.0.0.src/tools/clang
mv compiler-rt-7.0.0.src compiler-rt
mkdir llvm-7.0.0.obj
cd llvm-7.0.0.obj
cmake CMAKE_BUILD_TYPE:STRING=Release -DLLVM_ENABLE_THREADS:BOOL=ON -DLLVM_ENABLE_PROJECTS:STRING=compiler-rt ../llvm-7.0.0.src
make -j4
```
Update the following variables:
```
export PATH=<llvm_build_dir>/bin:$PATH
export LLVM_COMPILER=clang
```

_minisat_

```
git clone https://github.com/stp/minisat.git
cd minisat
mkdir build
cd build
cmake -DCMAKE_INSTALL_PREFIX=/usr/local/ ../
sudo make install
```

_STP_

```
git clone https://github.com/stp/stp.git
cd stp
git checkout tags/2.3.3
mkdir build
cd build
cmake ..
make
sudo make install
```

_klee-uclibc_
```
git clone https://github.com/klee/klee-uclibc.git
cd klee-uclibc
git checkout 038b7dc050c07a7b4d941b48a0f548eea3089214
./configure --make-llvm-lib
make
cd ..
```

In the paper we used 3 versions of KLEE:
- Baseline KLEE
- SMM
- Our extension

To build those, first clone our repository:
```
git clone git@github.com:davidtr1037/klee-ram.git <klee_dir>
```

_Building Baseline KLEE_
```
git checkout issta-vanilla
mkdir vanilla-build
cd vanilla-build
CXXFLAGS="-fno-rtti" cmake \
    -DENABLE_SOLVER_STP=ON \
    -DENABLE_POSIX_RUNTIME=ON \
    -DENABLE_KLEE_UCLIBC=ON \
    -DKLEE_UCLIBC_PATH=/home/user/tau/klee-uclibc/ \
    -DLLVM_CONFIG_BINARY=<llvm_build_dir>/bin/llvm-config \
    -DLLVMCC=<llvm_build_dir>/bin/clang \
    -DLLVMCXX=<llvm_build_dir>/bin/clang++ \
    -DENABLE_UNIT_TESTS=OFF \
    -DKLEE_RUNTIME_BUILD_TYPE=Release+Asserts \
    -DENABLE_SYSTEM_TESTS=ON \
    -DENABLE_TCMALLOC=ON \
    ..
make
```

_Building SMM KLEE_

First, build SVF:
```
git clone https://github.com/kren1/SVF.git <svf-dir>
cd <svf-dir>
git checkout fix_pta_delete
mkdir build
cd build
LLVM_DIR=../../llvm-7.0.0.obj/ cmake ..
make
cp lib/Svf.so lib/libSvf.so
cp lib/CUDD/Cudd.so lib/CUDD/libCudd.so
```

And then:
```
git checkout segmented_mem7.0
mkdir smm-build
cd smm-build
CXXFLAGS="-fno-rtti" cmake \
    -DENABLE_SOLVER_STP=ON \
    -DENABLE_POSIX_RUNTIME=ON \
    -DENABLE_KLEE_UCLIBC=ON \
    -DKLEE_UCLIBC_PATH=/home/user/tau/klee-uclibc/ \
    -DLLVM_CONFIG_BINARY=<llvm_build_dir>/bin/llvm-config \
    -DLLVMCC=<llvm_build_dir>/bin/clang \
    -DLLVMCXX=<llvm_build_dir>/bin/clang++ \
    -DENABLE_UNIT_TESTS=OFF \
    -DKLEE_RUNTIME_BUILD_TYPE=Release+Asserts \
    -DENABLE_SYSTEM_TESTS=ON \
    -DENABLE_TCMALLOC=ON \
    -DSVF_ROOT_DIR=<svf-dir> \
    ..
make
```

_Building our tool_

To build our tool which is an extension of KLEE, do the following:
```
git checkout issta-tool
mkdir tool-build
cd tool-build
CXXFLAGS="-fno-rtti" cmake \
    -DENABLE_SOLVER_STP=ON \
    -DENABLE_POSIX_RUNTIME=ON \
    -DENABLE_KLEE_UCLIBC=ON \
    -DKLEE_UCLIBC_PATH=/home/user/tau/klee-uclibc/ \
    -DLLVM_CONFIG_BINARY=<llvm_build_dir>/bin/llvm-config \
    -DLLVMCC=<llvm_build_dir>/bin/clang \
    -DLLVMCXX=<llvm_build_dir>/bin/clang++ \
    -DENABLE_UNIT_TESTS=OFF \
    -DKLEE_RUNTIME_BUILD_TYPE=Release+Asserts \
    -DENABLE_SYSTEM_TESTS=ON \
    -DENABLE_TCMALLOC=ON \
    <klee_dir>
make
```

Update the PATH variable:
```
export PATH=<klee-dir>/tool-build/bin:$PATH
```

#### Usage and example
Our tool is an extension of KLEE, to which we added several new command line options.
The main options are:
- _use-sym-addr_: use the addressing model described in the paper (section 2.2)
- _use-rebase_: enable dynamic merging of memory objects (section 2.3)
- _split-objects_: enable dynamic splitting of memory objects (section 2.4)
- _split-threshold=N_: split objects larger than _N_ bytes (section 2.4)
- _partition-size=N_: set the size of the split objects (section 2.4)

For a simple example, we provide the following C program:
```
#include <stdio.h>
#include <stdlib.h>
#include <stdbool.h>
#include <klee/klee.h>

#define N (4)

typedef struct {
    int k;
    char data[512];
} object_t;

int main(int argc, char *argv[]) {
    object_t **array = calloc(N, sizeof(object_t *));
    for (unsigned int i = 0; i < N; i++) {
        array[i] = calloc(sizeof(object_t), 1);
    }

    unsigned int i = klee_range(0, N, "i");
    if (array[i]->k == 0) {
        printf("OK\n");
    }

    unsigned int k = klee_range(0, N, "k");
    if (array[k]->k == 0) {
        printf("OK\n");
    }

    return 0;
}
```

Analyzing with baseline KLEE:
```
klee -libc=uclibc -search=dfs -allocate-determ -allocate-determ-start-address=0x0 <bitcode_file>
```
Analyzing with our addressing model with merging:
```
klee -libc=uclibc -search=dfs -allocate-determ -allocate-determ-start-address=0x0 -use-sym-addr -use-rebase <bitcode_file>
```
Analyzing with our addressing model with splitting:
```
klee -libc=uclibc -search=dfs -allocate-determ -allocate-determ-start-address=0x0 -use-sym-addr -split-objects -split-threshold=128 -partition-size=64 <bitcode_file>
```

#### Benchmarks

First, clone the benchmarks repository:
```
git clone https://github.com/davidtr1037/klee-mm-benchmarks <benchmarks-dir>
```
Build the .bc files:
```
cd <benchmarks-dir>/m4
make all
cd <benchmarks-dir>/make
make all
cd <benchmarks-dir>/sqlite
make all
cd <benchmarks-dir>/apr
make test_driver.bc
cd <benchmarks-dir>/binutils
make all
cd <benchmarks-dir>/libxml2
make test_driver.bc
cd <benchmarks-dir>/coreutils
make all
./extract.sh
```

#### Traces

We provide the traces for our experiments, which can be downloaded from here: <https://doi.org/10.6084/m9.figshare.12625109>.
The traces are the _klee_out_ directories generated by KLEE.

Section 4.1:
```
python <benchmarks-dir>/parse_overhead.py traces/overhead
```
The directory _traces/overhead_ contains output directories of the form:
- `out-klee-<program>` (baseline)
- `out-mm-<program>` (our addressing model)

Table 3:

The directory _traces/merge/models_ contains output directories of the form _out-mode-search_.
```
python <benchmarks-dir>/parse_models.py traces/merge/models
```
Table 4:

The directory _traces/merge/resolve-queries_ contains output directories of the form _out-kN_ (for N=0,1,2,3,4) and _out-default_.
```
python <benchmarks-dir>/parse_resolve_queries.py traces/merge/resolve-queries
```
Table 5:

The directory _traces/merge/optimizations_ contains output directories of the form _out-no-opt_, _out-opt-context_, _out-opt-reuse_.
```
python <benchmarks-dir>/parse_opts.py traces/merge/optimizations
```
Table 7:

The directory _traces/split_ contains output directories of the form _out-split-pN_ (for N=32,64,128,256,512) and _out-split-vanilla_.
```
python <benchmarks-dir>/parse_opts.py traces/split
```

#### Experiments

__Configuration__

First, initialize the output directory which will contain the results of the experiments (klee-out directories):
```
<benchmarks-dir>/init_artifact_dir.sh <full-path-to-new-output-dir>
```
Before running the experiments, we will need to set some variables in `<benchmarks-dir>/config.sh`:
```
VANILLA_KLEE=<vanilla-build-dir>/bin/klee
KLEE_SMM=<smm-build-dir>/bin/klee
KLEE=<tool-build-dir>/bin/klee
ARTIFACT_DIR=<full-path-to-new-output-dir>
```

__Correctness and overhead__

Run the following command:
```
<benchmarks-dir>/run_overhead_all.sh
```
To parse the results:
```
python <benchmarks-dir>/parse_overhead.py <full-path-to-new-output-dir>/overhead
```

__Merging__

For the experiment related to Table 3, we run in 3 different memory models:
Forking memory model:
```
<benchmarks-dir>/run_fmm_all.sh
```
Segmented memory model:
```
<benchmarks-dir>/run_smm_all.sh
```
Dynamically segmented memory model:
```
<benchmarks-dir>/run_dsmm_all.sh
```
To parse the results:
```
python <benchmarks-dir>/parse_models.py <full-path-to-new-output-dir>/merge/models
```

__Resolution Queries__

For the experiment related to Table 4, do the following:
```
<benchmarks-dir>/run_resolve_all.sh
```
To parse the results:
```
python <benchmarks-dir>/parse_resolve_queries.py <full-path-to-new-output-dir>/merge/resolve-queries
```

__Optimizations__

For the experiment related to Table 5, do the following:
```
<benchmarks-dir>/run_opts_all.sh
```
To parse the results:
```
python <benchmarks-dir>/parse_opts.py <full-path-to-new-output-dir>/merge/optimizations
```


__Splitting__

For the experiment related to Table 7, do the following:
```
<benchmarks-dir>/run_split_all.sh
```
To parse the results:
```
python <benchmarks-dir>/parse_opts.py <full-path-to-new-output-dir>/split
```
