# Stage 5: CERTIFY

## Issue Conformance Status

> **SOSA Stage**: 5 of 5
> **SABRE Component**: Report Generator + Fleet Dashboard
> **Classification**: UNCLASSIFIED

---

## Purpose

The CERTIFY stage **issues a formal conformance determination** based on the assessment results. This is the final stage — it produces the official conformance status, generates reports, and records the result for audit and tracking purposes.

## SOSA Framework

In the SOSA verification model, the CERTIFY stage answers the question:

> "What is the official conformance status, and how is it communicated?"

This involves:
1. Issuing a formal conformance determination (conformant/non-conformant)
2. Generating detailed reports with evidence for each finding
3. Recording the result in a product registry or dashboard
4. Providing remediation guidance for non-conformant items
5. Enabling trend tracking and fleet-wide visibility

## SABRE Implementation

### Component: Report Generator + Fleet Dashboard

SABRE implements the CERTIFY stage through two complementary outputs:

```
┌─────────────────────────────────────────────────────────────┐
│              SABRE CERTIFICATION OUTPUTS                     │
│                                                              │
│   ┌──────────────────────────────────────────┐              │
│   │ Report Generator                          │              │
│   │                                           │              │
│   │  PDF Report: Formal conformance document  │              │
│   │  - Executive summary                      │              │
│   │  - Per-check results with evidence        │              │
│   │  - Remediation guidance                   │              │
│   │  - Tier determination with score          │              │
│   │                                           │              │
│   │  JSON Report: Machine-readable output     │              │
│   │  - Same data, structured for automation   │              │
│   │  - Enables CI/CD gate integration         │              │
│   └──────────────────────────────────────────┘              │
│                                                              │
│   ┌──────────────────────────────────────────┐              │
│   │ Fleet Dashboard (Web UI)                  │              │
│   │                                           │              │
│   │  Fleet-wide conformance view across       │              │
│   │  all verified MPUs, all vendors:          │              │
│   │  - Per-MPU conformance status             │              │
│   │  - Vendor comparison                      │              │
│   │  - Trend tracking over time               │              │
│   │  - Drill-down to individual findings      │              │
│   └──────────────────────────────────────────┘              │
│                                                              │
│   ┌──────────────────────────────────────────┐              │
│   │ Change Packages (Remediation)             │              │
│   │                                           │              │
│   │  For each violation, SABRE generates      │              │
│   │  actionable remediation guidance:         │              │
│   │  - What the violation is                  │              │
│   │  - Why it matters (standard reference)    │              │
│   │  - How to fix it (code changes)           │              │
│   │  - Optional: Devin auto-generates fix PR  │              │
│   └──────────────────────────────────────────┘              │
└─────────────────────────────────────────────────────────────┘
```

### Report Contents

| Section | Contents |
|---------|----------|
| Executive Summary | Overall score, tier, pass/fail counts, critical findings |
| Findings Detail | Per-check results with file, line, excerpt evidence |
| Remediation | Prioritized list of fixes with effort estimates |
| Standards Traceability | Mapping from each finding to source standard section |
| Metadata | MPU name, version, date, rule set used, operator |

### Fleet Dashboard Features

| Feature | Description |
|---------|-------------|
| Multi-MPU View | See all verified MPUs in a single dashboard |
| Vendor Comparison | Compare conformance across different vendors |
| Trend Tracking | Track conformance scores over time |
| Drill-Down | Click through from fleet view → MPU → finding → code |
| Export | Export fleet data as CSV or JSON for external analysis |

### Certification Workflow

```
Assessment Complete
    │
    ├── Score ≥ Tier Threshold AND no critical failures
    │   └── Status: CONFORMANT (Tier 1/2/3)
    │       └── Record in fleet dashboard
    │       └── Generate certification report
    │
    └── Score < Threshold OR critical failure detected
        └── Status: NON-CONFORMANT
            └── Generate remediation change package
            └── Record in fleet dashboard
            └── (Optional) Devin generates fix PR
```

## SOSA Traceability

| SOSA Element | SABRE Implementation |
|-------------|---------------------|
| Certification Decision | Tier determination based on score + critical failures |
| Certification Report | PDF + JSON report with evidence |
| Product Registration | Fleet dashboard with per-MPU status |
| Remediation Guidance | Change packages with fix instructions |
| Audit Trail | Git-versioned reports and dashboard history |

---

## Classification

All content is **UNCLASSIFIED**. Derived from publicly available SOSA process descriptions and SABRE tool architecture.
