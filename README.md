# Cookie Cats A/B Test Analysis

A end-to-end experiment analysis examining the effect of gate placement on player retention in the mobile puzzle game Cookie Cats.

## Overview

Cookie Cats is a mobile puzzle game where players encounter gates that require them to wait or make an in-app purchase before continuing. This project analyzes an A/B test in which the first gate was moved from level 30 (control) to level 40 (treatment) across 90,189 players, with the goal of determining whether gate placement affects player retention.

The project follows a pre-registration framework: the experiment design document (including hypothesis, MDE, power, and sample size calculations) was completed before the analysis, reflecting real-world best practices for rigorous A/B testing.

## Repository Structure

```
cookie-cats-ab-test/
├── experiment_design_doc.docx   # Pre-registered experiment design: hypothesis, MDE, power, sample size
├── analysis.ipynb               # Statistical analysis notebook
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

**If** the first gate is moved from level 30 to level 40, **then** 7-day player retention will decrease, **because** the earlier gate creates a natural break that reinforces the habit of returning to the game before players have had a chance to disengage.

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

**Recommendation: retain the gate at level 30.** The data does not support moving the gate to level 40.

## Data Quality

A sample ratio mismatch (SRM) check was performed using a chi-square goodness-of-fit test against the intended 50/50 allocation. The test returned p = 0.0086, indicating a statistically significant deviation from the expected split (observed: 50.4% / 49.6%, a difference of ~789 players).

While the absolute imbalance is modest and unlikely to materially bias the retention estimates, an SRM of this kind would trigger an investigation into the randomization mechanism in a production setting before results are acted upon.

## Tools & Methods

- **Python** — pandas, scipy, statsmodels, matplotlib
- **Statistical tests** — two-proportion z-test, Welch's t-test, chi-square goodness-of-fit
- **Framework** — pre-registered experiment design with locked hypothesis, MDE, and sample size prior to analysis
