# Cookie Cats A/B Test: Experiment Design & Results

---

## 1. Problem Statement

Cookie Cats is a mobile puzzle game in which players periodically encounter gates that require them to wait or make an in-app purchase before continuing. The first gate currently appears at level 30. This experiment tests whether moving that gate to level 40 — giving players more uninterrupted playtime before hitting friction — positively impacts player retention and overall engagement.

---

## 2. Hypothesis

**If** the first gate is moved from level 30 to level 40, **then** 7-day retention will increase, **because** players will have more time to become invested in the game before encountering friction, reducing early drop-off.

**Null hypothesis:** Moving the gate from level 30 to level 40 has no effect on 7-day retention.

---

## 3. Variants

| Variant | Description | Traffic allocation |
|---|---|---|
| Control (gate_30) | Players encounter the first gate at level 30 | 49.6% |
| Treatment (gate_40) | Players encounter the first gate at level 40 | 50.4% |

**Randomization unit:** User ID — each player is assigned to one group at install and remains in that group for the duration of the experiment.

**Eligibility:** All players who installed the game while the A/B test was running.

---

## 4. Metrics

| Metric type | Metric | Definition |
|---|---|---|
| Primary | 7-day retention | Did the player return to the game 7 days after install? |
| Secondary | 1-day retention | Did the player return to the game 1 day after install? |
| Guardrail | Game rounds played | Total rounds played in the 14 days following install — must not decrease significantly |

---

## 5. Sample Size & Duration

| Input | Value |
|---|---|
| Baseline 7-day retention rate (gate_30) | 19.02% |
| Minimum detectable effect (MDE) | 10% relative lift (1.90 pp absolute) — set as the minimum lift that would represent a meaningful improvement in long-term engagement in the absence of explicit business targets. Post-hoc analysis revealed the actual sample of 90,189 players yielded a true MDE of 0.74 pp (3.9% relative lift), substantially smaller than the pre-specified MDE. |
| Treatment rate under MDE | 20.92% |
| Statistical significance (α) | 0.05 (two-tailed) |
| Statistical power (1 − β) | 0.80 |
| Required sample size per arm | 6,932 |
| Total required sample size | 13,864 |
| Average daily installs | ~6,442 |
| Estimated minimum runtime | 3 days (rounded up to 14 days to capture a full two-week cycle and smooth day-of-week effects) |
| Actual sample collected | 90,189 players |

The experiment was substantially overpowered relative to the minimum required sample, giving high confidence in the reliability of the results and the ability to detect effects smaller than the pre-specified MDE.

---

## 6. Implementation Checklist

- [ ] Experiment logged in experiment tracking system (Amplitude, Mixpanel, LaunchDarkly, etc.)
- [ ] Randomization verified — no imbalanced split on pre-experiment metrics
- [ ] Tracking events instrumented for primary metric and all secondary/guardrail metrics
- [ ] QA passed for both control and treatment variants
- [ ] A/A test passed (run control vs. control for 48h to verify randomization)
- [ ] Monitoring dashboard set up
- [ ] Alert configured for guardrail metric breaches

---

## 7. Analysis Plan

| Metric | Statistical test | Rationale |
|---|---|---|
| 7-day retention (primary) | Two-proportion z-test | Binary outcome; large sample satisfies normal approximation |
| 1-day retention (secondary) | Two-proportion z-test | Binary outcome; large sample satisfies normal approximation |
| Game rounds played (guardrail) | Welch's t-test | Continuous outcome; Welch's does not assume equal variance |

**Multiple comparisons:** 7-day retention is the pre-committed primary metric. 1-day retention is secondary and exploratory — results are interpreted in context, not used independently to make the ship/no-ship decision.

**Data quality check:** A chi-square goodness-of-fit test will be performed against the intended 50/50 allocation to detect any sample ratio mismatch (SRM) before results are interpreted.

---

## 8. Results

### Data Quality

A sample ratio mismatch check returned p = 0.0086, indicating a statistically significant deviation from the intended 50/50 split (observed: 50.4% gate_40 / 49.6% gate_30, a difference of ~789 players). The absolute imbalance is modest and unlikely to materially bias retention estimates, but would warrant investigation into the randomization mechanism in a production setting.

Post-hoc MDE calculation confirms the experiment was sensitive enough to detect differences as small as 0.74 pp (3.9% relative lift) at α = 0.05 and 80% power. The non-significant 1-day retention result (p = 0.074) should therefore be interpreted as a genuine null effect rather than a power limitation. The experiment had sufficient sensitivity to detect effects smaller than those observed.

### Hypothesis Tests

| Test | Metric | Result | p-value |
|---|---|---|---|
| Welch's t-test | Game rounds played (guardrail) | No significant difference | 0.3759 |
| Two-proportion z-test | 1-day retention | No significant difference | 0.0744 |
| Two-proportion z-test | 7-day retention | **Significant — gate_30 higher** | 0.0016 |

### Key Finding

Contrary to the hypothesis, moving the gate to level 40 did not improve retention — it reduced it. Gate_30 shows a **0.82 percentage point higher 7-day retention rate** than gate_40 (19.02% vs. 18.20%), a ~4.3% relative difference. With 90,000+ players, this effect is statistically reliable. The guardrail metric (game rounds played) showed no significant difference between groups, confirming the gate change did not affect overall engagement volume.

The finding suggests that the earlier gate at level 30 functions as a habit-forming mechanism — the enforced break creates anticipation that brings players back — rather than a friction point that drives them away.

---

## 9. Recommendation

**Do not move the gate to level 40. Retain the gate at level 30.**

The data does not support the hypothesis that a later gate improves retention. The statistically significant decrease in 7-day retention under gate_40 indicates that the current gate placement is the stronger design choice for long-term player engagement.
