# Stage 4: ASSESS

## Evaluate Results Against Criteria

> **SOSA Stage**: 4 of 5
> **SABRE Component**: Scoring Engine
> **Classification**: UNCLASSIFIED

---

## Purpose

The ASSESS stage **evaluates raw test results** against defined criteria to produce a conformance score. Raw pass/fail results from the TEST stage are aggregated, weighted by severity, and compared against tier thresholds to determine conformance status.

## SOSA Framework

In the SOSA verification model, the ASSESS stage answers the question:

> "Based on the test results, does this system meet the required conformance level?"

This involves:
1. Aggregating pass/fail results from all executed checks
2. Applying severity-based weighting to results
3. Calculating a weighted conformance score
4. Comparing the score against tier-specific thresholds
5. Identifying critical failures that override scoring

## SABRE Implementation

### Component: Scoring Engine

SABRE implements the ASSESS stage through its **tiered scoring engine**, which applies severity-weighted calculations and threshold comparisons.

### Severity Weighting

```
Severity    Weight    Impact on Score
──────────  ──────    ───────────────
CRITICAL    10x       Single failure can trigger auto-fail
HIGH         5x       Significant impact on overall score
MEDIUM       2x       Moderate impact
LOW          1x       Minor impact
```

### Tiered Thresholds

```
┌─────────────────────────────────────────────────────────────┐
│              TIERED CONFORMANCE MODEL                        │
│                                                              │
│   Tier 1: PLATFORM    ──── 90% threshold                    │
│   (Full system-level conformance)                            │
│                                                              │
│   Tier 2: SUBSYSTEM   ──── 85% threshold                    │
│   (Integrated MPU groups)                                    │
│                                                              │
│   Tier 3: APPLICATION ──── 80% threshold                    │
│   (Individual MPU)                                           │
│                                                              │
│   Below Tier 3 ─────── NON-CONFORMANT                       │
│   Any CRITICAL failure ── AUTO-FAIL regardless of score     │
└─────────────────────────────────────────────────────────────┘
```

### Score Calculation

```
weighted_score = Σ(passed_checks × severity_weight) / Σ(all_checks × severity_weight) × 100

Example:
  30 checks total
  25 passed (5 critical×10, 10 high×5, 10 medium×2)
  5 failed (2 high×5, 3 medium×2)

  Passed weight: (5×10) + (10×5) + (10×2) = 120
  Total weight:  (5×10) + (12×5) + (13×2) = 136
  Score: 120/136 = 88.2% → Tier 2 (SUBSYSTEM)
```

### Critical Failure Override

Certain checks are marked as **critical failures** — if any of these fail, the overall assessment is automatically set to NON-CONFORMANT regardless of the numerical score:

- Direct ASB access (bypassing CAL API) — OMS §5.3 violation
- Missing required lifecycle states — OMS §6.1 violation
- Use of proprietary communication protocols — MOSA violation

### Data Flow

```
┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  Raw Results      │     │  Scoring Engine   │     │  Conformance     │
│  (pass/fail per   │────▶│  (weight, sum,    │────▶│  Assessment      │
│   check)          │     │   threshold)      │     │  (tier + score)  │
└──────────────────┘     └──────────────────┘     └──────────────────┘
```

## SOSA Traceability

| SOSA Element | SABRE Implementation |
|-------------|---------------------|
| Assessment Criteria | Severity weights + tier thresholds |
| Score Calculation | Weighted pass rate formula |
| Override Rules | Critical failure auto-fail |
| Assessment Artifacts | `conformance_score.json` |

---

## Classification

All content is **UNCLASSIFIED**. Derived from publicly available SOSA process descriptions and SABRE tool architecture.
