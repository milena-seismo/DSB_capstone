# DSB Capstone
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

- ## 9. Detailed Study Area Selection and Catalogue Quality Assessment

### 9.1. Selection of the Čarađe Study Area

Following the preliminary exploratory data analysis, the investigation was narrowed to the aftershock sequence associated with the **M 5.3 earthquake of March 14, 2024, near Čarađe, Montenegro**.

A circular study area with a **30 km radius** was defined around the mainshock epicentral region.

The reference center used for the spatial selection was approximately:

- **Latitude:** 42.974° N
- **Longitude:** 18.661° E
- **Radius:** 30 km

The spatial selection was performed using geographical distances between earthquake epicenters and the reference center.

This approach provides a more focused dataset for investigating the spatial and temporal evolution of the Čarađe aftershock sequence.

The selection resulted in **906 earthquakes recorded during 2024** within the defined radius.

### 9.2. Temporal Selection of the Aftershock Sequence

For the initial machine learning experiment, the analysis was further restricted to the period:

**March 14–20, 2024 (inclusive)**

This seven-day interval includes the mainshock and the subsequent period of increased seismic activity.

A total of **337 earthquakes** were identified within the selected spatial and temporal limits.

The daily distribution was:

| Date | Number of earthquakes |
|---|---:|
| March 14, 2024 | 131 |
| March 15, 2024 | 44 |
| March 16, 2024 | 45 |
| March 17, 2024 | 44 |
| March 18, 2024 | 32 |
| March 19, 2024 | 20 |
| March 20, 2024 | 21 |
| **Total** | **337** |

The selected catalogue provides the reference dataset for the initial comparison with machine learning detections.

### 9.3. Review of Zero-Depth and Zero-Magnitude Records

During catalogue quality assessment, special attention was given to earthquake records containing potentially uncertain or default parameter values.

The original catalogue included:

- **80 earthquakes with a reported focal depth of 0 km**
- **5 earthquakes with a reported magnitude of 0.0**
- **156 records with a depth uncertainty of exactly 11.1 km**
- **2 earthquakes with a focal depth of 33 km**, corresponding to a default depth configured in the seismic processing system

These records were examined as part of the catalogue quality-control process.

A reported depth of 0 km does not necessarily represent a physically determined surface earthquake. It may reflect limitations or constraints in hypocentral depth determination.

Similarly, a magnitude value of 0.0 requires careful interpretation and should not automatically be treated as an invalid earthquake record.

Repeated depth uncertainty values and default focal depths indicate that some catalogue parameters may reflect processing conventions rather than independently constrained solutions.

**Decision:** All these records were retained in the reference catalogue.

No earthquakes were excluded solely because of zero focal depth, zero magnitude, repeated depth uncertainty values, or default depth assignments.

This decision preserves the original catalogue information and avoids introducing additional selection bias before the machine learning comparison.

The identified parameter limitations will be considered during subsequent quality assessment, event association, and interpretation of the results.

### 9.4. Final Reference Dataset for the Pilot Study

The selected reference catalogue contains:

| Parameter | Result |
|---|---|
| Study region | Čarađe, Montenegro |
| Spatial selection | 30 km radius |
| Annual catalogue within radius | 906 earthquakes |
| Pilot analysis period | March 14–20, 2024 |
| Earthquakes in pilot period | 337 |
| Mainshock magnitude | M 5.3 |
| Minimum recorded magnitude in pilot catalogue | M 0.5 |
| Maximum recorded magnitude | M 5.3 |

This dataset will serve as the reference catalogue for evaluating the performance of automated earthquake detection and phase picking.

The next stage focuses on preparing continuous seismic waveform recordings and applying the pretrained EQTransformer model.

## 10. Continuous Seismic Waveform Data Preparation

### 10.1. Waveform Data Collection

Following the selection of the Čarađe study area and the seven-day aftershock sequence, continuous seismic waveform recordings were prepared for machine learning analysis.

The waveform data originate from the seismic monitoring network operated by the Institute of Hydrometeorology and Seismology (IHMS), Montenegro.

The selected period covers **March 14–20, 2024**, corresponding to Julian days 074–080 of 2024.

Continuous recordings were extracted from the original seismic waveform archive and copied to a separate working directory on an external storage device.

The original recordings were preserved without modification.

### 10.2. Selection of Seismic Stations and Components

Seven seismic stations were included in the initial waveform dataset.

| Station | Available components | Number of daily files |
|---|---|---:|
| BRY | EHZ | 7 |
| CEME | HHZ, HHN, HHE | 21 |
| DRME | HHZ, HHN, HHE | 21 |
| HCY | EHZ | 7 |
| KOME | HHZ, HHN, HHE | 21 |
| NKME | EHZ | 7 |
| UPM | EHZ | 7 |
| **Total** | **13 channels** | **91** |

The selected waveform dataset contains approximately **1.65 GB of MiniSEED data**.

Three-component broadband recordings are available for CEME, DRME and KOME.

The remaining four stations provide single-component vertical recordings in the selected dataset.

The initial EQTransformer experiment was restricted to CEME because its three-component recordings are compatible with the pretrained model's input requirements.

Single-component stations will be considered in subsequent processing using an appropriate detection or phase-picking approach.

### 10.3. Data Storage and Processing Environment

Waveform files were retained on an external storage device because the complete seismic archive is too large for practical storage in the project GitHub repository.

Google Colab was selected as the initial Python processing environment.

For the pilot experiment, three MiniSEED files were uploaded to the temporary Colab runtime:

- `ME.CEME..HHZ.D.2024.074`
- `ME.CEME..HHN.D.2024.074`
- `ME.CEME..HHE.D.2024.074`

The external archive remains the primary source of waveform data.

The GitHub repository is used for project documentation, notebooks, scripts and selected processing results rather than storing the complete continuous waveform dataset.

### 10.4. Loading Waveform Data Using ObsPy

The ObsPy Python library was used to read the three-component MiniSEED recordings.

The waveform data were loaded into an ObsPy Stream object for inspection and subsequent processing.

The following properties were confirmed:

- Three waveform traces corresponding to HHE, HHN and HHZ.
- Sampling frequency of **100 Hz** for each component.
- Approximately 24 hours of continuous recording per component.
- Approximately 8.64 million samples per component.

The initial continuity check using `get_gaps()` returned no reported gaps.

Additional inspection confirmed that the waveform arrays were not masked.

These checks established that the selected CEME recordings were suitable for the initial model experiment.

### 10.5. Mainshock Waveform Inspection

A two-minute waveform segment was extracted around the March 14, 2024 mainshock.

The segment extended from 30 seconds before to 90 seconds after the catalogue origin time:

**2024-03-14 03:06:46.300 UTC**

The three waveform components were visualized using ObsPy.

The original recordings showed clear seismic signals associated with the mainshock.

A low-frequency baseline variation was particularly noticeable on the HHE component.

To improve waveform visualization, a separate copy of the segment was processed using:

- Mean removal
- Linear detrending
- Bandpass filtering from 1 to 15 Hz
- Four filter corners
- Zero-phase filtering

The processed visualization provided a clearer representation of the seismic phases.

**The filtered waveform segment was used for visual inspection only.**

The original waveform data were retained for subsequent EQTransformer processing, allowing the pretrained model to apply its intended preprocessing procedures.

### 10.6. Preliminary Waveform Quality Assessment

The initial data inspection confirmed that:

- The selected MiniSEED files could be successfully read using ObsPy.
- The three CEME components had consistent sampling frequencies.
- No internal gaps were reported by the initial ObsPy continuity check.
- No masked waveform samples were identified.
- The mainshock signal was clearly visible on the three components.

These results supported proceeding with the pretrained EQTransformer model.

The inspection represents an initial waveform quality check rather than a complete assessment of instrumental response, timing accuracy or signal quality across the entire seismic network.

### 10.7. Preparation for Machine Learning Analysis

After completing waveform inspection, the analysis proceeded to automatic earthquake detection and P- and S-wave phase picking.

The pretrained **EQTransformer** model, implemented through the **SeisBench** Python library, was selected for the initial experiment.

The next stage evaluates model performance on:

1. A two-minute waveform segment containing the known M5.3 mainshock.
2. A continuous 30-minute waveform segment from the same station.

The objective is to verify the model's ability to identify known seismic signals before extending the analysis to longer recordings and additional stations.
