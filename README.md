## Spatial models of organic carbon content, dry bulk density, carbon reactivity index and labile organic matter on the UK continental shelf

### Overview
The aim is to provide new data layers on dry bulk density, organic carbon content and the carbon reactivity index of surficial sediments of the United Kingdom's continental shelf. These layers will be used in a model intercomparison study to explore the potential net effects of mobile bottom fishing on organic carbon stored in seabed sediments. We model and spatially predict organic carbon content, dry bulk density and the carbon reactivity index ([Smeaton and Austin, 2022](https://doi.org/10.1029/2021GL097481)) using a quantile regression forest ([Meinshausen, 2006](https://jmlr.org/papers/volume7/meinshausen06a/meinshausen06a.pdf)) framework. Outputs are aligned with c-squares on which fishing intensity data are reported by ICES; however, the spatial resolution has been increased from 0.05 degrees to 0.01 degrees.

Initially, a raster stack of predictor variables (covariates) is created. These include bathymetry, distance to land, bottom water salinity, bottom water temperature, bottom water current speed (mean and maximum), sea surface chorophyll-a, suspended particulate matter and seabed sediment composition (mud, sand and gravel). Organic carbon content in surface sediments, dry bulk density, the carbon reactivity index and the labile organic matter fraction are then modelled and spatially predicted. The resulting data layers are provided as georeferenced TIFF-files in unprojected format (WGS84) with a resolution of 0.01 degrees.

### Workflow and outputs

The repository is organised in numbered folders. Each model folder contains the same three scripts, which are run in order:

- `scripts/A_preprocessing/Aa_data_prep.Rmd` prepares the response data and the predictor stack.
- `scripts/B_data_exploration/Ba_data_exploration.Rmd` explores the response data and the predictors.
- `scripts/C_modelling/Ca_modelling.Rmd` fits the quantile regression forest, validates it, and predicts and exports the maps.

Some models use the output of another model as a predictor, so the order matters: **1_predictors → 3_DBD_model → 2_OC_content_model → 4_CRI_model and 5_LabileOM_model**.

| Step | Response variable (`resp_var`) | Unit | Data set tag (`dat`) | Notes |
|---|---|---|---|---|
| `1_predictors` | – | – | – | Builds the predictor stack `data/output/env_vars_0.01deg.tif` (and a Bio-ORACLE variant `env_vars_bio-oracle_0.01deg.tif`). |
| `2_OC_content_model` | Organic carbon content (`OC`) | weight-% | `all_ll_0.01` | Uses the DBD median from `3_DBD_model` as an additional predictor. |
| `3_DBD_model` | Dry bulk density (`DBD`) | g/cm³ | `ll_0.01` | Response data from St Andrews and CEFAS merged into one data set. |
| `4_CRI_model` | Carbon reactivity index (`CRI`) | – | `core_ll_0.01` | Uses the OC median from `2_OC_content_model` as an additional predictor. |
| `5_LabileOM_model` | Labile organic matter (`Labile_OM_pc`) | % | `core_ll_0.01` | Uses the OC median from `2_OC_content_model` as an additional predictor. |

#### Outputs of each model (`2_` to `5_`)

**Final maps** are written to `<model>/data/output/` as GeoTIFFs named `<resp_var>_<dat>_<layer>_<YYYY-MM-DD>.tif`, e.g. `OC_all_ll_0.01_median_2026-06-25.tif`:

| Layer | Description |
|---|---|
| `median` | Median prediction (the main map) |
| `P5`, `P25`, `P75`, `P95` | 5th, 25th, 75th and 95th percentile predictions of the quantile regression forest |
| `PI_abs` | Absolute uncertainty: width of the 90 % prediction interval (P95 − P5), masked to the area of applicability |
| `PI_rel` | Relative uncertainty: (P95 − P5) / median × 100 (%), masked to the area of applicability |
| `DI` | Dissimilarity index, i.e. how different a location's predictors are from the training data |
| `AOA` | Area of applicability (1 = inside, 0 = outside), where the model can be trusted |

The area of applicability is also exported as a polygon shapefile (`<resp_var>_<dat>_AOA_<date>.shp`) in the same folder.

**Interim files** in `<model>/data/interim/`: the cleaned response points (shapefile named after `resp_var`), the response data joined with predictor values (`rm_resp.csv`) and the predictor stack used by the model (`predictors.tif`).

**Figures** in `<model>/figures/` (JPEG, 300 dpi): observation histogram, predictor correlation plot, observed vs. predicted histogram, validation plot and variable importance plot. `5_LabileOM_model` also saves each map separately (`map_median.jpg`, `map_P5.jpg`, …, `map_DI.jpg`, `map_AOA.jpg`).

Final layers selected for delivery are collected in `results/` (`data - OCC`, `data - DBD`, `data - CRI`).

### Licensing

<p xmlns:cc="http://creativecommons.org/ns#" >This work is licensed under <a href="https://creativecommons.org/licenses/by-nc/4.0/?ref=chooser-v1" target="_blank" rel="license noopener noreferrer" style="display:inline-block;">CC BY-NC 4.0<img style="height:22px!important;margin-left:3px;vertical-align:text-bottom;" src="https://mirrors.creativecommons.org/presskit/icons/cc.svg?ref=chooser-v1" alt=""><img style="height:22px!important;margin-left:3px;vertical-align:text-bottom;" src="https://mirrors.creativecommons.org/presskit/icons/by.svg?ref=chooser-v1" alt=""><img style="height:22px!important;margin-left:3px;vertical-align:text-bottom;" src="https://mirrors.creativecommons.org/presskit/icons/nc.svg?ref=chooser-v1" alt=""></a></p>