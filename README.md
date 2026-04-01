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
│   ├── crustal_thickness_dataset.csv
│   └── mantle_temperature_dataset.csv
├── scripts/
│   ├── main_analysis.ipynb
│   ├── elevation_reconstruction.py
│   └── clustering_analysis.py
├── figures/
│   ├── boxplots/
│   └── violinplots/
└── README.md
```

---

## Included Datasets

### 1. Global Paleo Crustal Thickness Dataset

Derived from global igneous geochemical compilations (GEOROC, EarthChem) and processed using a machine learning–based mohometry framework.

| Field | Description |
|---|---|
| Major elements | wt% |
| Trace elements | ppm |
| Derived crustal thickness | Z_c (km) |
| Metadata | Spatial coordinates + age |

**Data filtering criteria:**

- SiO₂: 45–80 wt%
- MgO: 0–10 wt%
- LOI < 3%
- \> 100,000 samples retained

### 2. Age–Temperature Dataset

Interpolated mantle potential temperature evolution based on [Herzberg et al. (2010)](https://doi.org/10.1029/2009GC002924), used to compute time-dependent density structure.

---

## Methodological Framework

### 1. Paleo-Crustal Thickness Reconstruction

Crustal thickness is estimated using a machine learning framework (Zhou et al., 2025), trained on global geochemical data. Kernel Ridge Regression with bootstrap resampling is applied for temporal trend analysis.

### 2. Paleo-Elevation Reconstruction

Elevation is calculated using time-dependent isostatic balance:

$$H(t) = \frac{\rho_m(t) - \rho_c(t)}{\rho_m(t)} \cdot \left(Z_c(t) - Z_0(t)\right)$$

Key features:
- **Time-evolving densities** for both crust and mantle
- **Variable reference crustal thickness** Z₀(t):
  - Archean: 40–55 km
  - Post-Archean: 26–32 km
- Reflects secular thermal and compositional evolution

### 3. Uncertainty Quantification

Monte Carlo simulation (n = 200 per sample) yields:

| Output | Description |
|---|---|
| Median elevation | Central estimate |
| IQR | Interquartile range |
| P95 | 95th percentile |

### 4. Spatial Clustering of Highlands

DBSCAN clustering using haversine distance, with elevation thresholds:

| Label | Threshold |
|---|---|
| P2 | ≥ 2 km |
| P3 | ≥ 3 km |
| P45 | ≥ 4.5 km |

Key innovations:
- Sampling bias correction via spatial weighting
- Spatial thinning at 75 km resolution
- Fixed clustering parameters for temporal comparability

### 5. Sensitivity Tests

The following uncertainties were systematically evaluated:

- Mantle thermal history
- Crustal density evolution timing
- Time bin width (25–100 Myr)
- Clustering probability thresholds

> ✅ **Result:** Elevation trends are robust, with variations typically < 0.2–0.3 km

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
2. Reconstruct crustal thickness trends
3. Convert to elevation via Monte Carlo simulation
4. Apply DBSCAN spatial clustering
5. Generate statistical plots

---

## Reproducibility Notes

- The datasets provided here are simplified versions of the full compilation used in the study
- The full dataset (> 100,000 samples) is available upon request or via linked repositories
- All statistical trends (median, IQR, P95) can be reproduced using the included scripts
- Spatial reconstructions (Movies S1–S2) require integration with plate reconstruction models (see [EarthByte repo](https://github.com/EarthByte/Paleo-topography-in-deep-time))

---

## Supplementary Materials

This repository supports the following supplementary materials from the manuscript:

| Item | Description |
|---|---|
| Text S1–S4 | Methods and sensitivity tests |
| Figures S1–S6 | Supporting figures |
| Table S1 | Full geochemical + derived dataset |
| Movies S1–S2 | Spatiotemporal evolution of highlands |

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
- Frontiers Science Center for Deep Ocean Multispheres and Earth System, Ocean University of China
- Laoshan Laboratory, Qingdao, China
- EarthByte Group, School of Geosciences, The University of Sydney, Australia
