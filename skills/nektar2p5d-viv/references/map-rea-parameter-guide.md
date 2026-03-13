# map.C / cyl.rea VIV parameter guide (in-house Nektar2.5D)

## Scope

Use this as a compact reference for practical case edits. Ground truth for behavior is `SPM_Thermo/src/map.C` (and related SPM_Thermo sources), with values read from `cyl.rea`.

## Frequently edited parameters

- `FORCX`, `FORCY`
  - Primary VIV/forcing-direction selectors in this code branch.
  - Treat as first knobs to inspect/edit when changing vibration mode.

- `KINVIS`
  - Kinematic viscosity; controls Reynolds number for fixed U,D scaling.

- `ZMASS`
  - Structural mass parameter used by the mapping/structure coupling routines.

- `ZETA`
  - Structural damping coefficient (set `0.0` for no structural damping).

- `WN`, `WNC`, `WNB`
  - Structural frequency/tension/stiffness-related knobs used in SPM/map coupling.

- Harmonic motion controls:
  - `AMPX`, `FREQX`, `PHITX`
  - `AMPY`, `FREQY`, `PHITY`
  - Use for prescribed x/y oscillation cases.

## Practical setup patterns

### Free VIV (typical)

- Set physical/structural parameters (`KINVIS`, `ZMASS`, `ZETA`, `WN/WNC/WNB`).
- Keep harmonic amplitudes zero unless intentional forcing is required.
- Confirm `FORCX/FORCY` match desired active directions.

### Forced vibration (typical)

- Set `FORCX/FORCY` for desired forcing/coupling mode.
- Set nonzero harmonic amplitude/frequency/phase in target direction(s).
- Keep damping as requested (`ZETA=0` for undamped structure).

## Runtime checks

- `out` should show successful parameter read and advancing timesteps.
- Common failure mode: restart/map file missing (e.g., `cyl_old.rst`, `cyl.map.rst`).
- If running from a copied/new directory, use absolute path to `flexF` and copy required restart files.
