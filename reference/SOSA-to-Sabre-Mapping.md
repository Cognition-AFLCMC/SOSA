# SOSA to Sabre Tool Mapping

## How Each SOSA Stage Maps to Our Conformance Tool Architecture

This document traces how each stage of the SOSA verification pipeline maps to specific components, data flows, and outputs in the Sabre Conformance Tool.

---

## Overview

```
SOSA Stage    →    Sabre Component           →    Data Flow
──────────────────────────────────────────────────────────────
1. DEFINE     →    Rule Generator            →    Standards → YAML Rules
2. PROFILE    →    Rule Selector             →    MPU Metadata → Check List
3. TEST       →    Verification Engine       →    MPU Code → Raw Results
4. ASSESS     →    Assessment Engine         →    Raw Results → Scores
5. CERTIFY    →    Report Generator          →    Scores → Reports/Dashboard
```

---

## Stage 1: DEFINE → Rule Generator

| SOSA Activity | Sabre Implementation |
|--------------|---------------------|
| Identify applicable standards | Parse OMS v2.5, UCI v2.5 documents |
| Extract verifiable requirements | Transform Excel checklists (OMSC-CHK-*) into structured data |
| Categorize requirements | Map to categories: interface, behavioral, documentation, structural |
| Define pass/fail criteria | Encode as YAML rules with check_type, expected_value, severity |
| Establish severity levels | 4-tier: CRITICAL, HIGH, MEDIUM, LOW |

**Sabre Component**: `rules/` directory + rule generation scripts

**Key Files**:
```
rules/
├── oms-v25.yaml           # 104 requirements from OMS compliance checklists
├── uci-v25.yaml           # Message schema validation rules from UCI XSD
├── face-cts-patterns.yaml # Test procedure patterns from FACE CTS
└── ams-gra-stub.yaml      # Placeholder for CUI rules (post-award)
```

**SOSA Alignment**: Our YAML rule schema directly implements SOSA's DEFINE stage output — a machine-readable requirements registry with categorized, severity-weighted verification checks.

---

## Stage 2: PROFILE → Rule Selector

| SOSA Activity | Sabre Implementation |
|--------------|---------------------|
| Classify target component | Read MPU manifest.yaml (type, tier, version) |
| Select applicable rules | Filter rules by MPU tier and type |
| Exclude non-applicable rules | Tier-based exclusion (e.g., Tier 3 rules skip for Tier 1 MPU) |
| Configure thresholds | Load tier-specific pass/fail thresholds |
| Generate verification profile | Produce tailored check list for this MPU |

**Sabre Component**: Profile generation module in the verification engine

**Algorithm**:
```python
def generate_profile(mpu_metadata, full_rule_set):
    tier = mpu_metadata['oms_tier']  # 1, 2, or 3
    mpu_type = mpu_metadata['type']  # e.g., 'navigation', 'comms', 'sensor'
    
    applicable_rules = [
        rule for rule in full_rule_set
        if rule.min_tier <= tier
        and (rule.mpu_types == ['*'] or mpu_type in rule.mpu_types)
    ]
    
    return VerificationProfile(
        mpu=mpu_metadata,
        rules=applicable_rules,
        threshold=TIER_THRESHOLDS[tier]
    )
```

**SOSA Alignment**: This stage ensures we don't apply irrelevant checks. A Tier 1 (Platform) MPU has different requirements than a Tier 3 (Application). SOSA's PROFILE stage recognizes this — not all standards apply to all components.

---

## Stage 3: TEST → Verification Engine

| SOSA Activity | Sabre Implementation |
|--------------|---------------------|
| Ingest target component | Parse MPU source code (AST), docs, service contract |
| Execute checks | Run each rule against parsed MPU artifacts |
| Collect evidence | Store code references, line numbers, excerpts |
| Record results | Pass/Fail/Warning per check with evidence |
| Capture metadata | Timestamp, duration, engine version |

**Sabre Component**: Core verification engine (3 analysis modes)

```
Verification Engine
├── Static Analysis Engine
│   ├── AST Parsing (Python, C++, Java)
│   ├── Pattern Matching (CAL API usage, lifecycle states)
│   ├── Import/Dependency Analysis
│   └── Schema Validation (UCI XSD)
│
├── Document Analysis Engine
│   ├── Service Contract Completeness
│   ├── Compliance Checklist Verification
│   └── Message Definition Validation
│
└── Behavioral Analysis Engine (Devin-powered, optional)
    ├── Deep Code Understanding
    ├── Semantic Interface Matching
    └── Cross-MPU Dependency Detection
```

**SOSA Alignment**: SOSA's TEST stage is about rigorous, evidence-based execution. Our engine collects code-level evidence (file paths, line numbers, code excerpts) for every check — not just pass/fail booleans.

---

## Stage 4: ASSESS → Assessment Engine

| SOSA Activity | Sabre Implementation |
|--------------|---------------------|
| Aggregate results | Roll up per-check results into category scores |
| Apply severity weighting | CRITICAL=10x, HIGH=5x, MEDIUM=2x, LOW=1x |
| Compare against thresholds | Tier-specific thresholds (Tier 1: 90%, Tier 2: 85%, Tier 3: 80%) |
| Identify critical failures | Any CRITICAL fail = automatic NON-CONFORMANT |
| Generate risk assessment | Warnings with risk context and remediation suggestions |

**Sabre Component**: Assessment scoring module

**Scoring Formula**:
```
weighted_score = Σ(passed_checks × severity_weight) / Σ(total_checks × severity_weight)

Conformance Status:
  IF any CRITICAL fail → NON-CONFORMANT
  ELIF weighted_score ≥ tier_threshold AND no HIGH fails → CONFORMANT
  ELIF weighted_score ≥ (tier_threshold - 15%) → CONDITIONALLY CONFORMANT
  ELSE → NON-CONFORMANT
```

**SOSA Alignment**: SOSA emphasizes that raw test results need interpretation. Our assessment engine applies severity-weighted scoring so a single CRITICAL failure (e.g., proprietary interface) correctly overrides dozens of passing LOW checks.

---

## Stage 5: CERTIFY → Report Generator + Dashboard

| SOSA Activity | Sabre Implementation |
|--------------|---------------------|
| Review for completeness | Verify all profile checks were executed |
| Generate formal report | PDF + JSON conformance report |
| Issue conformance status | CONFORMANT / CONDITIONAL / NON-CONFORMANT |
| Archive evidence | Store results + evidence for audit trail |
| Generate change package | Auto-generate remediation PRs (Devin-powered) |

**Sabre Component**: Report generator + web dashboard

**Outputs**:
```
reports/
├── nav_mpu_conformance_report.pdf      # Formal PDF report
├── nav_mpu_conformance_report.json     # Machine-readable results
├── nav_mpu_evidence_archive.zip        # Code excerpts, screenshots
└── nav_mpu_change_package/             # Auto-generated fix PRs
    ├── fix_cal_api_usage.patch
    └── fix_lifecycle_states.patch
```

**Dashboard View**:
```
┌─────────────────────────────────────────────────────┐
│  SABRE CONFORMANCE DASHBOARD                         │
├─────────────────────────────────────────────────────┤
│                                                      │
│  Fleet Overview (4 MPUs verified)                    │
│  ┌───────────────────┬────────┬─────────┐           │
│  │ MPU               │ Score  │ Status  │           │
│  ├───────────────────┼────────┼─────────┤           │
│  │ nav_mpu           │ 95.2%  │ ✓ CONF  │           │
│  │ comms_mpu         │ 92.1%  │ ✓ CONF  │           │
│  │ sensor_fusion_mpu │ 88.4%  │ ⚠ COND  │           │
│  │ ew_counter_mpu    │ 71.3%  │ ✗ NON   │           │
│  └───────────────────┴────────┴─────────┘           │
│                                                      │
│  [View Details] [Generate Report] [Ask Devin]       │
└─────────────────────────────────────────────────────┘
```

**SOSA Alignment**: SOSA's CERTIFY stage produces a formal determination. Our tool goes further — it not only certifies but also generates actionable change packages to remediate failures.

---

## End-to-End Pipeline Comparison

```
SOSA PIPELINE                    SABRE TOOL PIPELINE
═══════════════                  ═══════════════════

┌──────────┐                     ┌──────────────────────┐
│  DEFINE  │ ──────────────────▶ │ Rule Generator       │
│          │                     │ OMS/UCI → YAML rules │
└────┬─────┘                     └─────────┬────────────┘
     │                                      │
┌────▼─────┐                     ┌──────────▼───────────┐
│ PROFILE  │ ──────────────────▶ │ Rule Selector        │
│          │                     │ MPU metadata → checks│
└────┬─────┘                     └──────────┬───────────┘
     │                                      │
┌────▼─────┐                     ┌──────────▼───────────┐
│   TEST   │ ──────────────────▶ │ Verification Engine  │
│          │                     │ Code + docs → results│
└────┬─────┘                     └──────────┬───────────┘
     │                                      │
┌────▼─────┐                     ┌──────────▼───────────┐
│  ASSESS  │ ──────────────────▶ │ Assessment Engine    │
│          │                     │ Results → scores     │
└────┬─────┘                     └──────────┬───────────┘
     │                                      │
┌────▼─────┐                     ┌──────────▼───────────┐
│ CERTIFY  │ ──────────────────▶ │ Report + Dashboard   │
│          │                     │ + Change Packages    │
└──────────┘                     └──────────────────────┘
                                        ▲
                                        │
                                 ┌──────┴───────┐
                                 │ DEVIN LAYER  │
                                 │ (Optional)   │
                                 │ Deep analysis│
                                 │ Fix gen PRs  │
                                 │ NL queries   │
                                 └──────────────┘
```

---

## Classification

All content is **UNCLASSIFIED**. Derived from publicly available SOSA program descriptions and our own architectural analysis.
