# Capstone feedback — Seismic Catalogue Completeness and Aftershock Detection, Čarađe 2024

**For:** Milena
**Repository reviewed:** github.com/milena-seismo/DSB_capstone (README, `Capstone_EDA.ipynb`, `Capstone_Modeling.ipynb`, public CSVs)
**Accompanying notebook:** `Caradje_completeness_modelling_solution.ipynb`

---

## Overall

This is a substantial and careful piece of work. You built a full detection pipeline from raw waveforms across seven stations: picking, association, location, filtering and catalogue comparison. You kept the development day separate from the test period, and you were honest in the notebook about what the results do and do not show. Your recall, precision and F1 arithmetic is correct.

There are two things to address before the project's main claim stands. First, one filtering choice is responsible for most of the recall you lost, and it can be fixed. Second, the "85 new events / 82 % precision" result rests on a review that cannot yet be reproduced or measured. The tasks below are ordered so that the biggest gains come first.

## What holds up well

- Your EDA counts reproduce exactly (2,292 unique events, 906 in the 30 km circle, 337 in 14–20 March).
- Your 6.03 km/s velocity assumption, which you flagged as unverified, is good. Comparing the bulletin P arrivals with predicted travel times gives median station residuals within ±0.2 s, and a free fit gives 5.99 km/s. You can state this as validated.
- One-to-one matching with `linear_sum_assignment` and a fixed tolerance is the right approach. Chance coincidences within ±2 s explain only one or two matches.
- Selecting settings on 14 March and testing on 15–31 March is the correct train/test logic for time-ordered data.

## Key findings

**1. The search-boundary filter removes good events, not bad ones.** In your locator, `search_boundary = True` whenever any bound is active. In practice this means depth was pushed to 0 or 33 km. On a sparse, one-sided network with P picks only, depth is the least-constrained parameter, so this is the normal outcome rather than a sign of a false trigger.

Your own 14 March table shows this:
- At ≥ 5 stations, keeping boundary solutions gives 87 of 130 catalogue matches (67 % recall) instead of 28 (22 %).
- Precision is the same either way, about 27 % vs 25 %.
- Boundary solutions with 6–7 stations have the *smallest* location errors (1.6–2.2 km).

The conclusion in the Modeling notebook that boundary solutions account for most false triggers is true in raw counts but not in rate. Rate is what matters for precision. This is an easy mistake to make when reading a grouped table.

**2. Where the recall goes.** Running your locator on the analysts' own P picks separates the stages of the pipeline. Shares are of the 272 reference events, 15–31 March:

| Stage | Share surviving |
|---|---:|
| Analysts picked ≥ 5 of your 7 stations | 79 % |
| Perfect picks, depth fixed at ~13 km | 77 % |
| Perfect picks, free depth + boundary rule | 66 % |
| Pick errors like your CEME test (σ ≈ 0.65 s), free depth + boundary rule | 45 % |
| Achieved by your workflow | 21 % |

The remaining gap after fixing depth comes from missing or spurious MAD picks and association, mainly at the distant stations (DRME, HCY, KOME).

**3. The review-based precision is not yet a measurement.** For the 82 % figure to count as a result, a reader needs to see:
- the labels and the review notebook (the README mentions a separate review notebook, but the "Milena notebooks" file in the repo is empty);
- a review done without knowing which windows were candidates;
- control windows to compare against;
- a check that accepted events are not catalogued events located elsewhere or more than 2 s away.

A visual label confirms "an earthquake", not "a missing Čarađe aftershock".

**4. Uncertainty is missing from the headline numbers.** With 95 % confidence intervals:
- Recall of 21.3 % is 17–27 %.
- The 27–31 March validation block (0 of 10) is compatible with a recall of up to 28 %.

Comparing 21.5 % on 14 March with 21.3 % on 15–31 March does not show "stable generalisation", because your own temporal cross-validation ranges from 0 % to 20 % by block.

**5. Your EDA under-states the completeness problem.**
- Maximum curvature tends to underestimate Mc. Corrected and goodness-of-fit methods give Mc ≈ 1.8–1.9 for 14–31 March, with b ≈ 0.9–1.0.
- Mc is higher in the first 24 hours (≈ 1.9–2.1), as expected after a mainshock.
- Extrapolating Gutenberg–Richter suggests roughly 740 events of M ≥ 1 are missing from 14–31 March (range about 520–1,060). That makes your search well motivated and the 85 candidates plausible, but it also shows they are a small share of what is missing.

**6. The reference catalogue changes over time.** From 18 March, the distant stations are picked much less often at the same magnitudes (for example, DRME drops from 0.86 to 0.36). This partly explains the weaker later validation blocks, so recall should be reported per period.

## Prioritised task list

| # | Task | Why | Done when |
|---|---|---|---|
| 1 | Re-run location with depth **fixed** (try 13 km, the catalogue median; optionally 8 and 18 km) and drop the search-boundary rule. Choose the station threshold on 14 March, then report 15–31 March once. | Largest avoidable recall loss; no precision cost on the development day. | A 15–31 March table of candidates, matches, precision, recall and F1 for ≥ 4/5/6/7 stations. Part 9.1 of the accompanying notebook does this from your saved `march_locations` files. |
| 2 | Run a **blind review**: mix all unmatched candidates with ~30 catalogue-matched candidates and ~30 random windows, shuffle, label, then score. | Turns the 82 % into a measured precision with an interval and a false-alarm baseline. | The labelled sheet and key are committed, and precision is reported with a 95 % CI. Parts 9.2–9.3 build and score the sheet. |
| 3 | Check every accepted candidate against the **full March bulletin** (all distances, ±10 s). | Separates truly new events from catalogued events with a shifted origin time or a location outside 30 km. | Accepted candidates split into "new", "catalogued elsewhere" and "shifted"; only "new" counts towards completeness. |
| 4 | Add **confidence intervals** and per-period results to the README; remove the "stable generalisation" sentence and the stale 206/46 cell. | The current text over-reads small numbers. | Every headline proportion has k/n and a CI. |
| 5 | Redo **Mc and b** with a corrected method, split by period (first 24 h vs after), and add the expected number of missing events. | Your EDA baseline (Mc = 1.3) is too low, and this is the yardstick for the whole project. | An Mc table by method and period, the b-value, and the missing-event estimate with a range. |
| 6 | Improve **P detection at DRME, HCY and KOME**: band-pass 2–15 Hz before the MAD trigger, and compare with ObsPy `classic_sta_lta` and your EQTransformer picks. | This is where the remaining recall is lost. | Per-station pick recall against the bulletin, before and after. The station detection curves in Part 5 give target magnitudes. |
| 7 | Compute **local magnitudes** for accepted candidates and re-estimate Mc and b with them added. | Closes the completeness question set in your EDA. | A frequency–magnitude plot before and after adding the candidates. |
| 8 | **Tidy the EDA**: keep depth-quality flags instead of imputing 10 km (the 11.1 km flag also covers 76 non-zero depths), check near-duplicates within 2 s, and reconcile the zero-magnitude event. | Small, but each affects matching or depth statistics. | Near-duplicate table included; README and notebook agree on the zero-magnitude event. |
| 9 | **State the question explicitly** at the top of the README: target, metric, success criterion. | Makes the project read as a data science question rather than a pipeline description. | One short paragraph before Part I. |

Tasks 1–4 change the conclusions; 5–9 strengthen them. If time is short, do 1, 2 and 4.

## Notes on the README

- The README says the zero-magnitude event was "checked and corrected"; the EDA notebook says the magnitude was retained. Use one version.
- The README says "Duplicate checks were completed". What was done is an origin-time proximity check, so describe it that way.
- Note that ML and Md magnitudes are mixed in the catalogue. Even one sentence is enough.
- When stating that true negatives are undefined, add what replaces them: a precision–recall table over a selection parameter, and a false-alarm rate from random windows.

## Course materials that map to the fixes

- Model workflow, and train/test split and cross-validation: the temporal hold-out and choosing settings on development data only.
- Classification metrics and ROC-AUC: precision, recall and F1, and why accuracy and ROC are not available without true negatives.
- Imbalanced data ("Option 4: Change the class cutoff threshold"): choosing an operating point.
- GridSearch and Pipelines: sweeping station thresholds and depth settings systematically.
- Logistic regression, ensembles and model explainability: the station detection-probability model in the accompanying notebook.

The accompanying notebook runs end-to-end in Colab from your two public CSVs. Part 9 runs on the result tables you have already saved on Drive.
