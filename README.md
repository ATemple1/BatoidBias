# Too threatened to try: how extinction risk drives research neglect in rays and skates

**Andrew J. Temple\*, Sophia Rosinski\*, Jesse E.M. Cochran, Faizan F. Khan, Lindsay Marshall, Michael L. Berumen**

\*Joint first authors

## Overview

This repository contains the data preparation scripts, analysis code, and processed data for the paper "Too threatened to try: how extinction risk drives research neglect in rays and skates". The study uses a Bayesian causal modelling framework to investigate what drives research bias across 651 species of rays and skates (Batoidea), drawing on over 7,000 publications. We construct a directed acyclic graph (DAG) to identify causal pathways through which species traits, geography, economics, and IUCN Red List status influence publication counts, and fit taxonomically-informed Bayesian mixed-effects models using `brms`.

## Repository structure

```
batoid-research-bias/
├── README.md
├── LICENSE
├── .gitignore
├── data/                                   # Processed datasets
│   ├── Data_Merged.csv                     # Main analysis dataset (see Data preparation below)
│   ├── rl_synonyms.csv                     # Red List synonyms for literature matching
│   └── Literature_Search/
│       └── lit_return_filtered.csv          # Filtered literature search results
├── data_preparation/
│   ├── 00a_data_extraction.Rmd             # IUCN Red List data extraction
│   ├── 00b_literature_processing.Rmd       # Literature search filtering & species assignment
│   └── 00c_colour_analysis.Rmd             # Colour phenotype quantification from images
├── analysis/
│   └── 01_analysis.Rmd                     # Main analysis (DAG, Bayesian models, figures)
└── output/                                 # Generated figures (not tracked)
    └── .gitkeep
```

## System requirements

### Software

- **R** (≥ 4.2.0)
- **RStudio** (recommended for running .Rmd notebooks)

#### R packages

Data preparation:
`tidyverse`, `iucnredlist`, `rredlist`, `purrr`, `doParallel`, `foreach`, `stringi`, `devtools`, `recolorize`, `colorspace`, `colordistance`, `parallel`, `grid`, `ggimage`, `ape`, `vegan`, `cowplot`

Analysis:
`tidyverse`, `dagitty`, `ape`, `car`, `nnet`, `brms`, `tidybayes`, `effectsize`, `ggtree`, `cowplot`, `emmeans`, `EValue`

### Tested on

- macOS (Apple Silicon)
- R 4.3.x / 4.4.x

### Hardware

No specialised hardware is required. Bayesian model fitting with `brms` benefits from multiple CPU cores. Models were run on a standard laptop (Apple M-series, 16 GB RAM). Total model fitting time is approximately 2–4 hours depending on hardware.

## Installation guide

1. Install [R](https://cran.r-project.org/) and [RStudio](https://posit.co/download/rstudio-desktop/).
2. Install required packages by running in R:

```r
install.packages(c(
  "tidyverse", "dagitty", "ape", "car", "nnet", "brms", "tidybayes",
  "effectsize", "ggtree", "cowplot", "emmeans", "EValue",
  "iucnredlist", "rredlist", "purrr", "doParallel", "foreach",
  "stringi", "devtools", "colorspace", "colordistance", "parallel",
  "grid", "ggimage", "vegan", "recolorize", "nnet", "stringi"
))
```

Note: `ggtree` is a Bioconductor package and may need to be installed via:

```r
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")
BiocManager::install("ggtree")
```

Typical install time: 10–20 minutes.

## Running the analyses

Set the working directory to the repository root before running any script.

### Data preparation

The data preparation scripts document how the raw data were processed. **They are provided for transparency and are not required to reproduce the main analysis**, which uses the processed dataset `Data_Merged.csv` in `data/`.

`Data_Merged.csv` was assembled manually by merging the outputs of the three data preparation scripts (`IUCN_Data.csv`/`status_all.csv` from 00a, `species_counts.csv` from 00b, `Colour_Data.csv` from 00c). Species range sizes (km²) were calculated separately from IUCN spatial data (species distribution shapefiles; see manuscript for details) and added to the merged dataset.

| Script | Description | Input | Output | Notes |
|--------|-------------|-------|--------|-------|
| `00a_data_extraction.Rmd` | Extracts batoid species data from the IUCN Red List API | IUCN API (requires key) | `IUCN_Data.csv`, `rl_synonyms.csv`, `status_all.csv`, `b_search.txt` | Requires a valid IUCN API key (register at https://apiv4.iucnredlist.org/) |
| `00b_literature_processing.Rmd` | Filters literature search results and assigns publications to species | `lit_return_filtered.csv`, `rl_synonyms.csv` | `species_counts.csv` | Uses parallel processing |
| `00c_colour_analysis.Rmd` | Quantifies colour phenotypes from species illustrations | Batoid images (not included) | `Colour_Data.csv` | Images not included in repo (file size and copyright); uses `recolorize` and `colordistance` |

### Main analysis

| Script | Description | Expected run time |
|--------|-------------|-------------------|
| `01_analysis.Rmd` | DAG construction and validation, Bayesian mixed-effects models (year, range, GDP, depth, size, colour contrast, colour uniqueness, shape, pattern, IUCN status), phylogenetic visualisation, effect size plots, E-value sensitivity analysis | 2–4 hours (model fitting) |

### Expected output

The analysis script generates all figures and statistical results reported in the paper, including:

- DAG visualisation and conditional independence tests
- Bayesian model summaries with phylogenetic random effects
- Posterior pairwise comparisons for categorical predictors
- Circular phylogenetic tree with colour-coded research bias
- Standardised effect size forest plots
- E-value sensitivity analysis

Figures are saved to the `output/` directory.

## Instructions for use

To reproduce the main analysis:

1. Clone this repository
2. Open `analysis/01_analysis.Rmd` in RStudio
3. Set the working directory to the repository root
4. Knit the document or run chunks sequentially

The processed data files in `data/` contain everything needed for the main analysis. The data preparation scripts (`00a`–`00c`) are provided for methodological transparency; rerunning them requires access to the IUCN Red List API and the original species images.

## Data sources

- **IUCN Red List of Threatened Species** — species assessments, taxonomy, distribution, and threat status (accessed via API v4; https://www.iucnredlist.org/)
- **Species illustrations** — by Lindsay Marshall (not included in repository due to file size and copyright)
- **Literature search** — conducted across multiple databases; filtered results provided in `data/Literature_Search/`

## License

Code in `analysis/` and `data_preparation/` is licensed under the [MIT License](LICENSE).
Data in `data/` is licensed under [CC-BY-4.0](https://creativecommons.org/licenses/by/4.0/).
See [LICENSE](LICENSE) for details.
