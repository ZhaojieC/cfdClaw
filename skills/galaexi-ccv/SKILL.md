---
name: galaexi-ccv
description: Build and run Galaexi on Brown CCV/Oscar with NVHPC+MPI, including module setup, CMake configure/build, GPU sbatch job setup, and quick runtime checks. Use when compiling Galaexi, preparing run folders from Example inputs, submitting jobs, or diagnosing common startup/runtime issues on CCV.
---

# Galaexi on CCV

Use this skill for the practical compile/run workflow of Galaexi on CCV.

## Quick workflow
1. Enter code root (e.g. `/users/zwang197/Codes/galaexi_EVM`).
2. Load CCV modules and compiler env.
3. Configure with the repo script (`cmake_nvhpc.sh` or MPI variant).
4. Build and confirm `build/bin/galaexi` exists.
5. Create a clean run folder, copy binary + `.ini` + mesh + run script.
6. Submit with `sbatch runme.galaexi`.
7. Verify startup via `squeue`, `gpu.out`, `gpu.err`.

## Compile steps (CCV)
From the Galaexi repo root:

```bash
module purge
module load nvhpc/25.5-ar5i
module load hpcx-mpi/2.25.1s-le4f
module load cuda
module load cmake

export CC=mpicc
export CXX=mpicxx
export FC=mpifort
export OMPI_CC=nvc
export OMPI_CXX=nvc++
export OMPI_FC=nvfortran

source cmake_nvhpc.sh
make -j 4
```

If using explicit MPI build script in this repo, use `source cmake_mpi_nvhpc.sh` instead.

## Run-folder preparation
Use a separate run directory (typically under scratch):

```bash
RUN=/users/zwang197/scratch/zwang/galaexi_runs/<case_name>
mkdir -p "$RUN"
cp build/bin/galaexi "$RUN/"
cp Example/parameter_flexi.ini "$RUN/"
cp Example/DARU_mesh.h5 "$RUN/"
cp Example/runme.galaexi "$RUN/"
cd "$RUN"
sbatch runme.galaexi
```

## Runtime checks
- Queue/state:
```bash
squeue -j <JOBID> -o "%.18i %.9P %.20j %.8u %.2t %.10M %.6D %R"
```
- Output tail:
```bash
tail -n 40 gpu.out
tail -n 40 gpu.err
```
- If only sanity-checking startup, cancel once logs look healthy:
```bash
scancel <JOBID>
```

## Common issues
- **Binary missing**: rebuild and verify `build/bin/galaexi`.
- **Input not found**: run folder must include mesh + ini expected by run script.
- **Module mismatch message in `gpu.err`**: check loaded toolchain (`module list`) and keep NVHPC/HPCX consistent with build.
- **Job pending for long time**: check partition/QoS/resource request against available GPUs.

## References
- `references/ccv-job-template.md`
