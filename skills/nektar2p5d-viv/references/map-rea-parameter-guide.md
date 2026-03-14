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
- `STASTEP` must be present in the parameter block, or runtime aborts with `forget to set STASTEP !`.

## `.rea` structure rule (critical)

When parameters are inserted/removed, line 4 (`N PARAMETERS FOLLOW`) must be updated.

Count this exactly as:
- number of lines after `PARAMETERS FOLLOW`
- until `Lines of passive scalar data follows...`.

Mismatch here can break parser behavior downstream.

## 2D run convention in this workflow

- Use `-z2` for 2D runs.
- With `MODES=3` (2nd-order SEM), use dealiasing quadrature `LQUAD=5`, `MQUAD=5`.

## `*.dog` format from `map.C`

Current branch writes with `writedog2(...)`:

- Default (`D_SCR` off, 13 columns):
  1. `time`
  2. `dx`
  3. `dy`
  4. `dzx`
  5. `dzy`
  6. `vx`
  7. `vy`
  8. `ax`
  9. `ay`
  10. `fx`
  11. `fy`
  12. `basep`
  13. span index `i`

- If `D_SCR` on (14 columns): inserts `old_static_map` after `dx`.
