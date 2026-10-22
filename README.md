# Tsetse Fly Data Cleaning

Cleaning and validating the 2013 tsetse fly (*Glossina morsitans morsitans*) survey data from Eastern Province, Zambia, using Python and Pandas.

I did not collect this data. This repository contains only my cleaning work. Credit for the data goes to the original authors and the data centre listed below.

## Data source

Anderson, N.E.; Mulenga, G.; Mashili, J.; Simuunza, M.; Machila, N.; Atkinson, P.M.; Welburn, S.C. (2017). *Tsetse (Glossina morsitans morsitans) count data from Eastern Province, Zambia, (2013)*. NERC Environmental Information Data Centre. https://doi.org/10.5285/59fc6e1a-e1cb-4bcf-8b49-68211b2a363a

## Licence and attribution

The original data is available under the [Open Government Licence v3.0](https://www.nationalarchives.gov.uk/doc/open-government-licence/version/3/).

> Contains data supplied by UK Centre for Ecology & Hydrology.

The raw file is included here under that licence, with the attribution above. If you reuse the data, follow the licence terms and cite the source.

## About the data

- **What it is:** tsetse fly counts recorded at survey stops in Eastern Province, Zambia.
- **Collection dates:** 29 May to 6 June 2013 and 11 to 19 November 2013.
- **Size:** 4,497 rows and 10 columns in the original file.

| Column | Description |
|---|---|
| `Code` | Unique row number |
| `Survey` | Survey round (`One` or `Two`) |
| `ID_Survey` | Record number within the survey |
| `BFR` | Survey code from the source data, kept as provided (values 1 to 173) |
| `Stop` | Stop number from the source data (values 0 to 31) |
| `Latitude` | Latitude in decimal degrees (renamed from `Latitude_`) |
| `Longitude` | Longitude in decimal degrees (renamed from `Longitude_`) |
| `Date` | Date of the record (converted to a datetime) |
| `Result` | 1 if flies were caught, 0 if none |
| `Count` | Number of flies counted |

## Problems found

- **Missing coordinates:** 113 rows (about 2.5%) had no latitude or longitude. The missing values were always in the same rows, and no other column had missing values.
- **A stray value in `Result`:** one row had `S` instead of `0` or `1`. Its `Count` was 0.
- **Wrong data types:** `Date` was stored as text in day/month/year format, and `Result` was stored as text.
- **Awkward column names:** `Latitude_` and `Longitude_` had trailing underscores.

## Cleaning steps

1. **Loaded the data** and checked its size (4,497 rows, 10 columns) and column names.
2. **Checked missing values** as a percentage per column. Only `Latitude_` and `Longitude_` had missing values (2.51% each).
3. **Dropped the 113 rows with missing coordinates.** Both coordinates were missing in the same rows, and no other column was affected. This leaves 4,384 rows. I dropped them so every remaining record can be mapped.
4. **Confirmed no missing values remained.**
5. **Checked for duplicate rows.** None were found.
6. **Inspected the unique values** of every column, which is how I found the stray `S` in `Result`.
7. **Fixed the stray `S`** by treating it as a typo for `0`, because its `Count` was 0. I then converted `Result` to an integer.
8. **Converted `Date`** from text (`dd/mm/yyyy`) to a proper datetime.
9. **Renamed** `Latitude_` and `Longitude_` to `Latitude` and `Longitude`.
10. **Saved the cleaned data** as `cleaned_tsetse_data.csv`.

## Before and after

| | Before | After |
|---|---|---|
| Rows | 4,497 | 4,384 |
| Columns | 10 | 10 |
| Rows with missing coordinates | 113 (2.51%) | 0 |
| `Result` values | `0`, `1`, `S` (text) | `0`, `1` (integer) |
| `Date` type | text | datetime |

After cleaning, `Result` is 0 in 4,205 rows and 1 in 179 rows. Every row with `Result = 1` has a `Count` of 1 or more, and every row with `Result = 0` has a `Count` of 0.

## Limitations

- The 113 rows with missing coordinates were removed so that all records can be mapped. Their counts are still valid, so anyone doing a non-spatial analysis may prefer to use the original file instead.
- Treating `S` as `0` is an assumption based on its `Count` of 0.

## Files

- `cleaned_tsetse.ipynb`: the cleaning notebook, with an explanation of every step
- `DDDAC_tsetse_2013.csv`: the original data
- `cleaned_tsetse_data.csv`: the cleaned dataset

To rerun the notebook, keep all three files in the same folder and run it with Jupyter.

## Tools

Python, Pandas, NumPy, Jupyter Notebook

## Author

Samuel Chibinji Mwanza, Computer Science (Software Engineering), University of Zambia
