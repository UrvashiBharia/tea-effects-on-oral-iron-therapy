# tea-effects-on-oral-iron-therapy
Reanalysis of the study "A cohort study on change in hemoglobin levels in Indian female tea and coffee drinkers currently on oral iron therapy for iron deficiency anemia" using synthetic practice data to validate my pipeline

I re-ran the statistics from my published research paper [Bharia & Srivastav, IJCMPH 2026,](https://www.ijcmph.com/index.php/ijcmph/article/view/15247) on hemoglobin response to oral iron therapy (OIT) in tea and coffee-drinking women with iron deficiency anemia above 8g/dL (conventionally treatable with OIT), using synthetic practice data to validate my pipeline before running it on the real dataset. 

The original analysis was done in Excel/SPSS. I reimplemented it in R, added a synthetic-data validation step to confirm whether the pipeline recovers known coefficients before trusting it on real data. 

![Figure 1](figures/fig1_tea_vs_change.png)
*Individual participants' daily tea intake vs. change in Hb (n=134).*

![Figure 2](figures/fig2_tea_only.png)
*Tea intake vs. change in Hb with linear trend.*

![Figure 3](figures/fig3_venn.png)
*Adverse effects: black stool and constipation overlap (n=134).*

## Table 1: Baseline characteristics (n=134)
Table 1: Baseline Characteristics (n=134)
| | |
|---|---|
| Age | 31.6 ± 9.7 years |
| OIT duration | 4.31 ± 1.65 months |
| Hb at start | 9.79 ± 1.22 g/dl |
| Hb at end | 10.37 ± 1.29 g/dl |
| Change in Hb | 0.58 ± 0.55 g/dl |
| Daily tea (median) | 6.25 g |`.

## Method
1. Extracted per-participant data and rebuilt it as a clean CSV.
2. Built a synthetic dataset with known true coefficients (`synthetic-practice/`)
   and confirmed the R pipeline recovers them before using it on real data.
3. Ran the multivariable linear regression (change in Hb ~ tea grams + therapy duration) in R,
   plus standard diagnostics (residual normality, homoscedasticity, VIF).
4. Added robustness checks: CI on the tea coefficient, an ANCOVA-style
   model conditioning on baseline Hb, and a GAM check for nonlinearity.

## Results
Across the cohort (n=134), more daily tea intake was associated with a smaller
improvement in Hb on iron therapy, controlling for treatment duration. This held
up under every robustness check below.

**Primary regression** (change in Hb ~ tea grams + therapy duration), R² = 0.15:
| Predictor | Coefficient | 95% CI (bootstrap) | p-value |
|---|---|---|---|
| Tea (g/day) | -0.025 | (-0.040, -0.008) | 0.003 |
| Therapy duration (months) | 0.075 | — | 0.008 |

**Standardized effect sizes:** tea β = -0.25, therapy duration β = 0.23.

**Robustness checks:**
- *Baseline-adjusted (ANCOVA-style) model*: conditioning on starting Hb instead
  of just modeling the raw change: tea effect essentially unchanged
  (β = -0.026, p = 0.003), so the result isn't an artifact of regression to the mean.
- *Nonlinearity check (GAM)*: a flexible smooth term for tea dose fit better
  than a straight line (AIC 198.8 vs. 203.8), suggesting the dose-response isn't
  perfectly linear across the full range.
- *Residual diagnostics*: homoscedastic (Breusch-Pagan p > 0.3), no collinearity
  between predictors (VIF ≈ 1.0).
