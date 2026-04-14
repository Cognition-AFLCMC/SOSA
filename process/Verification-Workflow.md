# Verification Workflow

## Sabre Conformance Tool — SOSA-Based Verification Process

This document describes the end-to-end verification workflow for the Sabre Conformance Tool, modeled on the SOSA 5-stage verification pipeline.

---

## Workflow Overview

```
                        VERIFICATION WORKFLOW
                        ═════════════════════

  Vendor/VA Team submits MPU for verification
        │
        ▼
  ┌─────────────────────────────────────────────────┐
  │  1. INGEST                                       │
  │     ├─ Upload MPU source code                    │
  │     ├─ Upload service contract (OMSC-TMP-003)    │
  │     ├─ Upload compliance checklist (OMSC-CHK-005)│
  │     └─ Upload manifest.yaml (metadata)           │
  │                                                   │
  │     CLI: sabre verify ./nav_mpu --rules oms-v25  │
  │     Web: Dashboard → New Verification → Upload   │
  └────────────────────┬────────────────────────────┘
                       │
                       ▼
  ┌─────────────────────────────────────────────────┐
  │  2. PROFILE (SOSA Stage 2)                       │
  │     ├─ Read MPU manifest (tier, type, version)   │
  │     ├─ Load rule set (oms-v25.yaml)              │
  │     ├─ Filter rules for this MPU's tier          │
  │     ├─ Generate verification profile             │
  │     └─ Display: "87 checks will be executed"     │
  │                                                   │
  │     Duration: <1 second                          │
  └────────────────────┬────────────────────────────┘
                       │
                       ▼
  ┌─────────────────────────────────────────────────┐
  │  3. VERIFY (SOSA Stage 3)                        │
  │     ├─ Parse source code → AST                   │
  │     ├─ Run interface compliance checks           │
  │     │   ├─ CAL API usage verification            │
  │     │   ├─ UCI message schema validation         │
  │     │   └─ Transport service compliance          │
  │     ├─ Run behavioral checks                     │
  │     │   ├─ Lifecycle state machine validation    │
  │     │   ├─ Timing constraint verification        │
  │     │   └─ Error handling patterns               │
  │     ├─ Run documentation checks                  │
  │     │   ├─ Service contract completeness         │
  │     │   ├─ Checklist verification                │
  │     │   └─ API documentation coverage            │
  │     └─ Collect evidence for each check           │
  │                                                   │
  │     Duration: 30-60 seconds (static analysis)    │
  │     Duration: 2-5 minutes (with Devin deep scan) │
  └────────────────────┬────────────────────────────┘
                       │
                       ▼
  ┌─────────────────────────────────────────────────┐
  │  4. SCORE (SOSA Stage 4)                         │
  │     ├─ Calculate weighted conformance score      │
  │     ├─ Determine conformance status              │
  │     │   ├─ CONFORMANT (≥ tier threshold)         │
  │     │   ├─ CONDITIONAL (within 15% of threshold) │
  │     │   └─ NON-CONFORMANT (below or CRIT fail)   │
  │     ├─ Generate risk assessment                  │
  │     └─ Identify top remediation priorities       │
  │                                                   │
  │     Duration: <1 second                          │
  └────────────────────┬────────────────────────────┘
                       │
                       ▼
  ┌─────────────────────────────────────────────────┐
  │  5. REPORT (SOSA Stage 5)                        │
  │     ├─ Generate PDF conformance report           │
  │     ├─ Generate JSON machine-readable results    │
  │     ├─ Update fleet dashboard                    │
  │     ├─ Archive evidence for audit trail          │
  │     └─ (Optional) Generate change package PRs    │
  │                                                   │
  │     Duration: <5 seconds                         │
  └────────────────────┬────────────────────────────┘
                       │
                       ▼
  ┌─────────────────────────────────────────────────┐
  │  6. REMEDIATE (Sabre Extension — Beyond SOSA)    │
  │     ├─ Devin analyzes each failure               │
  │     ├─ Generates fix suggestions with code       │
  │     ├─ Creates change package (PR-ready patches) │
  │     └─ Vendor reviews and applies fixes          │
  │                                                   │
  │     Duration: 5-15 minutes (Devin-powered)       │
  │     ★ THIS IS OUR DIFFERENTIATOR ★              │
  └─────────────────────────────────────────────────┘
```

---

## User Journeys

### Journey 1: VA Team Member (Verification Agent)

```
1. Log into Sabre dashboard
2. Click "New Verification"
3. Upload MPU package (zip containing source + docs)
4. Select rule set (OMS v2.5 / AMS GRA)
5. Click "Run Verification"
6. Wait 30-60 seconds
7. View results dashboard
8. Download PDF report
9. (Optional) Click "Ask Devin" for deep analysis
10. (Optional) Click "Generate Fixes" for change package
```

### Journey 2: Vendor Developer (Self-Verification)

```
1. Install Sabre CLI: pip install sabre-conformance
2. Run: sabre verify ./my_mpu --rules oms-v25
3. View terminal output (pass/fail summary)
4. Fix flagged issues
5. Re-run until conformant
6. Submit MPU package to VA team with confidence
```

### Journey 3: Program Manager (Fleet Oversight)

```
1. Open Sabre dashboard
2. View Fleet Overview (all MPUs across all vendors)
3. Filter by vendor / status / date
4. Drill into specific MPU for details
5. Track conformance trends over time
6. Export fleet report for milestone review
```

---

## Error Handling

| Scenario | Behavior |
|----------|----------|
| Invalid MPU package | Error message with specific missing files/format issues |
| Unsupported language | Warning + skip code analysis checks, run doc checks only |
| Missing service contract | Flag as CRITICAL failure in documentation category |
| Rule set not found | Error message + list available rule sets |
| Timeout during analysis | Partial results returned with indication of incomplete checks |
| Devin API unavailable | Graceful degradation — static analysis only, no deep scan |

---

## Classification

All content is **UNCLASSIFIED**. Process workflow is our own design, inspired by publicly available SOSA verification framework.
