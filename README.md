# Detroit Urban Farm Suitability

Ranks vacant, land-bank-owned lots in Detroit for urban agriculture by combining
lot size, food access, and proximity to EPA-tracked cleanup sites.

**[Interactive map](https://faza-dzaki.github.io/detroit-urban-farm-suitability/)**

![Map screenshot](docs/screenshot.png)

## Question
Which vacant lots in Detroit are the best candidates for urban farming, given
that large lots in food deserts, far from known contamination, are most useful?

## Data
| Dataset | Source | Used for |
|---|---|---|
| DLBA Owned Properties | Detroit Open Data | Lot inventory (55,690 properties, 51,843 vacant lots after filtering) |
| Parcels (Current) | City of Detroit, Office of the Assessor | Lot size (acres) |
| Food Access Research Atlas (2019) | USDA ERS | Low-income / low-access census tracts (2010 boundaries) |
| Census tract boundaries (2019 cartographic files) | US Census Bureau | Joining the Atlas to lots |
| Cleanups in My Community | US EPA | Brownfield, Superfund, RCRA and response sites (925 in Wayne County) |

Data pulled on 25 Nov 2026. EPA and parcel data are live services and change over time.

## Method
1. Kept lot-type DLBA properties; excluded structures, side lots, improved lots
   and accessory-structure lots.
2. Spatial join of lots to census tracts to get food access flags
   (6,150 lots fall in low-income, low-access tracts).
3. Distance from each lot to the nearest EPA cleanup site (projected to UTM 17N
   so distances are in true meters).
4. Scored each lot from 0 to 100:

| Criterion | Rule | Weight |
|---|---|---|
| Lot size | Linear up to 0.5 acre, capped at 1 | 40% |
| Food access | 1 if low-income and low-access tract, 0.5 if low-income only, else 0 | 30% |
| Contamination proxy | 0 if within 100 m of an EPA site, 0.5 within 250 m, else 1 | 30% |

Weights and thresholds are my own design choices, not an established standard.

## Results
[One or two sentences: where the top 50 are, which neighborhoods dominate,
typical size. Fill in from your own output.]

Top 50 list: [`outputs/top50_lots.csv`](outputs/top50_lots.csv)

## Limitations
- EPA data only covers sites reported to EPA programs. It is not a complete
  inventory of contaminated land, so distance to an EPA site is a rough proxy.
- Stepwise distance thresholds create cliff effects (a lot at 249 m is
  penalized, one at 251 m is not).
- Parcel size comes from the assessor file. Very large lots may be merged
  parcels and should be checked on satellite imagery.
- Ownership and sale status change often. "DLBA Owned Lot" does not mean the
  lot is currently for sale.
- Soil drainage (SSURGO) and sunlight (LiDAR shadow analysis) are not included yet.

## Reproduce
Python 3.10+, `pip install pandas geopandas folium jupyter matplotlib openpyxl`.
Download the datasets above into `data/`, then run `notebooks/01_explore.ipynb`
top to bottom.
