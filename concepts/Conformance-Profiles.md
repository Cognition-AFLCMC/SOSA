# Conformance Profiles

## SOSA Conformance Profile Concept → SABRE Tiered Conformance

> **Classification**: UNCLASSIFIED — Derived from publicly available SOSA and OMS documentation.

---

## Overview

In the SOSA framework, a **conformance profile** defines the specific set of requirements that a product must satisfy for a given level of conformance. Rather than requiring all products to meet all requirements, conformance profiles allow for tiered verification based on the product's role and scope within the system.

SABRE implements this concept through its **tiered conformance model**, which maps SOSA's profile concept to OMS-derived thresholds.

---

## SOSA Conformance Profile Concept

SOSA defines conformance profiles as:

- **A subset of the full standard** — Not every requirement applies to every product
- **Role-based** — Different profiles for different system roles (sensor, processor, display, etc.)
- **Graduated** — Multiple levels of conformance, from minimal to full
- **Verifiable** — Each profile has clear, testable pass/fail criteria

### Profile Components

| Component | Description |
|-----------|-------------|
| Applicable Requirements | Which requirements from the standard apply |
| Threshold | What percentage of requirements must pass |
| Critical Requirements | Requirements that must pass regardless of score |
| Scope | System level the profile applies to (component, subsystem, platform) |

---

## SABRE Tiered Conformance Model

SABRE maps SOSA's conformance profile concept to three tiers derived from OMS compliance checklists:

```
┌─────────────────────────────────────────────────────────────┐
│              SABRE TIERED CONFORMANCE                        │
│                                                              │
│   ┌─────────────────────────────────────────────────┐       │
│   │  Tier 1: PLATFORM (90% threshold)               │       │
│   │  Full system-level conformance                   │       │
│   │  All rules apply, highest standards              │       │
│   │  Applicable: Complete CCA system assessment      │       │
│   └─────────────────────────────────────────────────┘       │
│                                                              │
│   ┌─────────────────────────────────────────────────┐       │
│   │  Tier 2: SUBSYSTEM (85% threshold)              │       │
│   │  Integrated MPU group conformance                │       │
│   │  Cross-MPU interface rules apply                 │       │
│   │  Applicable: Vendor subsystem integration        │       │
│   └─────────────────────────────────────────────────┘       │
│                                                              │
│   ┌─────────────────────────────────────────────────┐       │
│   │  Tier 3: APPLICATION (80% threshold)            │       │
│   │  Individual MPU conformance                      │       │
│   │  Core interface and documentation rules          │       │
│   │  Applicable: Single MPU verification             │       │
│   └─────────────────────────────────────────────────┘       │
│                                                              │
│   Below 80%: NON-CONFORMANT                                 │
│   Any CRITICAL failure: AUTO-FAIL regardless of score       │
└─────────────────────────────────────────────────────────────┘
```

### Tier-to-Profile Mapping

| SOSA Profile Concept | SABRE Tier | Threshold | Rule Scope |
|---------------------|-----------|-----------|------------|
| Full System Profile | Tier 1 | 90% | All rules (339+ checks) |
| Subsystem Profile | Tier 2 | 85% | Cross-MPU + MPU rules |
| Component Profile | Tier 3 | 80% | MPU-specific rules |

---

## How Profiles Drive Verification

The conformance profile determines what gets checked and what the pass criteria are:

```
MPU Manifest declares: tier = 3 (Application)
    │
    ├── Load all rules from selected rule set
    ├── Filter to tier-3-applicable rules
    ├── Execute filtered checks
    ├── Calculate weighted score
    └── Compare against 80% threshold
        ├── ≥ 80% AND no critical failures → Tier 3 CONFORMANT
        └── < 80% OR critical failure → NON-CONFORMANT
```

---

## Classification

All content is **UNCLASSIFIED**. Derived from publicly available SOSA process descriptions and OMS compliance documentation.
