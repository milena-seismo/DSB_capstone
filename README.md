# DSB Capstone
# Seismic Catalogue Completeness and Aftershock Detection — Čarađe, Montenegro

## Project Overview

This project examines the earthquake sequence following the **M 5.3 Čarađe earthquake on 14 March 2024**. The aim is to detect earthquakes in continuous seismic records, compare the detections with the manual catalogue, and identify possible catalogue additions.

The work has three parts:

1. **EDA — Catalogue Inspection and Study-Area Selection**
2. **Modeling — Time-Series Detection and Multi-Station Processing**
3. **Model Evaluation — Catalogue Comparison, Validation, and Confusion Matrix**

The main method is a custom MAD detector applied to seismic time series. EQTransformer was tested as a comparison on CEME. The final seven-station workflow uses the custom detector.

The work is documented in the EDA and Modeling notebooks. A separate candidate-review notebook supports visual inspection of the unmatched detections.

## PART I — Exploratory Data Analysis

### Data and Initial Inspection

The 2024 earthquake catalogue was provided by the **Sector for Seismology, Institute of Hydrometeorology and Seismology (IHMS), Montenegro**.

It contains origin times, epicentral coordinates, depths, magnitudes, location uncertainties, station counts, and azimuthal gaps.

The original dataset contained **2,293 records and 16 variables**. One exact duplicate was removed, leaving **2,292 unique records**. No missing values were found in the initial inspection.

The quality check identified:

- 80 records with a depth of 0 km.
- Five records with magnitude 0.0.
- 156 records with depth uncertainty of 11.1 km.
- Two records with a depth of 33 km, associated with a default processing depth.

These values were reviewed before further analysis. Within the Čarađe study area, zero depths were assigned a fixed value of **10 km** after manual checking. This is an assumed depth. One zero-magnitude event in the study area was also checked and corrected using the seismic analysis software.

### Regional Selection and EDA

The initial geographical selection covered **18–22° E and 41–44° N**, retaining **1,824 events**. This region includes Montenegro and surrounding areas.

Of these events, **1,701 (93.3%)** had magnitudes from 1.0 to below 3.0. Only **52** had magnitudes below 1.0. March had the highest monthly count, with **476 events**, followed by April with **269**.

Magnitude, daily and monthly counts, and epicentral distributions were examined to describe the seismic activity and select the detailed study area.

### Čarađe Study Area

The detailed study area is a circle with a **30 km radius** around the published mainshock epicentre:

| Parameter | Value |
|---|---|
| Latitude | 42.974° N |
| Longitude | 18.661° E |
| Radius | 30 km |
| Selected events during 2024 | 906 |

Distances were calculated with the **Haversine formula**. The same circle was used later to filter estimated event locations.

### Initial Aftershock Period

The initial catalogue analysis covered **14–20 March 2024** and contained **337 events**.

| Date | Events |
|---|---:|
| 14 March | 131 |
| 15 March | 44 |
| 16 March | 45 |
| 17 March | 44 |
| 18 March | 32 |
| 19 March | 20 |
| 20 March | 21 |
| **Total** | **337** |

Magnitudes ranged from **0.5 to 5.3**, with a median of **1.7**. There were **231 events below magnitude 2.0**.

The Gutenberg–Richter distribution was examined. The Maximum Curvature method gave a preliminary magnitude of completeness of **Mc = 1.3**. This was an initial estimate for the selected period. The project did not calculate a revised Mc for the additional detections, because their magnitudes were not determined.

## PART II — Modeling and Time-Series Analysis

### Waveform Data

Continuous MiniSEED records were obtained from the seismic archive. StationXML supplied station coordinates and elevations valid for the waveform date.

| Station | Available components |
|---|---|
| BRY | EHZ |
| CEME | HHZ, HHN, HHE |
| DRME | HHZ, HHN, HHE |
| HCY | EHZ |
| KOME | HHZ, HHN, HHE |
| NKME | EHZ |
| UPM | EHZ |

ObsPy was used to read and inspect the records. Daily files were checked for availability, and waveform segments separated by gaps were processed independently.

The work started with individual CEME event windows. It was then extended to the full day of **14 March** and to all seven stations for **15–31 March**. Waveforms were stored on Google Drive; notebooks and documentation were maintained separately.

### Initial P and S Tests on CEME

For P detection, absolute differences between consecutive HHZ samples were compared with a threshold based on the median and **Median Absolute Deviation (MAD)**.

For S detection, the HHN component was examined using short-term energy, RMS, and STA/LTA. Moving averages, Hann and Savitzky–Golay smoothing, kurtosis, and frequency filters were also tested.

On the first ten-event check, the P method returned a candidate for every event, with a median absolute timing error of **0.435 s**. S errors were larger and varied between events. The RMS method performed better than the tested STA/LTA rule.

In the continuous CEME scan, nine of these ten reference P arrivals had a candidate within **±0.5 s**. A separate ten-event set was used for further checks.

EQTransformer was tested through SeisBench on CEME event windows and on the full day. It provided an external comparison. The final multi-station method used **P picks from the custom MAD detector**.

### Continuous MAD P Detection

The detector was applied independently to each station's raw vertical waveform.

1. Calculate absolute amplitude differences between consecutive samples.
2. Use the preceding **5 s** to estimate the median and MAD.
3. Update the threshold every **1 s**.
4. Select samples above the threshold.
5. Group crossings separated by at most **0.5 s**, keeping the first crossing.

The threshold was:

$$
T = \mathrm{median} + 6 \times 1.4826 \times \mathrm{MAD}.
$$

The output is a list of P candidates in UTC. A threshold crossing can also be caused by noise or a later part of a waveform, so multi-station processing is required.

### Travel-Time Baseline and Association

The locations and depths of **131 known events from 14 March** supplied trial source positions. Direct P travel times to each station were estimated using **Vp = 6.03 km/s**, taken from the first layer of CG-506.

The calculation included epicentral distance, source depth, and station elevation. Using one velocity along the path is a regional approximation.

For each trial source, the code searched for compatible picks at different stations and estimated a common origin time. Initial groups required:

- At least four stations.
- Absolute timing residuals no greater than **2.5 s**.
- Residual RMS no greater than **0.80 s**.

Groups sharing picks were ranked by station count and then RMS. The trial catalogue ID identifies the starting source position; it is not a catalogue-event assignment.

### Location and Final Filters

Least-squares fitting estimated latitude, longitude, depth, and origin time. Depth was limited to **0–33 km**. Picks with absolute residuals above **2.5 s** were removed and the solution was fitted again while enough stations remained.

Final candidates required:

| Criterion | Limit |
|---|---|
| Station support | At least 5 stations |
| Arrival-residual RMS | ≤ 0.80 s |
| Search boundary | No boundary flag |
| Study area | Within the 30 km circle |

Origin times separated by at most 1 s were flagged for possible duplicate review. A low RMS alone does not prove that the association is correct.

### Development Day — 14 March

The workflow produced **962 initial associations**. After final filtering and exclusion of the mainshock control, **113 candidates** remained.

Comparison with **130 reference aftershocks**, using one-to-one origin-time matching within **±2 s**, gave **28 matches** and **85 unmatched candidates**. Catalogue recovery was **21.5%**.

### March Test — 15–31 March

The fixed workflow processed all seven stations over **17 days**. It produced **3,914 initial associations** and **174 retained candidates**.

No retained candidate origin times were within 1 s of each other. This check identifies close-time solutions but does not exclude every possible duplicate.

## PART III — Model Evaluation

### Catalogue Comparison

The final reference was built from the **complete monthly GSE phase export**, with one record per event ID. It includes events represented by phase records at one or more of the seven stations.

For **15–31 March**, the reference contained **272 events** inside the study area. The earlier 14–20 March subset was not used as the final test reference.

Candidates and catalogue events were paired one-to-one within **±2 s of origin time**.

| Outcome | Count |
|---|---:|
| Reference events | 272 |
| Retained candidates | 174 |
| Catalogue time matches | 58 |
| Unmatched reference events | 214 |
| Candidates without a catalogue match | 116 |

Catalogue recovery was:

$$
\frac{58}{272} \times 100 = 21.3\%.
$$

The proportion of retained candidates with a catalogue time match was **33.3%**.

### Timing and Location Errors

For the **58 time-matched pairs**, the medians were:

| Measurement | Median |
|---|---:|
| Absolute origin-time difference | 0.557 s |
| Epicentral separation | 4.395 km |
| Absolute depth difference | 5.940 km |
| Fitted P-arrival residual RMS | 0.401 s |

These statistics apply to the matched subset. Arrival RMS measures agreement with the velocity approximation. Catalogue differences measure agreement with the reference solutions.

### Retrospective Temporal Cross-Validation

Three expanding temporal splits compared minimum station requirements of five and six. Earlier days were used to select the requirement, and the following block was used for validation. All folds selected five stations.

| Validation period | Reference events | Candidates | Matches | Recovery |
|---|---:|---:|---:|---:|
| 19–22 March | 72 | 29 | 12 | 16.7% |
| 23–26 March | 25 | 19 | 5 | 20.0% |
| 27–31 March | 10 | 16 | 0 | 0.0% |

Mean block recovery was **12.2%**. Pooled recovery was **15.9%**, or **17 of 107 reference events**.

This was a retrospective assessment because aggregate March results had already been inspected. It evaluates minimum station support. It is reported separately from the fixed-setting recovery of **21.3%** for the full period.

### Visual Review

The analyst reviewed all **116 unmatched candidates**, accepting **85 as real seismic events** and rejecting **31 as false detections**. The acceptance rate in this reviewed group was **73.3%**.

Together with the 58 catalogue assignments, this gives **143 accepted detections among 174 candidates**. The corresponding review-based precision estimate is **82.2%**, assuming correct catalogue assignments and distinct accepted events.

The review labels are used as analyst assessments. Their reliability depends on the waveform evidence and the consistency of the event associations.

### Conclusion

The workflow combined continuous time-series detection, multi-station P association, approximate location, and analyst review.

It recovered **58 of 272 known events**, showing limited catalogue recovery under the final criteria. Visual review also accepted **85 unmatched candidates** and removed **31 false detections**.

If all 85 accepted candidates are established as distinct additional local earthquakes, the reference count increases from **272 to 357**, or by **31.3%**. Final catalogue inclusion requires checking remaining associations and duplicate representations.

The main outcome is a reproducible candidate-selection workflow. Further work should improve recovery of known events, test the velocity approximation, and complete the catalogue processing of accepted candidates.

### Confusion Matrix — Final Evaluation Status

The evaluation metrics depend on whether performance is measured strictly against the existing manual bulletin or updated with multi-station analyst review.

| Outcome | Catalogue-Only Comparison | After Analyst Review |
|---|---:|---|
| **Catalogue time matches (TP)** | 58 | 58 retained catalogue matches |
| **Unmatched candidates** | 116 (treated as FP) | 85 accepted (new TP); 31 rejected (FP) |
| **Unmatched reference events (FN)** | 214 | Pending association check against accepted candidates |
| **True negatives (TN)** | Not defined | Not defined |

#### 1. Catalogue-Only Baseline
Treating all unmatched candidates as false positives yields conservative baseline metrics against the unexpanded reference bulletin:

$$
\mathrm{Precision}_{\text{catalogue}} = \frac{58}{174} = 33.3\%, \qquad

\mathrm{Recall}_{\text{catalogue}} = \frac{58}{272} = 21.3\%, \qquad

F1_{\text{catalogue}} = \frac{2 \times 58}{2 \times 58 + 116 + 214} = 26.0\%
$$

#### 2. Post-Review Performance and Methodological Scope

Visual review accepted **85** unmatched candidates as real seismic events and rejected **31** as false detections. Including the **58** catalogue matches, the review-based precision estimate is:

$$
\mathrm{Precision}_{\text{review}}
= \frac{58 + 85}{174}
= \frac{143}{174}
= 82.2\%.
$$

Duplicate checks were completed, with no retained candidate origin times within 1 s of each other. A final post-review F1-score requires checking whether accepted candidates correspond to catalogue events outside the ±2 s matching tolerance. True negatives were not evaluated because verified event-free windows were not defined.
