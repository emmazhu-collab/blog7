# Blog 7: Texas and California unemployment recovery

When did Texas and California return to their own pre-pandemic unemployment rates? This project compares January 2019 through December 2023 using two benchmarks: each state's 2019 average and February 2020 rate.

This repository contains the data, R code, calculated results, and final interactive chart for the [blog article](https://emmazhu-collab.github.io/website/blog/posts/post7/). The article itself stays in the website project.

## Files

```text
blog7/
├── blog7.Rproj
├── README.md
├── code/
│   ├── run_analysis.R       Run this to recreate the results and chart
│   ├── analysis.R           Functions for calculations and the Plotly chart
│   └── download_data.R      Optional download of a new FRED snapshot
├── data/source/
│   ├── unemployment_tx_ca.csv
│   └── series_metadata.csv
└── results/
    ├── tables/
    │   ├── recovery_summary.csv
    │   ├── recovery_gaps.csv
    │   ├── earlier_cutoff_check.csv
    │   └── benchmark_rankings.csv
    ├── interactive_recovery.html
    └── interactive_recovery_files/
```

Keep `interactive_recovery_files/` beside the HTML file. It contains the JavaScript and CSS needed to display the interactive chart. These are chart dependencies, not extra datasets.

## Data and method

The source is the U.S. Bureau of Labor Statistics through FRED: [TXUR](https://fred.stlouisfed.org/series/TXUR) for Texas and [CAUR](https://fred.stlouisfed.org/series/CAUR) for California. Both are monthly, seasonally adjusted unemployment rates in percent. The saved snapshot was downloaded on October 23, 2026. It contains 120 rows: 60 months for each state, with `date`, `state`, and `unemployment_rate` columns. The metadata file records the series details and download date.

For each state, the code finds its highest unemployment rate from March through December 2020. A first return is the first month after that peak with a rate at or below the selected benchmark. The code also identifies the first month of a run of three consecutive months at or below the benchmark. It checks shorter observation windows ending in December 2021 and December 2022.

The recovery gap equals the monthly rate minus that state's selected benchmark. It is measured in percentage points. A negative gap means the rate is below the benchmark.

## Replication instructions

Use R and open `blog7.Rproj` in RStudio. All commands below assume the working directory is the `blog7` project folder. The project was checked with R 4.5. Reproduction uses the included CSV files and does not need a FRED API key or a new download.

Install missing packages once in the **R Console**:

```r
install.packages(c("dplyr", "readr", "lubridate", "tibble", "tidyr", "plotly", "htmlwidgets"))
```

Then run this in the **R Console**:

```r
source("code/run_analysis.R")
```

Alternatively, run this in the **Terminal** from the project folder:

```sh
Rscript code/run_analysis.R
```

`analysis.R` defines functions; running it alone does not create the outputs. Use `run_analysis.R` for the complete process. It reads the saved data and writes four CSV tables plus the interactive HTML chart and its supporting folder. Running it again replaces those generated outputs. It does not change the source data.

Open `results/interactive_recovery.html` in a browser, or run this in the R Console:

```r
browseURL(normalizePath("results/interactive_recovery.html"))
```

There is no need to render an article to reproduce this project.

## Outputs and checks

- `recovery_summary.csv`: four state-benchmark combinations, peak rates, first return dates, and three-month checks.
- `recovery_gaps.csv`: 240 state-month-benchmark rows with rates, benchmarks, and gaps.
- `earlier_cutoff_check.csv`: eight checks using December 2021 and December 2022 cutoffs.
- `benchmark_rankings.csv`: return ordering under the two benchmarks.
- `interactive_recovery.html`: one Plotly chart with four options: Rate · 2019 average, Rate · February 2020, Gap · 2019 average, and Gap · February 2020.

Texas is blue and California is orange. Rate views show the original rates and dashed benchmark lines. Gap views show differences from the selected benchmark and a zero-reference line. Circles mark first returns. Hover text reports the month, rate, benchmark, gap, and the three-month check at return markers.

Both states peaked in April 2020: Texas at 12.8% and California at 16.1%. California first returned to its 2019 average (about 4.09%) in June 2022 and to its February 2020 rate (4.4%) in April 2022. Both dates also begin a qualifying three-month run. Texas did not reach either benchmark by December 2023. Neither state had an observed return by December 2021.

In April 2022, Texas had a lower rate than California (3.9% versus 4.3%). However, their gaps from February 2020 were +0.3 and -0.1 percentage points. A lower rate therefore does not necessarily mean a state is closer to its own benchmark.

## Optional fresh download

Keep the included snapshot to reproduce the article exactly. FRED can revise historical values, so a fresh download may change the results.

To intentionally replace the saved snapshot, install `fredr`, save your FRED key locally as `FRED_API_KEY` in `.Renviron`, and restart R. Do not put the key in code or Git. Then run from the project root in the R Console:

```r
install.packages("fredr") # Only if missing
source("code/download_data.R")
source("code/run_analysis.R")
```

The downloader replaces both source CSV files after checking series metadata, coverage, missing values, and duplicate state-month records. The key is read from the environment and is not printed or saved by the script.

## Limits

These results describe unemployment rates, not total jobs, overall economic recovery, or policy effects. The first observed return depends on the benchmark and study window. A three-month run does not mean the rate stayed below the benchmark afterward. A return not observed by December 2023 could occur later.
