# Cookie Cats A/B Test Analysis

A end-to-end experiment analysis examining the effect of gate placement on player retention in the mobile puzzle game Cookie Cats.

## Overview

Cookie Cats is a mobile puzzle game where players encounter gates that require them to wait or make an in-app purchase before continuing. This project analyzes an A/B test in which the first gate was moved from level 30 (control) to level 40 (treatment) across 90,189 players, with the goal of determining whether gate placement affects player retention.

The project follows a pre-registration framework: the experiment design document (including hypothesis, MDE, power, and sample size calculations) was completed before the analysis, reflecting real-world best practices for rigorous A/B testing.

## Repository Structure

```
cookie-cats-ab-test/
├── experiment_design.md   # Pre-registered experiment design: hypothesis, MDE, power, sample size
├── cookie-cats-a-b-testing.jpynb               # Statistical analysis notebook
└── README.md
```

## Dataset

**Source:** [Mobile Games A/B Testing — Cookie Cats](https://www.kaggle.com/datasets/mursideyarkin/mobile-games-ab-testing-cookie-cats) via Kaggle

| Column | Description |
|--------|-------------|
| `userid` | Unique player identifier |
| `version` | Group assignment: `gate_30` (control) or `gate_40` (treatment) |
| `sum_gamerounds` | Rounds played in first 14 days after install |
| `retention_1` | Did the player return 1 day after install? |
| `retention_7` | Did the player return 7 days after install? |

## Hypothesis

**If** the first gate is moved from level 30 to level 40, then 7-day retention will increase, because players will have more time to become invested in the game before hitting friction.

## Statistical Design

| Parameter | Value |
|-----------|-------|
| Primary metric | 7-day retention (binary) |
| Secondary metric | 1-day retention (binary) |
| Guardrail metric | Game rounds played (continuous) |
| Test (binary metrics) | Two-proportion z-test |
| Test (continuous metric) | Welch's t-test |
| Significance level (α) | 0.05 (two-tailed) |
| Statistical power (1 − β) | 0.80 |

## Results

| Test | Metric | Result | p-value |
|------|--------|--------|---------|
| Welch's t-test | Game rounds played (guardrail) | No significant difference | 0.3759 |
| Two-proportion z-test | 1-day retention | No significant difference | 0.0744 |
| Two-proportion z-test | 7-day retention | **Significant — gate_30 higher** | 0.0016 |

Gate_30 shows a **0.82 percentage point higher 7-day retention rate** than gate_40 (19.02% vs. 18.20%), a ~4.3% relative difference. With 90,000+ players, this effect is statistically reliable. In a free-to-play game where long-term retention drives monetization, a difference of this magnitude is operationally meaningful.

📊 View the Tableau Dashboard : (https://public.tableau.com/app/profile/gabrielle.epelle/viz/CookieCats_17826759522930/CookieCatsABTestGatePlacementEffectsOnPlayerRetention?publish=yes)

**Recommendation: retain the gate at level 30.** The data does not support moving the gate to level 40.

## Data Quality

A sample ratio mismatch check returned p = 0.0086, indicating a statistically significant deviation from the intended 50/50 split (observed: 50.4% gate_40 / 49.6% gate_30, a difference of ~789 players). The absolute imbalance is modest and unlikely to materially bias retention estimates, but would warrant investigation into the randomization mechanism in a production setting.

Post-hoc MDE calculation confirms the experiment was sensitive enough to detect differences as small as 0.74 pp (3.9% relative lift) at α = 0.05 and 80% power. The non-significant 1-day retention result (p = 0.074) should therefore be interpreted as a genuine null effect rather than a power limitation. The experiment had sufficient sensitivity to detect effects smaller than those observed.

## Tools & Methods

- **Python** — pandas, scipy, statsmodels, matplotlib
- **Statistical tests** — two-proportion z-test, Welch's t-test, chi-square goodness-of-fit
- **Framework** — pre-registered experiment design with locked hypothesis, MDE, and sample size prior to analysis
