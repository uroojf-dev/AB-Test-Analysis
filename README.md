# A/B Test Analysis: Landing Page Conversion

Analyzing an e-commerce A/B test to determine whether a new landing page design improves user conversion compared to the existing page.

## Overview

Companies routinely redesign key pages and want statistical evidence — not just a gut feeling — before rolling a change out to everyone. This project applies hypothesis testing to a real-world dataset to determine whether a new landing page actually improves conversion rate, or whether any observed difference is simply due to random chance.

## Dataset

- **Source:** Udacity A/B Testing dataset (`ab_data.csv`)
- **Size:** ~294,000 raw user sessions; ~290,000 after cleaning
- **Columns:** `user_id`, `timestamp`, `group` (control/treatment), `landing_page` (old_page/new_page), `converted` (0/1)
- **Question:** Does the new landing page produce a statistically significant difference in conversion rate compared to the old page?

## Approach

### 1. Data Loading and Cleaning
- Loaded the raw dataset and confirmed shape, types, and zero missing values

### 2. Data Integrity Checks
This is the step most analyses skip — and the step that matters most before trusting any result.
- Confirmed group sizes were balanced (control: 147,202 / treatment: 147,276) — no sample ratio mismatch
- **Found a real data quality issue:** 3,893 rows where the assigned `group` didn't match the `landing_page` actually shown (e.g. control users who were shown the new page). These rows would have contaminated the comparison if left in, so they were removed.
- Found and removed 1 duplicate `user_id`, since repeated observations from the same user violate the independence assumption required by the statistical tests used later

### 3. Descriptive Statistics
- Calculated conversion rate per group using the mean of the binary `converted` column

### 4. Hypothesis Testing
- **Null hypothesis (H₀):** no real difference in conversion rate between the old and new page
- **Alternative hypothesis (H₁):** a real difference exists
- Built a contingency table of group × converted counts and ran a chi-square test of independence

### 5. Confidence Interval
- Calculated a 95% confidence interval for the difference in conversion rates (treatment − control) to quantify the plausible range of the true effect, not just a significant/not-significant verdict

### 6. Visualization
- Bar chart comparing conversion rates between control and treatment

## Key Findings

| Metric | Control | Treatment |
|---|---|---|
| Conversion rate | 12.04% | 11.88% |

- **Chi-square statistic:** 1.70
- **P-value:** 0.192 (above the 0.05 significance threshold)
- **95% CI for difference (treatment − control):** (−0.39%, +0.08%) — includes zero

The new landing page did not produce a statistically significant improvement in conversion rate. The observed gap (control performing marginally better) is consistent with random chance rather than a real effect of the redesign. The confidence interval is also asymmetric — skewed toward the new page underperforming — meaning the plausible downside of shipping it outweighs the plausible upside.

## Recommendation

**Do not ship the new landing page based on this data.** With a sample of ~290,000 users, the test had strong statistical power, yet found no measurable lift. Continuing to run the new page would not be expected to improve conversion, and may carry a small risk of making it worse.

## Tech Stack

Python · Pandas · NumPy · SciPy · Statsmodels · Matplotlib · Seaborn · Jupyter Notebook

## Skills Demonstrated

- Data integrity validation (sample ratio mismatch check, group/treatment assignment mismatch, duplicate detection)
- Hypothesis testing (chi-square test of independence)
- Confidence interval estimation for difference in proportions
- Translating statistical results into a clear business recommendation
- Recognizing and correctly interpreting a non-significant result, rather than overstating findings

## How to Run

```bash
pip install -r requirements.txt
jupyter notebook AB_Test_Analysis.ipynb
```

## Future Improvements

- Run a power analysis to confirm the sample size was sufficient to detect a meaningful effect size
- Segment the analysis by user attributes (e.g. new vs. returning visitors) to check for heterogeneous effects
- Test for novelty effects by analyzing conversion rate trends over the test's time window
