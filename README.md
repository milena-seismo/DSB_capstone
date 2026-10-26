# DSB_capstone
# Machine Learning for Seismic Catalogue Completeness and Aftershock Analysis

## 1. Project Overview

This project investigates whether machine learning can improve the completeness of seismic earthquake catalogues by detecting additional events in continuous seismic waveform data.

The study focuses on seismic activity in Montenegro during 2024, with particular attention to the **M 5.3 earthquake that occurred on 14 March 2024 near Čarađe, Montenegro**, and its subsequent aftershock sequence.

The main objective is to compare manually processed earthquake records with automatically detected seismic events and investigate whether machine learning can identify smaller earthquakes that may have been missed during routine seismic processing.

### Research Question

**Can machine learning improve the completeness of seismic catalogues and support a more detailed analysis of aftershock sequences and seismogenic fault structures?**

## 2. Data Collection

The primary dataset is the **2024 earthquake catalogue provided by the Sector for Seismology of the Institute of Hydrometeorology and Seismology (IHMS), Montenegro**.

The catalogue contains:

- Earthquake origin times
- Epicentral coordinates (latitude and longitude)
- Focal depths
- Earthquake magnitudes
- Origin-time and hypocentral location uncertainties
- Number of stations (NST)
- Azimuthal gap (GAP)

The dataset is available in CSV format in the project repository.

The catalogue will serve as a reference dataset for comparison with automatically detected seismic events.

Continuous seismic waveform recordings will be used in the next stage of the project.

## 3. Data Inspection and Cleaning

The original catalogue contained **2,293 earthquake records and 16 variables**.

The preliminary inspection included checking data types, missing values, duplicate records, and potentially unreliable seismic parameters.

The following findings were identified:

- No missing values were detected during the initial inspection.
- One exact duplicate earthquake record was identified and removed.
- Five records have a reported magnitude of 0.0.
- Eighty records have a focal depth of 0 km.
- A total of 156 records have a depth uncertainty of exactly 11.1 km.
- Two records have a focal depth of 33 km, which is a default depth configured in the seismic processing system.

The repeated uncertainty values and zero-depth records indicate potential limitations in hypocentral depth determination.

These records were retained for further quality assessment rather than automatically removed.

After removing the duplicate, **2,292 unique earthquake records** remained.

## 4. Geographical Selection

The original catalogue covers a wider geographical region, including earthquakes outside Montenegro.

For the preliminary exploratory analysis, a geographical filter was applied using the following coordinate limits:

- **Longitude:** 18°–22° E
- **Latitude:** 41°–44° N

This selection reduced the dataset from 2,292 to **1,824 earthquake records**.

The selected rectangular region includes Montenegro and surrounding areas. It does not represent the exact national boundaries.

Further geographical and temporal selection will be required to isolate the Čarađe aftershock sequence.

## 5. Exploratory Data Analysis (EDA)

The preliminary EDA examined earthquake magnitudes, monthly seismic activity, and spatial epicentral distribution.

### Magnitude Distribution

The 1,824 earthquakes within the selected geographical region were grouped into the following magnitude intervals:

| Magnitude interval | Number of earthquakes |
|---|---:|
| 0 ≤ M < 1 | 52 |
| 1 ≤ M < 2 | 1,112 |
| 2 ≤ M < 3 | 589 |
| 3 ≤ M < 4 | 66 |
| 4 ≤ M < 5 | 4 |
| 5 ≤ M < 6 | 1 |
| **Total** | **1,824** |

Most recorded earthquakes (93.3%) have magnitudes between 1.0 and 3.0.

Only 52 earthquakes have magnitudes below 1.0. This observation raises questions about the detection capabilities and completeness of the existing catalogue, but does not by itself establish the magnitude of completeness.

### Temporal Distribution

The highest number of earthquakes was recorded in **March 2024, with 476 events**, followed by April with 269 events.

This increase is consistent with the occurrence of the Čarađe earthquake and its aftershock activity.

However, the selected geographical region includes other seismic sources, so further analysis is needed to identify events belonging specifically to the Čarađe sequence.

### Spatial Distribution

The spatial analysis shows concentrations of earthquake epicenters within Montenegro and the surrounding region.

The Čarađe M 5.3 earthquake was highlighted on the epicentral distribution plot.

The preliminary spatial analysis provides a basis for selecting a more specific study area for the investigation of aftershocks and potentially active seismogenic structures.

## 6. Planned Machine Learning Approach

The next stage will investigate automatic seismic event detection and phase picking using continuous waveform recordings.

A potential approach is to apply a **pretrained EQTransformer deep learning model through the SeisBench Python library**.

EQTransformer can be used to detect seismic signals and identify P- and S-wave arrivals.

Additional processing will be required to associate phase arrivals, determine earthquake locations, and produce a catalogue suitable for comparison.

The automatically detected events will be compared with the manually processed reference catalogue to assess whether additional earthquakes can be identified.

The final model and processing workflow will be evaluated during the next project stage.

## 7. Preliminary Conclusions

The preliminary analysis reduced the original dataset from **2,293 records to 2,292 unique earthquakes**, followed by geographical selection of **1,824 events**.

The analysis identified data-quality issues, examined the distribution of earthquake magnitudes, and highlighted increased seismic activity during March 2024.

The relatively small number of recorded low-magnitude earthquakes motivates further investigation of catalogue completeness.

The next phase will focus on continuous waveform analysis and evaluate whether machine learning can improve the detection of small earthquakes, particularly within the Čarađe aftershock sequence.

## 8. Project Status

**Completed:**
- Data collection and loading
- Initial data inspection
- Duplicate removal
- Preliminary data-quality assessment
- Geographical filtering
- Magnitude, temporal, and spatial EDA

**Planned:**
- Selection of the detailed Čarađe study area
- Preparation of continuous seismic waveform data
- Automated seismic event detection and phase picking
- Event association and catalogue construction
- Comparison with the manually processed catalogue
- Assessment of catalogue completeness and aftershock characteristics
