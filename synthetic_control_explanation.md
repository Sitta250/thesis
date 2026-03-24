# Synthetic Control Analysis -- Section-by-Section Explanation

## 0. Data Preparation

### What this section is

Load the dataset and convert it into a format usable by synthetic
control.

Typical steps: - Load CSV data - Clean column names - Convert dates into
a monthly index - Pivot the dataset so each corridor becomes a column

Example structure after pivot:

  Date      NATCOR   CAPE
  --------- -------- ------
  2019-01   100      90
  2019-02   105      95

### Why we run it

Synthetic control requires a time series for each corridor so the model
can compare them.

### How to interpret outcome

If done correctly: - All corridors share the same timeline - No missing
months - Data is aligned properly

If the data is wrong here, all later results will be unreliable.

------------------------------------------------------------------------

## 1. Base Synthetic Control Model

### What it is

The core model that creates a synthetic version of NATCOR using other
corridors.

Conceptually:

Synthetic NATCOR = w1 \* CAPE + w2 \* Corridor2 + ...

The weights are chosen so that the synthetic version closely matches
NATCOR before the flood.

### Why we run it

We want to estimate what NATCOR would have looked like **if the flood
had never happened**.

### Interpretation

Before 2022: - Actual NATCOR and Synthetic NATCOR should overlap.

After 2022: - The gap between the two lines represents the flood impact.

If Actual \< Synthetic after the flood, rail volumes were reduced due to
the disaster.

------------------------------------------------------------------------

## 2. In-Time Placebo Tests

### What it is

The model pretends the flood happened at earlier dates (fake
treatments).

Example fake dates: - 2017 - 2018 - 2019

### Why we run it

To check if the model detects impacts when nothing actually happened.

### Interpretation

Good model: - Placebo impacts are close to zero.

Bad model: - Large impacts appear even during fake treatment periods.

This would indicate the model is detecting noise rather than real
effects.

------------------------------------------------------------------------

## 3. In-Space Placebo Test

### What it is

Another corridor (for example CAPE) is treated as if it experienced the
flood.

### Why we run it

To confirm that the detected effect is unique to NATCOR.

### Interpretation

Good result: - NATCOR shows a large impact - CAPE shows little or no
impact

Bad result: - Both corridors show similar effects, which would suggest
the model is capturing general market changes instead of flood damage.

------------------------------------------------------------------------

## 4. Event Study -- Monthly Effects

### What it is

Instead of one overall effect, the analysis measures the treatment
effect for each month.

Effect = Actual -- Synthetic

### Why we run it

Flood impacts are not instantaneous. They often show: - sudden drop -
gradual recovery - stabilization

### Interpretation

Before flood: - Effect should be near zero.

After flood: - Negative values indicate lower rail volumes than
expected.

------------------------------------------------------------------------

## 5. Multi-Outcome Synthetic Control

### What it is

The analysis is applied to multiple indicators instead of just rail
volume.

Examples: - Rail Volume - Market Share - Delays - Road Freight

### Why we run it

If the flood truly disrupted the corridor, multiple indicators should
change together.

### Interpretation

Strong evidence: - Several outcomes show disruption.

Weak evidence: - Only one metric changes.

------------------------------------------------------------------------

## 6. Pre-Trends Test and Difference-in-Differences

### What it is

Two statistical checks.

**Pre-Trend Test** Checks whether NATCOR and comparison corridors
behaved similarly before the flood.

**Difference-in-Differences** A simpler causal model comparing: - before
vs after - treated vs control

### Why we run it

Synthetic control assumes the treated and donor corridors follow similar
trends before the treatment.

### Interpretation

Good: - Pre-trend difference is close to zero.

Bad: - NATCOR was already diverging before the flood.

------------------------------------------------------------------------

## 7. Treatment Date Sensitivity

### What it is

The model is rerun using slightly different treatment dates.

Example: - Oct 2021 - Feb 2022 - Apr 2022 - Jun 2022

### Why we run it

Real-world disruptions rarely start on a perfectly precise date.

### Interpretation

Good result: - The impact appears consistently around the real flood
date.

Bad result: - The impact appears randomly depending on the chosen date.

------------------------------------------------------------------------

## 8. Summary Metrics

### What it is

A table that summarizes model performance.

Typical metrics include: - Pre-treatment RMSPE - Post-treatment RMSPE -
RMSPE ratio - Average treatment effect

### Why we run it

Provides a quick evaluation of how well the model performed.

### Interpretation

A large Post/Pre RMSPE ratio indicates: - Good pre-flood fit -
Significant divergence after the flood

This supports the causal impact estimate.

------------------------------------------------------------------------

# Overall Workflow

1.  Build the synthetic control model
2.  Validate with placebo tests
3.  Study the dynamic impact over time
4.  Confirm robustness using additional metrics and sensitivity tests

If all checks support the results, we can confidently conclude that the
2022 floods caused a measurable reduction in NATCOR rail volumes.
