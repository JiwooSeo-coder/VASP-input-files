# VASP-input-files

Representative VASP input settings (INCAR, KPOINTS) for DFT calculations used in research on surface modification of Zn metal anodes with phosphonic acid at Gachon University's C-MAIN Lab.

Only calculation settings are shared. Structure files (POSCAR), pseudopotentials (POTCAR, which cannot be redistributed under the VASP license), and output files are not included.

## Structure

### `adsorption-energy/`
Settings for computing the adsorption energy of a molecule on a surface:

    E_ads = E(molecule + surface) - E(surface) - E(molecule)

The same INCAR and KPOINTS were applied to all three calculations (isolated molecule, bare surface, molecule on surface) so that the energies are directly comparable.

- **`INCAR`** — Geometry relaxation (conjugate gradient, `IBRION = 2`) with ions relaxed and the cell fixed. Plane-wave cutoff 520 eV, electronic convergence 1e-5 eV, force convergence 0.01 eV/Å, Gaussian smearing (`ISMEAR = 0`, `SIGMA = 0.05`), spin-unpolarized, symmetry off.
- **`KPOINTS`** — Gamma-point only (1x1x1).

### `charge-density-difference/`
Settings for computing the charge density difference upon adsorption:

    Δρ = ρ(molecule + surface) - ρ(surface) - ρ(molecule)

Each charge density comes from a single-point (static) calculation that writes CHGCAR. The subtraction is only meaningful if all three calculations use the same cell and FFT grid.

- **`INCAR`** — Static calculation (`IBRION = -1`, `NSW = 0`) with `LCHARG = .TRUE.`, `PREC = Accurate`, plane-wave cutoff 520 eV, electronic convergence 1e-6 eV, Gaussian smearing, spin-polarized (`ISPIN = 2`), symmetry off.
- **`KPOINTS`** — Gamma-point only (1x1x1).

The three calculations use identical settings except for the total magnetic moment constraint (`NUPDOWN`):

| Calculation | NUPDOWN |
|---|---|
| Isolated molecule | 1 |
| Bare surface | 1 |
| Molecule + surface | 0 |

The INCAR in this folder is the molecule + surface version.

### `aimd/`
Ab initio molecular dynamics (AIMD) at constant temperature.

- **`INCAR`** — MD mode (`IBRION = 0`) with a 1 fs timestep and 5000 steps (5 ps), Nosé–Hoover thermostat (`MDALGO = 2`) at 300 K (`TEBEG = TEEND = 300`), cell fixed (`ISIF = 2`). Electronic settings: plane-wave cutoff 520 eV, electronic convergence 1e-4 eV per MD step, Gaussian smearing, spin-unpolarized, symmetry off, `PREC = Normal`.
- **`KPOINTS`** — Gamma-point only (1x1x1).

Main outputs: `XDATCAR` (atomic trajectory) and `OSZICAR` (temperature and energy at each step).

## Usage

Place the files together with your own `POSCAR` and `POTCAR` in a calculation directory and run VASP:
```bash
mpirun -np <N> vasp_std
```

The exchange-correlation functional is PBE (GGA), set by the PBE POTCAR files used.

## Notes
These are the settings used for the specific systems in this work; cutoff, k-point sampling, and convergence criteria should be re-tested for other systems.
