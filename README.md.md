# Police Force Analytics - Crime Data Pipeline

**Author:** Maria Motter | **Date:** 20/05/2026 | **Version:** v1.0   | **Github:** [Crime Data Pipeline](https://github.com/vcmeamaria/Crime-Data-Pipeline)

---

## Overview

A Python data engineering pipeline that ingests, cleans, enriches, aggregates, and exports UK street-level crime data for four police forces, producing a single BI-ready reporting dataset for use in Power BI.

**Forces covered:** Metropolitan Police Service, West Midlands Police, West Yorkshire Police, Devon & Cornwall Police

**Time period:** April 2023 -> March 2026 (36 monthly batches)

**Reporting grain:** `force_name x month_period x crime_type`

The pipeline processes approximately 1.3 GB of raw data and joins three external enrichment datasets to contextualise crime volumes with population size, deprivation levels, and crime severity weights.

---

## Project Structure

```
police-force-analytics/
|
|--crime-data/                   # Raw monthly crime CSVs from police.uk
|   --- YYYY-MM/                 # One folder per month (36 folders)
|       --- <force>-street.csv   # One file per force per month
|
|-- extra-data/                   # Enrichment datasets
|   |--- imd2019lsoa.xlsx         # IMD 2019 LSOA-level deprivation scores
|   |--- ons_population.xlsx      # ONS Mid-Year Population Estimates (LAD level)
|   |--- crime_severity_score.ods # Home Office Crime Severity Score Data Tool
|
|--- outputs/
|   |--- crime_reporting_dataset.csv   # Final BI-ready reporting output
|
|--- pipeline.ipynb               # Main Jupyter Notebook (end-to-end pipeline)
|--- README.md                    # This file
|--- Statement_of_Work.docx       # Project scoping document
|--- data_dictionary              # Full column-level data dictionary
|--- workflow_diagram.html        # Pipeline workflow diagram 
```

---

## Requirements

Python 3.9+ is recommended. The following libraries must be installed:

```bash
pip install pandas numpy openpyxl xlrd IPython
```

| Library | Purpose |
|---|---|
| `pandas` | Core data manipulation, joining, aggregation |
| `numpy` | Numerical operations (e.g. `ceil` for quarter derivation) |
| `pathlib` | File system traversal for batch ingestion |
| `re` | String cleaning and standardisation |
| `xlrd` | Reading legacy `.xls` files |
| `openpyxl` | Reading `.xlsx` enrichment files |
| `IPython` | Display utilities within the notebook |
---

## How to Run

1. **Prepare raw data:** Download monthly crime CSVs from [data.police.uk](https://data.police.uk/data/) for the four forces across April 2023 – March 2026. Place each month in its own folder under `crime-data/YYYY-MM/`.

2. **Enrichment data:** Download the three enrichment datasets (see table below) and place them in `extra-data/`.

3. **Open the notebook:** Launch `pipeline.ipynb` in Jupyter Notebook or JupyterLab.

4. **Run all cells:** Execute the notebook from top to bottom (`Kernel -> Restart & Run All`). The pipeline runs end-to-end through all sections.

5. **Retrieve output:** The final reporting dataset is written to `outputs/crime_reporting_dataset.csv`.
---

## Enrichment Datasets

| Dataset | Source | Why Chosen | Output Column |
|---|---|---|---|
| **ONS Mid-Year Population Estimates** (LAD level, 2021 geography) | [ONS Population Estimates](https://www.ons.gov.uk/peoplepopulationandcommunity/populationandmigration/populationestimates/datasets/populationestimatesforukenglandandwalesscotlandandnorthernireland) | Enables normalisation of crime counts to a per-capita rate, allowing fair comparison across forces of very different sizes (e.g. MPS vs Devon & Cornwall) | `force_population`, `crimes_per_1000` |
| **Index of Multiple Deprivation 2019** (LSOA level, MHCLG) | [English Indices of Deprivation 2019](https://www.gov.uk/government/statistics/english-indices-of-deprivation-2019) | Provides LSOA-level socioeconomic context, enabling analysts to explore the relationship between deprivation and crime volumes or types. Most granular publicly available deprivation measure for England. | `avg_imd_decile` |
| **Home Office Crime Severity Score Data Tool** | [ONS Crime Severity Score](https://www.ons.gov.uk/peoplepopulationandcommunity/crimeandjustice/datasets/crimeseverityscoredatatool) | Weights crime categories by severity, allowing comparison beyond raw counts. Static Apr 2015–Mar 2016 baseline used as the most recent available year for consistent cross-force comparison. | `severity_score_baseline` |

---

## References

1. UK Police Crime Data. *data.police.uk*. https://data.police.uk/data/

2. Ministry of Housing, Communities & Local Government (2019). *English Indices of Deprivation 2019*. https://www.gov.uk/government/statistics/english-indices-of-deprivation-2019

3. Office for National Statistics (2023). *Population estimates for the UK, England, Wales, Scotland, and Northern Ireland — Mid-year estimates*. https://www.ons.gov.uk/peoplepopulationandcommunity/populationandmigration/populationestimates/datasets/populationestimatesforukenglandandwalesscotlandandnorthernireland

4. Office for National Statistics. *Crime Severity Score Data Tool*. https://www.ons.gov.uk/peoplepopulationandcommunity/crimeandjustice/datasets/crimeseverityscoredatatool

5. Office for National Statistics. *UK Population timeseries (UKPOP)*. https://www.ons.gov.uk/peoplepopulationandcommunity/populationandmigration/populationestimates/timeseries/ukpop/pop

---

## AI Disclosure

AI tools were utilised in this project for code debugging, code improvement, and LAD-to-PFA mapping derivation. All pipeline logic, dataset choices, and analytical decisions were made by the author.

