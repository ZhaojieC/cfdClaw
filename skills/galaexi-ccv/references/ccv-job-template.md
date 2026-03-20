# CCV job template for Galaexi

Use this minimal template in run folders where `galaexi`, mesh, and ini files already exist.

```bash
#!/bin/bash
#SBATCH --time=36:30:00
#SBATCH --nodes=1
#SBATCH --tasks-per-node=1
#SBATCH --mem=32G
#SBATCH -J galaexi
#SBATCH -p 3090-gcondo --gres=gpu:1
#SBATCH -o gpu.out
#SBATCH -e gpu.err

module purge
module load nvhpc/25.5-ar5i
module load hpcx-mpi/2.25.1s-le4f
module load cuda

./galaexi parameter_flexi.ini
```

## Notes
- Keep binary and inputs in the same folder unless paths are explicitly updated.
- For quick smoke test, shorten `--time`.
- If using another GPU partition, update `-p` and resource line accordingly.
