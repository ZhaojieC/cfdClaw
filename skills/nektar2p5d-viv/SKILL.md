---
name: nektar2p5d-viv
description: In-house Nektar2.5D VIV setup, run, and troubleshooting workflow for Cylinder-style cases. Use when editing `cyl.rea`, selecting free vs forced vibration settings, setting Re/Ur/mass/damping parameters, launching `flexF`, handling restart/map files, or converting outputs for post-processing.
---

# Nektar2.5D VIV

Use this skill for Zhicheng's in-house Nektar2.5D VIV work (not Nektar++ unless explicitly requested).

## Quick workflow

1. Go to the target case directory (usually under `.../Nektar2.5D/Examples/Cylinder*`).
2. Confirm key files exist:
   - `cyl.rea`
   - mesh/restart files if restart mode is used (`cyl_old.rst`, `cyl.map.rst`)
3. Edit VIV parameters in `cyl.rea`.
4. Launch with `flexF` (normally `mpirun -np 1 .../SPM_Thermo/Linux/flexF -chk -z2 -S -ou cyl.rea`).
5. Tail `out` to verify progression and catch startup failures early.

## Build order (from project ReadMe)

Run `source compile` in this order:
1. `GS/`
2. `rfftw/`
3. `Veclib/`
4. `Hlib/Linux/`
5. `SPM_Thermo/Linux/`
6. `Utilities/Linux/`

## Running the standard 2D example

In `Example(s)/Cylinder/`:
1. `source generateInput.sh`
2. `source runme`
3. `source post.sh`

## Parameter editing rules

- Treat `FORCX` and `FORCY` as primary VIV mode selectors in this branch.
- Keep edits minimal and explicit; change only requested knobs.
- For Re control in existing Cylinder cases, `KINVIS` is the primary fluid knob.
- For structural settings, commonly edited parameters are `WN`, `WNC`, `WNB`, `ZMASS`, `ZETA`.
- For prescribed harmonic motion, tune `AMPX/AMPY`, `FREQX/FREQY`, `PHITX/PHITY`.

For parameter meaning details, read `references/map-rea-parameter-guide.md`.

## Reliability checks after launch

- Check `out` for:
  - normal read of `cyl.rea`
  - no fatal missing-file errors
  - advancing `Time step = ...` lines
- If startup fails with restart/map errors, ensure restart files are present in run directory.
- If invoked outside original example directory, use absolute path for `flexF`.

## Expected outputs

- Runtime log: `out`
- Time histories / diagnostics: `cyl.dog`, `cyl.fce`, `cyl.cab` (case-dependent)
- Post-processing through project scripts (e.g., `post.sh`).
