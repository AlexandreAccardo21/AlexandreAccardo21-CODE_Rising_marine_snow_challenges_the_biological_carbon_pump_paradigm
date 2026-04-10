# Rising Marine Snow Challenges the Biological Carbon Pump Paradigm — Code Repository

This repository contains the analysis code associated with the scientific paper:

> **"Rising marine snow challenges the biological carbon pump paradigm"**
> Alexandre Accardo *et al.*

The traditional view of the biological carbon pump assumes that marine snow (aggregates of organic particles) sinks from surface waters into the ocean interior, exporting carbon to depth. This study challenges that paradigm by demonstrating that a significant fraction of marine snow particles actually *ascend* rather than sink. The code here reproduces all main and supplementary figures of the paper, together with the core data-processing pipelines.

---

## Scientific context

Particle vertical velocities were measured using the **UVP6** (Underwater Vision Profiler 6) camera, deployed on **TZEX** (Time-series of particle eXchange) floats during three oceanographic campaigns:

| Deployment | Region | Abbreviation |
|---|---|---|
| APERO cruise | North Atlantic | APERO |
| Villefranche cruise | Mediterranean Sea | VLFR |
| MBARI deployment | Monterey Bay (East Pacific) | MBARI |

Raw particle tracks are corrected for instrument motion using a polynomial-regression approach, filtered with an interquartile-range selection procedure, and classified as ascending or descending. The paper then quantifies the implications of upward-moving particles for particulate organic carbon (POC) fluxes and for the carbon budget of the mesopelagic zone.

---

## Repository structure

```
.
├── Correction_selection_procedure/
│   └── Vertical_speed_correction_selection_procedure.ipynb
├── Multi_Otsu_Thresholding/
│   └── Multi_Otsu_thresholding.ipynb
├── Figure_1/
│   └── Figure_1.ipynb
├── Figure_2/
│   └── Figure_2.ipynb
├── Figure_3/
│   └── Figure_3.ipynb
├── Figure_4/
│   └── Figure_4.ipynb
├── Figure_5/
│   └── Figure_5.ipynb
├── Figure_S1/
│   └── Figure_S1.ipynb
├── Figure_S2/
│   └── Figure_S2.ipynb
├── Figure_S4/
│   ├── APERO_tracks.ipynb
│   ├── VLFR_tracks.ipynb
│   └── MBARI_tracks.ipynb
├── Figure_S6/
│   └── Figure_S6.ipynb
├── Figure_S7/
│   └── Figure_S7.ipynb
├── Figure_S8/
│   └── Figure_S8.ipynb
├── Figure_S9/
│   └── Figure_S9.ipynb
├── Figure_S11/
│   └── Figure_S11.ipynb
└── Figure_S12/
    └── Figure_S12.ipynb
```

---

## Notebook descriptions

### Core processing pipelines

#### `Correction_selection_procedure/Vertical_speed_correction_selection_procedure.ipynb`
The central data-processing pipeline applied to all three deployments:
1. **Correction** — Fits a polynomial regression to each UVP6 imaging sequence to model the time-varying background water velocity, then subtracts the predicted value from the raw vertical speed. Only sequences with a regression R² > 0.5 are retained.
2. **Selection** — Identifies statistically anomalous tracks (fast-sinking or fast-ascending) using a 1.5 × interquartile-range criterion applied per sequence.
3. **Organism removal** — Merges EcoTaxa taxonomic annotations to discard zooplankton tracks, retaining only detrital particles (marine snow).

Outputs a per-deployment CSV of selected, corrected particle tracks used by all figure notebooks.

#### `Multi_Otsu_Thresholding/Multi_Otsu_thresholding.ipynb`
Applies **multi-level Otsu thresholding** (via `scikit-image`) to raw UVP6 vignettes to segment each particle image into low-grey and high-grey regions. This quantifies the internal grey-level heterogeneity of ascending vs. descending particles — a proxy for particle composition and porosity — across all three deployments.

---

### Main figures

#### `Figure_1/Figure_1.ipynb`
Generates the **study-site map**: a global basemap showing the float surfacing positions for all three TZEX deployments, overlaid on monthly satellite chlorophyll-*a* fields (CMEMS L4 products) representative of the sampling period. Inset maps zoom in on each regional study site.

#### `Figure_2/Figure_2.ipynb`
Produces **distributions of corrected vertical speeds** split by particle size class (equivalent spherical diameter, ESD) and by ocean basin (North Atlantic, Mediterranean Sea, East Pacific). Panels show histograms of ascending (negative speed) and descending (positive speed) populations with median values annotated.

#### `Figure_3/Figure_3.ipynb`
Plots **sinking speed vs. ESD** scatter plots comparing TZEX-UVP6 observations against:
- In-situ literature measurements (Williams & Giering 2022)
- Ex-situ (laboratory) measurements compiled by Williams & Giering (2022)

Descending particle speeds are compared across all data sources; ascending particle speeds are shown separately.

#### `Figure_4/Figure_4.ipynb`
Analyses the **proportion of ascending vs. descending particles** as a function of size class across all deployments. Also includes a comparison with theoretical Stokes/Navier-Stokes sinking-speed predictions derived from particle morphology (ESD, minor/major axis), and computes per-size-class POC flux contributions.

#### `Figure_5/Figure_5.ipynb`
Computes and visualises the **mesopelagic carbon budget** (APERO cruise only), balancing:
- **Sources**: net POC flux through the upper mesopelagic boundary (100 m) + ascending POC flux from depth + dissolved organic carbon (DOC) fluxes
- **Demands**: prokaryotic heterotrophic demand (PCD = PHP + PHR, from Le Coq *et al.*, 2026)

Uncertainties are propagated using a 10 000-iteration Monte Carlo simulation.

---

### Supplementary figures

| Notebook | Content |
|---|---|
| `Figure_S1` | Pressure–time series of each TZEX deployment cycle, showing float parking depths. |
| `Figure_S2` | Chlorophyll-*a* time series at the long-term Point B station (Mediterranean Sea, 2023) with the VLFR deployment period highlighted. |
| `Figure_S4` (`APERO_tracks`, `VLFR_tracks`, `MBARI_tracks`) | Individual particle track visualisations with side-by-side UVP6 vignettes for representative ascending and descending particles from each cruise. |
| `Figure_S6` | Turbulence profiles (Ozmidov length scale, eddy diffusivity *K*v, energy dissipation rate ε) from the APERO cruise (NetCDF input). |
| `Figure_S7` | Proportion of ascending vs. descending particles broken down by **parking depth** across all three deployments. |
| `Figure_S8` | Ascending vs. descending particle distributions by **biovolume class** (instead of ESD size class), including biovolume estimation from UVP6 pixel dimensions. |
| `Figure_S9` | Additional supplementary analysis (see notebook for details). |
| `Figure_S11` | Additional supplementary analysis (see notebook for details). |
| `Figure_S12` | Additional supplementary analysis (see notebook for details). |

---

## Dependencies

All notebooks are written in **Python 3** and rely on the following main libraries:

| Library | Purpose |
|---|---|
| `numpy`, `pandas` | Data manipulation |
| `matplotlib`, `seaborn` | Plotting |
| `xarray` | NetCDF file handling |
| `scikit-learn` | Polynomial regression, linear regression |
| `scikit-image` | Multi-Otsu thresholding, image region properties |
| `scipy` | Statistical tests (Mann–Whitney U) |
| `mpl_toolkits.basemap` | Geographic map rendering |
| `opencv-python` (`cv2`) | Image reading and resizing |

Install dependencies with:

```bash
pip install numpy pandas matplotlib seaborn xarray scikit-learn scikit-image scipy opencv-python
# Basemap must be installed separately; see https://matplotlib.org/basemap/
```

---

## Data

The notebooks reference input data files located on the author's local filesystem (`/home/aaccardo/these_alex/Papers/Rising_stars/Data/`). These data files are not included in this repository. The key datasets are:

- **Vertical speed tracks** (CSV): corrected and selected UVP6 particle tracks for APERO, VLFR, and MBARI (`*_selected_tracks_by_interquartile_poly_no_living.csv`).
- **Raw tracks** (TSV/CSV): per-frame particle positions, morphology, and grey-level data.
- **EcoTaxa exports** (TSV): taxonomic annotations used to remove zooplankton.
- **Satellite chlorophyll-*a*** (NetCDF, CMEMS): monthly L4 products for North Atlantic, Mediterranean, and East Pacific.
- **Turbulence data** (NetCDF): dissipation rate and diffusivity profiles from APERO.
- **POC flux data** (CSV): ascending and descending POC fluxes integrated at 100 m and 1000 m.
- **Carbon budget inputs** (CSV): dissolved organic carbon fluxes and prokaryotic heterotrophic demand from Le Coq *et al.* (2026).
- **Literature sinking speeds** (CSV): in-situ and ex-situ data compiled by Williams & Giering (2022).
- **UVP6 raw images**: vignettes used by `Figure_S4` track-visualisation notebooks.

---

## Citation

If you use this code, please cite the associated paper (full reference to be added upon publication).

---

## License

Please refer to the repository licence file or contact the corresponding author for reuse conditions.
