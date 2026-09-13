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
│       └── lit_return_filtered.csv         # Filtered literature search results
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

Versions are those used to produce the results reported in the paper (R 4.3.0, macOS 15.7.9).

Data preparation:
`tidyverse` (2.0.0), `iucnredlist` (0.1, installed from GitHub: `IUCN-UK/iucnredlist@ce169fb`), `rredlist` (1.0.0), `purrr` (1.0.4), `doParallel` (1.0.17), `foreach` (1.5.2), `stringi` (1.8.7), `devtools` (2.4.5), `recolorize` (0.2.0, installed from GitHub: `hiweller/recolorize@068d860`), `colorspace` (2.1-1), `colordistance` (1.1.2), `parallel` (base R), `grid` (base R), `ggimage` (0.3.3), `ape` (5.8-1), `vegan` (2.7-1), `cowplot` (1.1.3)

Analysis:
`tidyverse` (2.0.0), `dagitty` (0.3-4), `ape` (5.8-1), `car` (3.1-3), `nnet` (7.3-18), `brms` (2.22.0), `tidybayes` (3.0.7), `effectsize` (1.0.1), `ggtree` (3.10.1), `cowplot` (1.1.3), `emmeans` (1.11.1), `EValue` (4.1.3)

### Tested on

- macOS 15.7.9 (x86_64)
- R 4.3.0, RStudio 2023.03.1+446

### Hardware

No specialised hardware is required. Bayesian model fitting with `brms` benefits from multiple CPU cores. Models were run on a standard laptop (Apple M-series, 16 GB RAM). Total model fitting time is approximately 2–4 hours depending on hardware.

## Installation guide

1. Install [R](https://cran.r-project.org/) and [RStudio](https://posit.co/download/rstudio-desktop/).
2. Install required packages by running in R:

```r
install.packages(c(
  "tidyverse", "dagitty", "ape", "car", "nnet", "brms", "tidybayes",
  "effectsize", "cowplot", "emmeans", "EValue",
  "rredlist", "purrr", "doParallel", "foreach",
  "stringi", "devtools", "colorspace", "colordistance",
  "ggimage", "vegan"
))
```

`parallel` and `grid` are part of base R and do not need to be installed.

Note: `ggtree` is a Bioconductor package and must be installed via:

```r
if (!requireNamespace("BiocManager", quietly = TRUE))
    install.packages("BiocManager")
BiocManager::install("ggtree")
```

Note: `recolorize` and `iucnredlist` are not on CRAN and must be installed from GitHub:

```r
if (!requireNamespace("devtools", quietly = TRUE))
    install.packages("devtools")
devtools::install_github("hiweller/recolorize")   # version used: 068d860
devtools::install_github("IUCN-UK/iucnredlist")   # version used: ce169fb
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

## Data dictionary

Three data files are provided. All are comma-separated, UTF-8 encoded, with a single header row. Missing values are empty cells (read as `NA` by `read.csv()`).

### `data/Data_Merged.csv`

The analysis dataset: 651 rows, one per batoid species. This is the only file required to reproduce the main analysis. The variables below are those used in the DAG and the fitted models; transformations listed are those applied in `01_analysis.Rmd` (all numeric predictors were subsequently rescaled using Gelman's 2SD scaling).

**Taxonomy and identifiers**

| Variable | Description | Type / unit |
|----------|-------------|-------------|
| `class` | Taxonomic class (IUCN nomenclature); constant (`Chondrichthyes`) | Categorical |
| `order` | Taxonomic order: `MYLIOBATIFORMES`, `RAJIFORMES`, `RHINOPRISTIFORMES`, `TORPEDINIFORMES` | Categorical (4 levels) |
| `family` | Taxonomic family | Categorical (26 levels) |
| `genus` | Taxonomic genus | Categorical (103 levels) |
| `sci_name` | Accepted species binomial (IUCN nomenclature) | Character (651 unique) |

**Response variable**

| Variable | Description | Type / unit |
|----------|-------------|-------------|
| `papers` | Research output: number of Scopus records whose title, abstract or author keywords match the species' accepted name or any of its IUCN-listed synonyms (search conducted 3 July 2025, covering 1885–2025) | Count (0–1,083) |

**Exposures and confounders**

| Variable | Description | Type / unit |
|----------|-------------|-------------|
| `status` | IUCN Red List category: `LC` (Least Concern), `NT` (Near Threatened), `VU` (Vulnerable), `EN` (Endangered), `CR` (Critically Endangered), `DD` (Data Deficient), `EX` (Extinct) | Categorical (7 levels) |
| `min_depth` | Shallowest reported depth of occurrence (IUCN upper depth limit) | Metres (0–2,300) |
| `max_depth` | Deepest reported depth of occurrence (IUCN lower depth limit) | Metres (5–4,155) |
| `year` | Year of species description, parsed from the IUCN taxonomic authority string | Year (1758–2023) |
| `size` | Maximum body size, sourced from Dulvy et al. (2024; https://doi.org/10.1126/science.adn1477) | Centimetres (15–700) |
| `size_type` | The body dimension that `size` refers to: `TL` (total length) or `DW` (disc width) | Categorical (2 levels) |
| `range_km2` | Total geographic range area, calculated from the IUCN species distribution shapefile reprojected to the Mollweide equal-area projection | Square kilometres (10–216,571,767) |
| `sum_GDP_bil` | Economic context of the range: the sum of the median GDP over the preceding ten years of every country whose Exclusive Economic Zone overlaps the species' distribution (World Bank, 2024) | Billion current US dollars (0–75,322) |
| `chr` | Chromatic contrast: the pixel-proportion-weighted mean Euclidean distance between colour-cluster centres of the species illustration in the a\*b\* (hue–saturation) plane of CIELAB space. Higher values indicate greater colour contrast | CIELAB distance units (0.02–16.95) |
| `achr` | Achromatic contrast: as `chr`, but computed on the L\* (lightness) channel only. Higher values indicate greater light–dark contrast | CIELAB distance units (0.40–22.90) |
| `uniqueness` | Colour uniqueness: the Euclidean distance of a species from the centroid of a three-dimensional NMDS ordination (stress = 0.035) of pairwise colour distances among all species. Higher values indicate a more distinctive colour phenotype | NMDS ordination units (1.19–37.29) |
| `pattern` | Body patterning, classified from the species illustration: `None`, `Lined`, `Spotted`, `Mottled`, `Other` | Categorical (5 levels) |
| `shape` | Body shape, classified from the species illustration and the *Rays of the World* identification entry: `Disc`, `Spade`, `Diamond`, `Wedge`, `Rhombus` | Categorical (5 levels) |

**Missing data**

Missing values occur in `min_depth`/`max_depth` (n = 57), `size` and `size_meansure` (n = 40), `chr`, `achr`, `uniqueness`, `pattern` and `shape` (n = 30, species without an illustration in *Rays of the World*), `range_km2` (n = 25) and `sum_GDP_bil` (n = 4). In `01_analysis.Rmd` these are imputed within taxonomic order, using the order mean for numeric variables and the order mode for categorical variables.


### `data/rl_synonyms.csv`

661 rows, one per batoid species assessed by the IUCN Red List. Generated by `00a_data_extraction.Rmd` and used both to build the Scopus search string and to assign publications to species in `00b_literature_processing.Rmd`.

| Variable | Description | Type / unit |
|----------|-------------|-------------|
| `sci_name` | Accepted species binomial (IUCN nomenclature) | Character |
| `synonyms` | Boolean search string for that species: the accepted binomial plus all IUCN-listed synonyms separated by ` OR ` | Character |

### `data/Literature_Search/lit_return_filtered.csv`

7,124 rows, one per publication. The raw Scopus return (n = 7,359; searched 3 July 2025) restricted to finalised publications and to the document types listed below. Field names follow the Scopus export schema.

| Variable | Description | Type / unit |
|----------|-------------|-------------|
| `Authors` | Author list, surname and initials, separated by semicolons (6 records missing) | Character |
| `Title` | Publication title | Character |
| `Year` | Year of publication | Year (1885–2025) |
| `Source_title` | Journal, book or series title | Character |
| `DOI` | Digital Object Identifier | Character |
| `Abstract` | Publication abstract | Character |
| `Author_Keywords` | Keywords supplied by the authors, separated by semicolons | Character |
| `Index_Keywords` | Keywords assigned by the indexing database, separated by semicolons | Character |
| `Language` | Language of the publication | Categorical |
| `Document_Type` | Scopus document type: `Article`, `Review`, `Book`, `Book chapter`, `Data paper`, `Letter`, `Note` | Categorical (7 levels) |
| `Publication_Stage` | Publication stage; constant (`Final`), the search having excluded in-press records | Categorical |


## Data sources

- **IUCN Red List of Threatened Species** — species assessments, taxonomy, distribution, and threat status (accessed via API v4; https://www.iucnredlist.org/)
- **Species illustrations** — by Lindsay Marshall (not included in repository due to file size and copyright)
- **Scopus** — literature search conducted 3 July 2025 across titles, abstracts and keywords, covering 1885–2025; filtered results provided in `data/Literature_Search/` (https://www.scopus.com/)
- **World Bank** — country-level GDP, from which range-state GDP was derived (World Bank Open Data, 2024; https://data.worldbank.org/)
- **Dulvy et al. (2024)** — maximum body size (https://doi.org/10.1126/science.adn1477)
- ***Rays of the World*** (Last et al.) — species illustrations, body shape and patterning classification

## License

Code in `analysis/` and `data_preparation/` is licensed under the [MIT License](LICENSE).
Data in `data/` is licensed under [CC-BY-4.0](https://creativecommons.org/licenses/by/4.0/).
See [LICENSE](LICENSE) for details.
