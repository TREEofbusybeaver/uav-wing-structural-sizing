# UAV Wing Structural Sizing

A clean, reproducible workflow to go from planform to spanwise bending sizing for a tapered UAV wing with a constant-chord center section. The code (`wk2_uav-wing-structural-sizing.py`) computes **L′(y)** via Schrenk’s approximation, integrates to shear **S(y)** and bending **M(y)** with cantilever boundary conditions, derives **σₐₗₗₒw** from **σᵧᵢₑₗd/η**, and sizes the required second moment **Ireq(y) = M(y)·c(y)/σₐₗₗₒw** for comparison with the built-up thin-walled wing-box/I-spar **Itotal(y)**. Defaults match the project wing geometry and material selections so results can be replicated quickly.

---

## Project Overview
- **Spanwise lift distribution** using Schrenk’s approximation, combining elliptic and planform-proportional distributions.
- **Shear force** and **bending moment** along the half-span for a cantilevered root.
- **Thin-walled cross-section properties** (area, centroid, Ixx, Iyy, Ixy) of a NACA 4412 section at multiple spanwise stations, accounting for taper via local chord.
- Outputs feed structural sizing and stress checks to guide thicknesses and layout to withstand applied loads.

---

## Features
- **Planform** with constant-chord center section: b ≈ 1.999 m, taper ratio = 0.8, cr = 0.300 m, ct = 0.240 m, S ≈ 0.546 m², with a 0.2 m constant-chord segment before linear taper.
- **Schrenk’s approximation**: averages elliptic and chord-proportional shapes to get L′(y); normalized/scaled to total lift.
- **Structural loads**: integrates q(y) → S(y) and M(y) on half-span with cantilever BCs S(b/2)=0, M(b/2)=0.
- **Allowables**: σₐₗₗₒw = σᵧᵢₑₗd / η (Al 6061-T4 yield = 145 MPa baseline; η user-defined).
- **Bending sizing**: Ireq(y) = M(y)·c(y)/σₐₗₗₒw; c(y) from NA to extreme fiber, bounded by t ≈ 0.12·c(y).
- **Built-up section**: NA and Itotal(y) from skins, webs, and I-spar caps; reports Ixx, Iyy, Ixy and margins Itotal − Ireq.
- Hooks for **shear flow** and **panel buckling** checks.

---

## Getting Started

### Setup
- Python **3.10+**
- Install dependencies:
    ```bash
    pip install numpy pandas matplotlib scipy
    ```

### Inputs to Set
- **Wing geometry**: b, cr, ct, taper, constant-chord segment length.
- **Material**: σᵧᵢₑₗd = 145 MPa (default), E as needed, η for σₐₗₗₒw.
- **Load case**: total lift or load factor; optional weight per span q(y) = L′(y) − w′(y).

### Run Workflow
1. Compute Schrenk L′(y) with constant-chord segment.
2. Integrate to S(y) and M(y) (half-span cantilever).
3. Compute Ireq(y) = M(y)·c(y)/σₐₗₗₒw.
4. Build wing-box/I-spar section and margin checks.
5. Export arrays/plots.

---

## Usage
- **Airfoil data**: provide NACA 4412 coordinates as `.dat` in `data/`.
- Code reads/scales by local chord, applies thin-wall formulas for section properties.
- To exclude weight, subtract w′(y) from L′(y) before integration.

---

## Repository Structure
```
notebooks/
  wk2_uav-wing-structural-sizing.py   # Main script
src/
  geometry.py     # Planform & chord(y)
  schrenk.py      # Lift distribution
  beams.py        # Shear/Bending calc
  section.py      # Thin-wall section builder
  sizing.py       # Ireq & margin
data/
  NACA_4412.dat   # Airfoil coordinates
exports/          # CSV outputs, figures
README.md
```

---

## Notes and Assumptions
- **Thickness** for bending is structural box depth, not aerodynamic chord; t/c=12% bound on cap spacing.
- Use σₐₗₗₒw = σᵧᵢₑₗd / η; for Al 6061-T4, σᵧᵢₑₗd ≈ 145 MPa.
- Add **shear flow** and **torsion** checks before finalizing gauges.

---

## How to Run Locally and Push to Git
```bash
# Convert notebook to script if needed
python notebooks/wk2_uav-wing-structural-sizing.py

# Outputs in exports/ folder

# Git setup
git init
git add .
git commit -m "Initial commit: wing structural sizing with Schrenk & thin-walled section props"
git branch -M main
git remote add origin <your_repo_url>
git push -u origin main
```

---

## Files
- `wk2_uav-wing-structural-sizing.py`: Main workflow script.
- `NACA_4412.dat`: Airfoil coordinates.

---

## Contributing
Issues and PRs welcome. Include a brief description and example inputs/outputs.

---

## License
MIT License

```
MIT License

Copyright (c) 2025

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```
