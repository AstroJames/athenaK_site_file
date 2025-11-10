# CMake Presets for AthenaK / Kokkos Builds

This repository contains a single configuration file, [`CMakePresets.json`](./CMakePresets.json), defining multiple **CMake build presets** for different computing environments used in development and production runs of **AthenaK**, a high-performance magnetohydrodynamics (MHD) simulation framework.

The presets simplify cross-platform compilation by encapsulating compiler choices, architecture flags, and MPI/OpenMP/CUDA options for a variety of clusters that I will keep adding to with time, with the idea being that anyone can come and use these presets to build **AthenaK**.


## Overview of Presets

| Preset Name | Description | Target Platform | Parallelism | Key Compilers |
|--------------|--------------|----------------|--------------|----------------|
| **beattie-cpu** | Apple M3 CPU build with Homebrew LLVM, OpenMP, and MPI | macOS ARM (local) | OpenMP + MPI | Clang / Clang++ |
| **bee-gpu** | Generic GPU build on BEE (Volta70) | HPC GPU node | CUDA + MPI | `nvcc_wrapper` |
| **trill-cpu** | MPI CPU build on Trillium cluster | HPC CPU node | MPI | `mpicc`, `mpicxx` |
| **trill-gpu** | GPU MPI build on Trillium (Hopper90) | HPC GPU node | CUDA + MPI | `nvcc_wrapper` |

Each preset can be selected automatically through CMake ≥3.19 using the `--preset` flag, which configures compilers and build directories consistently across environments.

---

## Example Usage

First, put `CMakePresets.json` in your `/athenak/` directory. Now performing `cmake` from within this directory will be able to see your presets. You will also need to, as per usual, populate the `kokkos` directory in `athenak/kokkos` with the version of `kokkos` that is linked in the main **AthenaK** repository.

### 1. Apple M3 (Local Development)
Optimized for Homebrew LLVM with OpenMP and MPI. Need to `brew install llvm libomp open-mpi`, at least for the compile preset that I am using locally on my Mac.
```bash
cmake --preset beattie-cpu
```
or if you want to build a particular problem that is not a test problen
```bash
cmake --preset beattie-cpu --DPROBLEM current_sheet
```
or if you want to build a fresh build, getting rid of the cached files,
```bash
cmake --fresh --preset beattie-cpu --DPROBLEM current_sheet
```
Note how you can use the `--preset` flag, but also a `--DPROBLEM` flag to compile a particular problem. Next, go to `/athenak/build/src/` and `make` or `make -j $NUM_OF_CORES` for however many cores you have available. This will build the **AthenaK** executable.

### 2. Trillium CPU
First `module purge` then `module load gcc cmake openmpi fftw` (only need fftw if you have Beattie's fork). Now, go into `/athenak/` and use `cmake`
```bash
cmake --preset trill-cpu
cmake --preset trill-cpu --DPROBLEM current_sheet
```
then follow the steps above for building the executable. 

### 3. Trillium GPU
First `module purge` then `module load gcc cmake cude openmpi` (this will use `cufft` if you're using Beattie's fork). Now, go into `/athenak/` and use `cmake`
```bash
cmake --preset trill-gpu
cmake --preset trill-gpu --DPROBLEM current_sheet
```



