# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is the KIDS23 Time-Dependent Covariate Survival Visualization App (TDC Vis App), created during the St. Jude KIDS23 BioHackathon by Team 2. The app provides an intuitive interface for interpreting survival analysis results with time-varying covariates, implementing the methodology described by Jay & Betensky (2021).

## Running the Application

### Main Application

The primary Shiny app is `tdc_app_aes.R`. To run:

```r
# Required dependencies must be installed first
source("tdc_app_aes.R")
```

### Required R Packages

- `shiny` - Shiny web framework
- `survival` - Survival analysis functions
- `survminer` - Visualization for survival analysis
- `tidyverse` - Data manipulation
- `fresh` - Shiny theming
- `shinythemes` - Additional Shiny themes
- `shinyWidgets` - Enhanced Shiny widgets
- `dplyr` - Data manipulation
- `DT` - Interactive tables
- `RColorBrewer` - Color palettes
- `ggpubr` - Publication-ready plots
- `gridExtra` - Arranging multiple plots
- `cowplot` - Plot composition

### Required Assets

The app requires a `www/` directory in the same location as the Shiny app containing:
- `SJ_Tag_H_C_PNG.Jpg` - St. Jude logo
- `mythemesj.css` - Custom CSS theme
- `Rosalie.jpg` - Team photo
- `data_example.csv` - Example dataset for demonstrations

## Core Architecture

### Key Functions

**survfitCP()** (`CWu/survfitCP.R` and `SIM_8888_function_survfitCP.R`)
- Implements survival estimators from Jay & Betensky (2021)
- Predicts survival curves for user-specified trajectories of time-varying covariates
- Parameters:
  - `data`: Data frame with columns: id, start, stop, event status, discrete time-varying covariate, additional covariates
  - `covariate_vals`: Vector of covariate values on path (default: c(0,1))
  - `transition_times`: Vector of times at which covariate value switches
  - `weights`: Boolean for using stabilized weights (default: FALSE)
  - `conf_level`: Confidence level for intervals (default: 0.95)
- Returns: Data frame with columns t, n, d, surv, lower_ci, upper_ci

**tMergeToConventional()** (`functionTmergeToConventional.R`)
- Converts tmerge-formatted data (multiple rows per subject with time intervals) to conventional survival data format
- Input: Data with columns id, tstart, tstop, endpt, and optional time-varying covariates
- Output: One row per subject with final covariate values and renamed columns (Y for event time, D for event indicator)

### Data Format Requirements

The app expects CSV files with columns in this specific order:
1. `id` - Subject identifier
2. `start` - Interval start time
3. `stop` - Interval stop time
4. `event` - Event status indicator (0/1)
5. `tdc` - Binary time-dependent covariate (0/1)
6. Additional covariates (optional, for stabilized weights)

Data must be in "counting process" format created by the `tmerge()` function from the survival package. This creates multiple rows per subject representing different time intervals where covariate values change.

### Application Structure

The Shiny app has three main tabs:

1. **DESCRIPTION**: About the app, team info, methodology references
2. **DATA**: File upload, variable selection, data preview, example data toggle
3. **PLOT**: Single survival curve with adjustable transition time, CI toggle, weight adjustment, downloadable plot
4. **COMPARISON PLOT**: Compare multiple transition times (up to 10), user-specified comma-separated values

### Key Limitations

- Currently supports only binary (0,1) time-dependent covariates
- Only supports one-way transitions (0 to 1, not back to 0)
- Transition time represents when the group transitions from covariate value 0 to value 1

## Related Projects

### ShinyCox Example

Located in `ShinyCoxExample_2023-05-02/`, this is a related previous project for Cox model survival predictions. To run the example:

1. Download files: `cox-predictions.R`, `shiny-blocks.R`, `shiny-builder.R`, `test-shiny-Cox.R`
2. Update file paths in `test-shiny-Cox.R`
3. Create a new folder for `app.dir`
4. Run: `source("test-shiny-Cox.R")`
5. Open `shinyCoxapp.R` in the generated `app.dir` folder
6. Click "Run App" in RStudio

Required packages: `survival`, `shiny`, RStudio IDE

## References

1. Jay M, Betensky RA. Displaying survival of patient groups defined by covariate paths: Extensions of the Kaplan-Meier estimator. Statistics in Medicine. 2021 Apr 15;40(8):2024-36.
2. Therneau T, Crowson C, Atkinson E. Using time dependent covariates and time dependent coefficients in the Cox model. Survival Vignettes. 2023 March 11;1-30.
3. R survival package documentation: https://cran.r-project.org/web/packages/survival/
4. tmerge documentation: https://www.rdocumentation.org/packages/survival/versions/3.5-5/topics/tmerge

## Project Goals

Primary: Demo that can upload data and plot survival probability for time-varying covariates

Secondary:
- User-specified trajectories
- Customizable plot elements (labels, legend, colors)
- Option to show invalid use of time-varying covariate as baseline
- Support for arbitrary data names

Tertiary:
- Non-martingale representation for input data
- Support for arbitrary class of time-varying covariates
