# Recurrent Super Highlands Since 2.1 Ga

> **Paper:** *Recurrent super highlands since 2.1 Ga reveal rhythmic coupling between deep Earth and surface evolution*
>
> **Authors:** Jianping Zhou · Ehsan Farahbakhsh · Sanzhong Li · Yongjiang Liu · Guozheng Sun · Xianzhi Cao · Yanhui Suo · R. Dietmar Müller

---

## Overview

This repository provides the core datasets and analysis workflows used to reconstruct global paleo-elevation and identify recurrent super highland systems since 2.1 Ga.

The workflow integrates:

- 🤖 Machine learning–based paleo-crustal thickness estimation
- 🌍 Time-dependent lithospheric density modeling
- ⚖️ Isostatic elevation reconstruction
- 📊 Monte Carlo uncertainty propagation
- 🗺️ Bias-aware spatial clustering (DBSCAN)

This repository is designed to ensure full reproducibility of the statistical results and figures (box plots, violin plots, elevation distributions) presented in the manuscript.

> For the complete reconstruction pipeline and plate reconstruction framework, see:
> 👉 [EarthByte/Paleo-topography-in-deep-time](https://github.com/EarthByte/Paleo-topography-in-deep-time)

---

## Repository Structure

```
├── data/
│   ├── Global_crustal_thickness_with_paleo_coords copy.csv
│   └── Herzberg_2010_Ur 0.23_Age_Temperature_Averaged.csv
│   └── Herzberg_2010_Ur 0.08_Age_Temperature_Averaged.csv
│   └── Herzberg_2010_Ur 0.38_Age_Temperature_Averaged.csv
│   └── Brown_2022_Age_Temperature_Averaged.csv
│   └── Condie_2016_Age_Temperature_Averaged.csv
│   └── Davis_2009_Age_Temperature_Averaged.csv
├── scripts/
│   ├── Figure 2 and 3.ipynb
│   ├── Figure 4.ipynb
│   └── Figure S2.ipynb
│   └── Figure S3.ipynb
└── README.md
```

---

## Included Datasets

### 1. Global Paleo Crustal Thickness Dataset

Compiled from global igneous geochemical databases (GEOROC, EarthChem) and the Gard et al. (2019) compilation, processed using the machine learning–based mohometry framework of Zhou et al. (2025).

| Field | Description |
|---|---|
| Major elements | wt% |
| Trace elements | ppm |
| Elemental ratios | Calculated from major/trace elements |
| Derived crustal thickness | Z_c (km), with min/median/max uncertainty bounds |
| Mantle potential temperature | Interpolated T_p(t) |
| Crustal & lithospheric mantle densities | Time-dependent estimates |
| Isostatic elevation | Multiple calibration schemes (Herzberg-, Brown-, Davies-, Condie-based) |
| Spatial metadata | Latitude, longitude, reconstructed paleocoordinates |
| Temporal metadata | Age, age uncertainty |
| Tectonic metadata | Tectonic province, plate ID, continental affinity |
| Data quality flags | Missing rate, associated thermal uncertainty |

**Data filtering criteria (Model 1 from Zhou et al., 2025):**

- SiO₂: 45–80 wt%
- MgO: 0–10 wt%
- LOI < 3%
- Total major elements: 97–101%
- \> 100,000 samples retained across diverse tectonic settings

### 2. Age–Temperature Dataset

Interpolated mantle potential temperature (T_p) evolution based on [Herzberg et al. (2010)](https://doi.org/10.1016/j.epsl.2010.01.022), used to compute time-dependent crustal and mantle density structure. Sensitivity tests using alternative T_p paths (Davies 2009; Condie et al. 2016; Brown et al. 2022) are also supported.

---

## Methodological Framework

### 1. Paleo-Crustal Thickness Reconstruction

Crustal thickness is estimated using the machine learning mohometry framework of Zhou et al. (2025), applying **Model 1** to maximise data density across diverse tectonic settings. Raw data are standardised by age and geochemical parameters; missing age data are corrected prior to analysis. Kernel Ridge Regression with bootstrap resampling is applied to derive temporal trends.

### 2. Paleo-Elevation Reconstruction

Elevation is calculated using time-dependent isostatic balance:

$$H(t) = \frac{\rho_m(t) - \rho_c(t)}{\rho_m(t)} \cdot \left(Z_c(t) - Z_0(t)\right)$$

where $\rho_m(t)$ and $\rho_c(t)$ are the time-dependent densities of the lithospheric mantle and continental crust, $Z_c(t)$ is the reconstructed paleo-crustal thickness, and $Z_0(t)$ is the reference crustal thickness at zero elevation.

#### Reference Crustal Thickness Z₀(t)

Z₀(t) varies through time to reflect fundamentally different isostatic boundary conditions, with 2.5 Ga (Archean–Proterozoic boundary) as the transition point:

| Era | Z₀ Range | Basis |
|---|---|---|
| Archean (> 2.5 Ga) | 40–55 km | Phase-driven lower crust densification; hotter mantle; suppressed subaerial emergence (Tang et al., 2024) |
| Post-Archean (≤ 2.5 Ga) | 26–32 km | Present-day observations across arc, margin, and orogenic settings (Mooney et al., 2023) |

Z₀ is sampled from a **triangular distribution** (lower, modal, upper bound) at each time step during Monte Carlo propagation.

#### Density Parameterization

**Continental crust density** evolves from an oceanic-like Archean endmember (ρ₀ = 2880 kg m⁻³ at 3.5 Ga) toward a modern felsic value (upper bound ~3320 kg m⁻³) following a linear transition from 3.5 Ga to 2.0 Ga (Korenaga et al., 2017). Thermal expansion is applied using $\alpha_c = 2.5 \times 10^{-5}\ \text{K}^{-1}$, with a mid-crustal reference temperature of T₀ = 300 °C.

**Lithospheric mantle density** is:

$$\rho_{cl}(t) = \rho_{m,0} \times \left(1 - \alpha_m \left(T_p(t) - T_{0,m}\right)\right)$$

with $\rho_{m,0} = 3300\ \text{kg m}^{-3}$, $\alpha_m = 3.0 \times 10^{-5}\ \text{K}^{-1}$, and $T_{0,m} = 1350\ °C$.

### 3. Uncertainty Quantification

Monte Carlo simulation (**n = 200 draws per sample**) propagates uncertainty in Z₀ via triangular distributions. Outputs per sample:

| Output | Description |
|---|---|
| Median elevation | Central estimate |
| IQR | Interquartile range |
| P95 | 95th percentile |

Tests with larger sample sizes confirm n = 200 provides stable summary statistics. For probability-threshold filtering in the clustering step, 300 triangular-distribution draws are used.

### 4. Spatial Clustering of Highlands

DBSCAN clustering (Ester et al., 1996; scikit-learn; Pedregosa et al., 2011) with haversine distance metric. The pipeline proceeds in four steps:

**Step 1 — Probability filtering.** Samples are admitted to each layer's clustering pool only if their Monte Carlo support meets the probability threshold. Primary analyses use `thr = 0.7` (≥ 70% of draws exceed the target elevation); sensitivity analyses use `thr = 0.3`.

| Layer | Elevation threshold |
|---|---|
| P2 | ≥ 2 km |
| P3 | ≥ 3 km |
| P45 | ≥ 4.5 km |

**Step 2 — Spatial weighting.** Each sample is assigned weight $w_i = 1/n_i$, where $n_i$ is the count of neighbours within 300 km, to correct sampling bias in densely drilled regions.

**Step 3 — Greedy spatial thinning** at 75 km resolution, aggregating nearby samples into representative nodes with combined probability:

$$P = 1 - \prod_i (1 - P_i)$$

**Step 4 — DBSCAN** applied to thinned nodes (`eps = 900 km`, `min_samples = 3`). Parameters are fixed across all time windows for temporal comparability. Nodes failing the core-point criterion are labelled noise.

Time windows use a **50 Myr sliding window stepped at 10 Myr intervals**, reconstructed to the central age using the **Cao et al. (2024)** plate motion model. The 50 Myr bin width is determined by the Freedman–Diaconis rule as the optimal balance between temporal resolution and per-bin sample size.

### 5. Sensitivity Tests

| Test | Parameters varied | Result |
|---|---|---|
| Mantle thermal history | T_p paths: Herzberg (ref.), Davies, Condie, Brown | Elevation differences < 0.2–0.3 km; no systematic divergence |
| Crustal density transition timing | t₀ = 1.8, 2.0 (ref.), 2.2, 2.5 Ga | Minor variation in elevation; long-term trend unchanged |
| Time bin width | 25, **50 (ref.)**, 75, 100 Myr | First-order trends consistent; 50 Myr optimal |
| Clustering probability threshold | **thr = 0.7 (primary)** vs. thr = 0.3 (sensitivity) | Five primary P45 clusters consistently recovered in both settings |

> ✅ All key conclusions, including the emergence of elevated terrains during the early Paleoproterozoic, are robust to the above parameter choices.

---

## Usage Instructions

### Environment Setup

```bash
conda create -n superhighlands python=3.9
conda activate superhighlands
pip install numpy pandas scipy scikit-learn matplotlib jupyterlab
```

### Running the Analysis

```bash
git clone https://github.com/<your-repo>/super-highlands.git
cd super-highlands
jupyter lab
```

Open `scripts/main_analysis.ipynb` and update file paths as needed.

**Typical workflow:**

1. Load dataset (`crustal_thickness_dataset.csv`)
2. Reconstruct crustal thickness trends (Kernel Ridge Regression + bootstrap)
3. Compute time-dependent densities (crust & mantle)
4. Convert to elevation via Monte Carlo simulation (n = 200)
5. Apply probability-threshold filtering and DBSCAN spatial clustering
6. Generate statistical plots (box plots, violin plots, elevation distributions)

---

## Reproducibility Notes

- Datasets provided here are simplified versions of the full compilation; the **complete dataset (> 100,000 samples)** is available upon request or via linked repositories
- All statistical trends (median, IQR, P95) are fully reproducible using the included scripts
- Spatial reconstructions (Movies S1–S2) require integration with plate reconstruction models — see [EarthByte/Paleo-topography-in-deep-time](https://github.com/EarthByte/Paleo-topography-in-deep-time)
- Plate motion model used for paleocoordinate reconstruction: **Cao et al. (2024)**

---

## Supplementary Materials

| Item | Description |
|---|---|
| **Text S1** | Data preparation and paleo-crustal thickness estimation |
| **Text S2** | Paleoelevation reconstruction (isostatic balance, density modeling, Monte Carlo) |
| **Text S3** | Bias-aware spatial clustering pipeline (DBSCAN, spatial weighting, thinning) |
| **Text S4** | Sensitivity tests (mantle T_p, crustal density timing, bin width, probability threshold) |
| **Figure S1** | Reconstructed global paleo-crustal thickness scatter plots since 4 Ga |
| **Figure S2** | Present-day crustal thickness vs. elevation across 8 tectonic domains; Z₀ reference values |
| **Figure S3** | Elevation sensitivity to mantle potential temperature cooling paths |
| **Figure S4** | Elevation sensitivity to timing of crustal density transition |
| **Figure S5** | Elevation distribution sensitivity to time bin width (25/50/75/100 Myr) |
| **Figure S6** | P45 cluster detection sensitivity to probability threshold at 0 Ma |
| **Table S1** | Full geochemical and derived geophysical dataset (major/trace elements, crustal thickness, densities, elevation estimates, spatial/temporal/tectonic metadata) |
| **Movie S1** | Deep-time P45 cluster reconstruction, thr = 0.7 (primary); Precambrian to present |
| **Movie S2** | Deep-time P45 cluster reconstruction, thr = 0.3 (sensitivity); Precambrian to present |

---

## Key References

| Reference | Role in this study |
|---|---|
| Zhou et al. (2025), *JGR: Solid Earth* | ML mohometry framework for crustal thickness |
| Herzberg et al. (2010), *EPSL* | Primary mantle T_p cooling path |
| Korenaga et al. (2017), *Phil. Trans. R. Soc. A* | Continental crustal density evolution parameterization |
| Mooney et al. (2023), *Earth-Sci. Rev.* | Reference Z₀ from present-day tectonic domains |
| Tang et al. (2024), *Sci. Adv.* | Archean Z₀ constraints and subaerial emergence |
| Cao et al. (2024), *Geosci. Front.* | Plate motion model for paleocoordinate reconstruction |
| Gard et al. (2019), *ESSD* | Supplementary geochemical compilation |
| Ester et al. (1996); Pedregosa et al. (2011) | DBSCAN algorithm and scikit-learn implementation |

---

## Citation

If you use this repository, please cite:

```bibtex
@article{zhou2025superhighlands,
  author  = {Zhou, Jianping and Farahbakhsh, Ehsan and Li, Sanzhong and Liu, Yongjiang
             and Sun, Guozheng and Cao, Xianzhi and Suo, Yanhui and M{\"u}ller, R. Dietmar},
  title   = {Recurrent super highlands since 2.1 Ga reveal rhythmic coupling between
             deep Earth and surface evolution},
  journal = {TBD},
  year    = {2025}
}
```

---

## Contact

**Dr. Jianping Zhou**
- 📧 [jianping.geo@outlook.com](mailto:jianping.geo@outlook.com)
- 📧 [jianping.zhou@sydney.edu.au](mailto:jianping.zhou@sydney.edu.au)

**Affiliations:**
1. Frontiers Science Center for Deep Ocean Multispheres and Earth System, Key Lab of Submarine Geosciences and Prospecting Techniques, College of Marine Geosciences, Ocean University of China, Qingdao 266100, China
2. Laboratory for Marine Mineral Resources, Laoshan Laboratory, Qingdao 266100, China
3. EarthByte Group, School of Geosciences, The University of Sydney, NSW 2006, Australia
