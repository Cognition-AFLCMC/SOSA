# SOSA Conformance Levels

## How SOSA's Tiered Conformance Model Maps to Sabre

SOSA defines a tiered approach to conformance that acknowledges not all components require the same level of verification rigor. We adapted this model for the Sabre tool.

---

## SOSA's Approach

SOSA uses a layered conformance model where:
- Different component types have different conformance requirements
- Conformance is progressive — higher tiers include lower tier requirements
- Verification rigor increases with component criticality

## Adapted Conformance Model for Sabre

```
┌─────────────────────────────────────────────────────────────┐
│           SABRE CONFORMANCE TIERS (SOSA-Derived)            │
│                                                              │
│   Tier 3: APPLICATION                                        │
│   ┌──────────────────────────────────────────────────┐      │
│   │ Mission-specific logic                            │      │
│   │ Lowest verification rigor                         │      │
│   │ Checks: Basic interface + documentation           │      │
│   │ Threshold: 80% weighted score                     │      │
│   │ Examples: display widgets, logging services       │      │
│   └──────────────────────────────────────────────────┘      │
│                                                              │
│   Tier 2: SUBSYSTEM                                          │
│   ┌──────────────────────────────────────────────────┐      │
│   │ Domain-specific processing                        │      │
│   │ Medium verification rigor                         │      │
│   │ Checks: Interface + behavioral + documentation    │      │
│   │ Threshold: 85% weighted score                     │      │
│   │ Examples: navigation, communications, sensors     │      │
│   └──────────────────────────────────────────────────┘      │
│                                                              │
│   Tier 1: PLATFORM                                           │
│   ┌──────────────────────────────────────────────────┐      │
│   │ Core infrastructure services                      │      │
│   │ Highest verification rigor                        │      │
│   │ Checks: All categories including security + perf  │      │
│   │ Threshold: 90% weighted score                     │      │
│   │ Examples: message bus, lifecycle manager, security │      │
│   └──────────────────────────────────────────────────┘      │
│                                                              │
│   OMS Tier Assignment (from OMS §4.3):                       │
│   ┌──────────────────────────────────────────────────┐      │
│   │ Tier is auto-calculated from:                     │      │
│   │   - Number of interfaces exposed                  │      │
│   │   - Number of dependent MPUs                      │      │
│   │   - Service criticality classification            │      │
│   │   - Real-time constraint requirements             │      │
│   └──────────────────────────────────────────────────┘      │
└─────────────────────────────────────────────────────────────┘
```

---

## Conformance Status Definitions

| Status | Criteria | Action Required |
|--------|----------|----------------|
| **CONFORMANT** | All CRITICAL pass, no HIGH fails, score ≥ tier threshold | None — approved for integration |
| **CONDITIONALLY CONFORMANT** | All CRITICAL pass, score ≥ (threshold - 15%) | Remediation plan required within 30 days |
| **NON-CONFORMANT** | Any CRITICAL fail OR score < (threshold - 15%) | Cannot integrate — must remediate and re-verify |

---

## Check Distribution by Tier

```
                    Tier 3     Tier 2     Tier 1
                    (App)      (Subsys)   (Platform)
                    ─────────  ─────────  ─────────
Interface           ✓ 18       ✓ 18       ✓ 18
Behavioral          -           ✓ 10       ✓ 10
Documentation       ✓ 15       ✓ 15       ✓ 15
Structural          -           ✓ 12       ✓ 12
Security            -           -           ✓ 8
Performance         -           -           ✓ 4
                    ─────────  ─────────  ─────────
Total Checks:       33         55          67
Threshold:          80%        85%         90%
```

---

## Why Tiered Conformance Matters

1. **Proportional Rigor**: A display widget shouldn't need the same verification as the message bus
2. **Scalability**: With hundreds of MPUs across CCA vendors, tier-based profiling focuses effort where it matters
3. **Credibility**: Mirrors SOSA's established approach — AF evaluators will recognize the pattern
4. **Efficiency**: Lower-tier MPUs verify faster, allowing batch processing of large submissions

---

## Classification

All content is **UNCLASSIFIED**. Derived from publicly available SOSA conformance program descriptions and OMS v2.5 tier definitions.
