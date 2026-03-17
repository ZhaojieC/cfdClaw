# Nektar2.5D (non-VIV) simulation notes

## 1) Compile for simulation (without VIV coupling workflow)

Use the project `ReadMe.txt` order exactly:

1. `cd Nektar2.5D/GS && source compile`
2. `cd Nektar2.5D/rfftw && source compile`
3. `cd Nektar2.5D/Veclib && source compile`
4. `cd Nektar2.5D/Hlib/Linux && source compile`
5. `cd Nektar2.5D/SPM_Thermo/Linux && source compile`
6. `cd Nektar2.5D/Utilities/Linux && source compile`

For non-VIV simulation runs, use **`nektarF`** (not `flexF`).

---

## 2) Mesh preparation workflow

1. Build geometry/mesh in Gmsh (`.geo` -> `.msh`, MSH2 format).
2. Keep boundary physical groups explicit:
   - periodic side A: `Physical Curve(3)`
   - periodic side B: `Physical Curve(4)`
   - walls: `Physical Curve(7)`
   - obstacle wall: `Physical Curve(8)`
   - fluid surface: `Physical Surface(10)`
3. Convert mesh to Nektar `.rea`:

```bash
python3 /users/zwang197/.openclaw/workspace/picture9_mesh_scaled/gmsh2rea_picture9.py --msh <mesh.msh> --rea <case.rea>
```

---

## 3) Parameter changes in `.rea`

Typical edits used for this case:

- `KINVIS = 10.0` (for `Re = 0.1` with unit-scale reference)
- `MODES = 3`
- `LQUAD = 5`
- `MQUAD = 5`

---

## 4) Periodic flow-direction setup + driving force

If flow direction is periodic (inlet/outlet periodic), use pressure-gradient driving in **DRIVE FORCE** section:

- `FFX = <value>` (streamwise forcing, e.g. `1.0`)
- `FFY = 0.0`
- `FFZ = 0.0`

Important for non-VIV usage in this workflow:

- keep `FORCX` / `FORCY` as VIV-control fields (do not use them as flow driver)
- use `FFX` as the primary streamwise driver for periodic flow

---

## 5) 2D run command

```bash
mpirun -np 1 /users/zwang197/Works/NeuroSEM/OpenClaw/Nektar2.5D/SPM_Thermo/Linux/nektarF -chk -z2 -S cyl.rea > out 2>&1
```

Notes:
- this branch warns that dealiasing prefers `z` multiple of 4; for 2D workflow we still use `-z2` as requested.
- run from a dedicated case folder (with `cyl.rea`).
