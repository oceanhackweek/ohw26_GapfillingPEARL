# ohw26_GapfillingPEARL

## Project Name

Gapfilling the PEARL Dataset

## One-line Description

How accurately can missing PEARL observations be reconstructed under different outage types and gap lengths, and how much do external tide, atmospheric, satellite, and ocean-model data improve reconstruction when the PEARL system itself is unavailable?

## Collaborators

| Name                | Role                |
|---------------------|---------------------|
| Kaira      |  | 
| Participant 2       |  |
| Participant 3       |  |

## Planning

* Initial idea: "PEARL continuously collects data, but sometimes stops for different reasons (e.g. low tide). Use the data from before and after the gap to reconstruct the missing data, possibly with help from tide data and other external sources. (chinacreek data)"
* Ideation Slide: https://docs.google.com/presentation/d/1_KLEDpLLvtKpH3awDlZRAiOKuHzbEti4CWmhEykuCG8/edit?slide=id.g3f85357d4e2_6_0#slide=id.g3f85357d4e2_6_0 
* Final presentation: 

## Background

PEARL (Pacific Ecosystems Autonomous Research Lab) is hosted at the Bamfield
Marine Sciences Centre. It sits off a pier on the BMSC foreshore and runs a
flow-through continuous high-frequency sensing suite of oceanographic and
weather variables.

The system collects data continuously, but it sometimes stops. The reasons vary:
the pump cannot draw water at low tide, the power or logger goes down, or an
individual sensor fails. Each of these leaves a different kind of gap.

The version used here is PEARL 1.0, deployed 2024-05-17 to 2025-01-31, running
LabVIEW on the CUBE computer. 

## Goals

* Work out what is missing, how often, and why
* Separate the different outage types, since they behave differently:
  * whole-system outages, where everything including the weather station stops
  * water-only outages, where the pump stops but the weather station keeps running
  * single-instrument failures, where one sensor breaks while the rest of the line is fine
* Use linear interpolation as the baseline that every other method has to beat
* Test whether a GAM (generalised additive model) can beat that baseline
* Measure how reconstruction accuracy changes with gap length
* Measure how much external data (tide, atmospheric, satellite, ocean model) adds
  when PEARL itself is unavailable

## Datasets

**PEARL**

`pearl10_20240517-20250131_QC_clean_1min_avg.nc` — the whole PEARL 1.0
deployment in one file, one row per minute, May 2024 to January 2025. This is
the cleaned product: bad values were already flagged and removed, the remainder
averaged from 6-second to 1-minute resolution, and the QC flags dropped.

Variables used: water temperature, salinity, dissolved oxygen, chlorophyll
fluorescence (695 nm), backscatter (470 nm and 700 nm), flow rate, PAR, air
temperature, wind speed and direction, relative humidity, atmospheric pressure.

The `01_QC_flagged` version of the same data is also available. It keeps the
per-test quality flags, which say why each value was removed. Not used yet,
but needed if the outage types are to be separated by mechanism.

**Tide**

Bamfield tide station 08545 (Canadian Hydrographic Service), pulled from the
IWLS API at 1-minute resolution. The station is at 48.836, -125.136, which is
the same location as PEARL.

Predicted water level (`wlp`) is used rather than observed. Predictions are
calculated from the moon and sun rather than measured, so they exist even when
PEARL is switched off, which is exactly when they are needed.

**Other external data (to look at)**

* MEaSUREs MUR SST, 1 km: https://podaac.jpl.nasa.gov/MEaSUREs-MUR
* CIOPS-West, 2 km: https://open.canada.ca/data/en/dataset/390abee6-4ba0-4d6e-ae79-25753d1c43f3
* CHINACREEK DATASET

Open question: whether any of these can be matched to PEARL's 1-minute
resolution. Tide predictions are currently the only external source available at
1 minute.

## Workflow/Roadmap

1. Pull the PEARL data from Google Drive
2. Pull the tide data from the Bamfield station
3. China Creek salinity and oxygen data 
4. Pull any leftover remote sensing data
5. Analysis of the data
   * correlation matrix of what is missing and why it is missing
   * what is missing most often
   * analysis of the gaps: how long they are, when they happen, and where the
     big gaps are
6. Models
   * linear interpolation, the baseline everything is compared to
   * GAM (generalised additive model)
   * one of KNN / MICE / MissForest
   * brief mention of the EM algorithm approach already used on a later PEARL
     dataset

All methods are tested the same way: take a stretch of complete data, cut an
artificial gap of a known length, fill it, and compare against the real values
that were removed. This is repeated at many places across the record and scored
with RMSE.

## Results/Findings

## Lessons Learned

## References
