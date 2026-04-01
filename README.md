Repository for

“Recurrent super highlands since 2.1 Ga reveal rhythmic coupling between deep Earth and surface evolution”

Authors

Jianping Zhou*<sup>1,2,3</sup>, Ehsan Farahbakhsh<sup>1,2</sup>, Sanzhong Li<sup>1,2</sup>, Yongjiang Liu<sup>1,2</sup>, Guozheng Sun<sup>1,2</sup>, Xianzhi Cao<sup>1,2</sup>, Yanhui Suo<sup>1,2</sup>, and R. Dietmar Müller<sup>3</sup>

Affiliations
Frontiers Science Center for Deep Ocean Multispheres and Earth System, Ocean University of China
Laoshan Laboratory, Qingdao, China
EarthByte Group, School of Geosciences, The University of Sydney, Australia
Overview

This repository provides the core datasets and analysis workflows used to reconstruct global paleo-elevation and identify recurrent super highland systems since 2.1 Ga.

The workflow integrates:

Machine learning–based paleo-crustal thickness estimation
Time-dependent lithospheric density modeling
Isostatic elevation reconstruction
Monte Carlo uncertainty propagation
Bias-aware spatial clustering (DBSCAN)

This repository is designed to ensure full reproducibility of the statistical results and figures (e.g., box plots, violin plots, and elevation distributions) presented in the manuscript.

For the complete reconstruction pipeline and plate reconstruction framework, see:
👉 https://github.com/EarthByte/Paleo-topography-in-deep-time

Repository Structure (Recommended addition)
├── data/
│   ├── crustal_thickness_dataset.csv
│   ├── mantle_temperature_dataset.csv
│
├── scripts/
│   ├── main_analysis.ipynb
│   ├── elevation_reconstruction.py
│   ├── clustering_analysis.py
│
├── figures/
│   ├── boxplots/
│   ├── violinplots/
│
└── README.md
Included Data

Two simplified datasets are provided for transparency and reproducibility:

1. Global Paleo Crustal Thickness Dataset
Derived from global igneous geochemical compilations (e.g., GEOROC, EarthChem)
Processed using a machine learning–based mohometry framework
Includes:
Major elements (wt%)
Trace elements (ppm)
Derived crustal thickness (Zc)
Spatial and temporal metadata
2. Age–Temperature Dataset
Interpolated mantle potential temperature evolution
Based on Herzberg et al. (2010)
Used to compute time-dependent density structure
Methodological Framework
1. Paleo-crustal thickness reconstruction

Crustal thickness is estimated using a machine learning framework (Zhou et al., 2025), trained on global geochemical data.

Data filtering:
SiO₂: 45–80 wt%
MgO: 0–10 wt%
LOI < 3%

>100,000 samples retained

Kernel Ridge Regression + bootstrap resampling used for temporal trends
2. Paleo-elevation reconstruction

Elevation is calculated using time-dependent isostatic balance:

𝐻
(
𝑡
)
=
𝜌
𝑚
(
𝑡
)
−
𝜌
𝑐
(
𝑡
)
𝜌
𝑚
(
𝑡
)
⋅
(
𝑍
𝑐
(
𝑡
)
−
𝑍
0
(
𝑡
)
)
H(t)=
ρ
m
	​

(t)
ρ
m
	​

(t)−ρ
c
	​

(t)
	​

⋅(Z
c
	​

(t)−Z
0
	​

(t))

Key features:

Time-evolving crust and mantle densities
Variable reference crustal thickness 
𝑍
0
(
𝑡
)
Z
0
	​

(t):
Archean: 40–55 km
Post-Archean: 26–32 km
Reflects secular thermal and compositional evolution
3. Uncertainty quantification
Monte Carlo simulation (n = 200 per sample)
Outputs:
Median elevation
IQR
95th percentile
4. Spatial clustering of highlands
DBSCAN clustering with haversine distance
Elevation thresholds:
P2 ≥ 2 km
P3 ≥ 3 km
P45 ≥ 4.5 km

Key innovations:

Sampling bias correction via spatial weighting
Spatial thinning (75 km resolution)
Fixed clustering parameters for temporal comparability
5. Sensitivity tests

The following uncertainties were systematically evaluated:

Mantle thermal history
Crustal density evolution timing
Time bin width (25–100 Myr)
Clustering probability thresholds

Result:
👉 Elevation trends are robust, with variations typically <0.2–0.3 km

Reproducibility Notes
The datasets provided here are simplified versions of the full compilation used in the study
Full dataset (>100k samples) is available upon request or via linked repositories
All statistical trends (median, IQR, P95) can be reproduced using the included scripts
Spatial reconstructions (Movies S1–S2) require integration with plate reconstruction models (see EarthByte repo)
Usage Instructions
Download datasets and scripts
Update file paths in the scripts
Run in JupyterLab

Typical workflow:

1. Load dataset
2. Reconstruct crustal thickness trends
3. Convert to elevation (Monte Carlo)
4. Generate statistical plots
Recommended Environment
Python: 3.9
Libraries:
numpy
pandas
scipy
scikit-learn
matplotlib
Environment: conda or venv
IDE: JupyterLab
Supplementary Materials (NEW)

This repository supports the supplementary materials including:

Text S1–S4 (methods and sensitivity tests)
Figures S1–S6
Table S1 (full geochemical + derived dataset)
Movies S1–S2 (spatiotemporal evolution of highlands)
Citation (建议新增)

If you use this repository, please cite:

Zhou, J. et al.
Recurrent super highlands since 2.1 Ga reveal rhythmic coupling between deep Earth and surface evolution

Contact

Dr. Jianping Zhou
📧 jianping.geo@outlook.com

📧 jianping.zhou@sydney.edu.au
