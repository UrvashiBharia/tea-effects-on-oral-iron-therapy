# Validation checks

Additional checks run on the synthetic data (known ground truth) to confirm the pipeline behaves correctly before it would be trusted on real data.

## Standardised (beta) coefficients
Puts predictors on a common scale so effect sizes are directly comparable regardless of their original units.

| Term | Target | Result |
|---|---|---|
| Tea | ~-0.21 | -0.208 |
| Therapy duration | ~0.35 | 0.321 |

## One-sample t-test on Hb change
Tests whether the average Hb change across the cohort differs from zero confirming a real, non-zero average improvement is present, as built into the data.

- Target mean: ~0.765
- Result: mean = 0.695, 95% CI [0.625, 0.765], t = 19.63, p < 2.2×10⁻¹⁶

## Shapiro-Wilk test on regression residuals
Checks the normality assumption behind the regression's p-values. A non-significant result here means the model's error term is well-behaved, as designed.

- Target: p > 0.05
- Result: W = 0.995, p = 0.775 ✓

## Rate model: (Hb change / duration) ~ tea grams
An alternative way of expressing the relationship: Hb improvement per month of therapy. Since dividing by duration wasn't how the data was generated, this
coefficient isn't expected to exactly match the raw tea coefficient, but should stay negative and significant as a consistency check.

| Term | Estimate | Std. Error | t value | p-value |
|---|---|---|---|---|
| Intercept | 0.243 | 0.029 | 8.47 | <0.001 |
| Tea (g/day) | -0.0063 | 0.0028 | -2.22 | 0.028 |

## GAM nonlinearity check
Tests whether the tea-Hb relationship needs a curved fit instead of a straight line. Since the true relationship was built as linear, a correctly-behaving check should find no extra curvature.

- Target: edf ≈ 1, AIC ≈ linear model's AIC
- Result: edf = 1.00, AIC(linear) = AIC(GAM) = 267.60 ✓ (smooth term p = 0.0019,
  matching the linear model's tea coefficient test — no added curvature found)

## Proportions: compliance and adverse effects
Exact binomial tests checking whether sampled proportions are consistent with the probabilities used to generate them.

| Measure | Target | Result | 95% CI |
|---|---|---|---|
| Compliance | 0.80 | 0.76 | [0.695, 0.817] |
| Black stool | 0.30 | 0.31 | [0.247, 0.379] |
| Constipation | 0.20 | 0.19 | [0.138, 0.251] |

All three built-in target probabilities fall within their respective confidence intervals.
