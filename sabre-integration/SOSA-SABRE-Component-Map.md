# SOSA-SABRE Component Map

## How SABRE Implements the SOSA Verification Framework

> **Classification**: UNCLASSIFIED — Derived from `sosa_framework.py` in SABRE-Conformance-Application-.
>
> **Source**: `sabre-conformance-tool/src/sabre/engine/sosa_framework.py`

---

## Overview

SABRE's verification pipeline is a direct implementation of the SOSA 5-stage verification model. The `sosa_framework.py` module wraps the core SABRE verification engine with SOSA stage tracking, producing a fully traceable verification record that maps every action to its corresponding SOSA stage.

---

## SOSA_SABRE_MAPPING

The following mapping is taken directly from the SABRE implementation. It defines how each SOSA verification stage maps to a specific SABRE component:

| SOSA Stage | SOSA Element | SABRE Component | Data Flow | Artifacts |
|-----------|-------------|----------------|-----------|-----------|
| **1. DEFINE** | Verification Criteria Definition | Rule Generator | Standards (OMS/UCI/MOSA) → YAML Rules | `rules/*.yaml` |
| **2. PROFILE** | System Profiling | Rule Selector | MPU Metadata → Filtered Check List | `manifest.yaml`, `mpu_metadata.json` |
| **3. TEST** | Test Execution | Verification Engine (3-engine pipeline) | MPU Code → Raw Results | `engine_results.json` |
| **4. ASSESS** | Results Assessment | Scoring Engine | Raw Results → Tiered Scores | `conformance_score.json` |
| **5. CERTIFY** | Certification | Report Generator | Scores → Reports/Dashboard | `report.pdf`, `report.html`, `dashboard` |

---

## SOSAVerificationPipeline

The `SOSAVerificationPipeline` class in `sosa_framework.py` orchestrates the full 5-stage process:

```
SOSAVerificationPipeline
├── __init__(mpu_path, rules_dir, verification_id)
│   └── Creates SOSAVerificationResult with unique ID
│
├── execute(tier_filter) → SOSAVerificationResult
│   ├── Stage 1: DEFINE — Load rules from YAML files
│   ├── Stage 2: PROFILE — Parse MPU manifest, filter rules
│   ├── Stage 3: TEST — Run 3-engine verification pipeline
│   ├── Stage 4: ASSESS — Calculate weighted conformance score
│   └── Stage 5: CERTIFY — Generate reports and certification status
│
└── Returns SOSAVerificationResult with:
    ├── verification_id (unique tracking ID)
    ├── mpu_name and mpu_path
    ├── stage_results (per-stage timing and artifacts)
    ├── overall_status (CONFORMANT / NON_CONFORMANT)
    ├── conformance_score
    └── started_at / completed_at timestamps
```

---

## Stage-by-Stage Implementation Detail

### Stage 1: DEFINE — Rule Generator

**SOSA Element**: Verification Criteria Definition

The DEFINE stage loads verification rules from YAML files. Rules are organized by source standard:

| Rule Directory | Source Standard | Checks |
|---------------|----------------|--------|
| `rules/oms-v25/` | OMS v2.5 | 339 verification checks |
| `rules/uci-v25/` | UCI v2.5 | Message type validation |
| `rules/mosa/` | 10 U.S.C. § 4401 | 67 MOSA principle checks |
| `rules/ams-gra-14/` | AMS GRA v14.0+ | Architecture-specific (CUI) |

Rules are Pydantic-validated YAML files with fields: `id`, `description`, `standard_reference`, `check_type`, `pass_criteria`, `severity`, `tier`, `allocation`.

### Stage 2: PROFILE — Rule Selector

**SOSA Element**: System Profiling

The PROFILE stage reads the MPU's `manifest.yaml` to determine:
- **Target type**: service, subsystem, platform, isolator, or mission_package
- **Tier**: 1 (Platform), 2 (Subsystem), or 3 (Application)
- **Capabilities**: Which standard features the MPU implements

Rules are then filtered to only those applicable to the target's type and tier.

### Stage 3: TEST — Verification Engine

**SOSA Element**: Test Execution

The TEST stage runs the filtered rules through SABRE's 3-engine pipeline:

| Engine | Handles | Analysis Method |
|--------|---------|----------------|
| Interface Engine | `interface_signature`, `schema_validation`, `static_analysis` | AST parsing, import analysis, XSD matching |
| Behavioral Engine | `state_machine`, `behavioral_test` | State transition analysis, pattern matching |
| Documentation Engine | `document_inspection`, `checklist_validation` | File presence, section completeness |

Each rule is dispatched to the appropriate engine based on its `check_type`. Specialized checkers handle well-known rule prefixes (e.g., `CAL-API-*`, `SVC-LIFECYCLE-*`). All other rules fall through to a generic checker.

### Stage 4: ASSESS — Scoring Engine

**SOSA Element**: Results Assessment

The ASSESS stage calculates a weighted conformance score:

```
Severity Weights:
  CRITICAL = 10x
  MAJOR    = 5x
  MINOR    = 2x

weighted_score = Σ(passed × weight) / Σ(all × weight) × 100

Tier Thresholds:
  ≥ 90% → Tier 1 (Platform)
  ≥ 85% → Tier 2 (Subsystem)
  ≥ 80% → Tier 3 (Application)
  < 80% → Non-Conformant

Critical Failure Override:
  ANY critical-severity rule with status=FAIL → automatic NON_COMPLIANT
```

### Stage 5: CERTIFY — Report Generator

**SOSA Element**: Certification

The CERTIFY stage produces conformance reports and dashboard entries:

| Output | Format | Contents |
|--------|--------|----------|
| PDF Report | `report.pdf` | Executive summary, per-check results, remediation |
| HTML Report | `report.html` | Interactive version with drill-down |
| JSON Export | `report.json` | Machine-readable for CI/CD integration |
| Dashboard | Flask web app | Fleet-wide view, vendor comparison, trends |

---

## SOSAVerificationResult

The verification result object captures the complete audit trail:

```
SOSAVerificationResult:
  verification_id:   "SOSA-nav_mpu-20260414120000"
  mpu_name:          "nav_mpu"
  mpu_path:          "./nav_mpu"
  rules_dir:         "rules/oms-v25"
  started_at:        "2026-04-14T12:00:00Z"
  completed_at:      "2026-04-14T12:00:47Z"
  stage_results:
    DEFINE:   { duration: 0.2s, rules_loaded: 339 }
    PROFILE:  { duration: 0.1s, rules_filtered: 87 }
    TEST:     { duration: 45.3s, checks_executed: 87 }
    ASSESS:   { duration: 0.5s, weighted_score: 92.1 }
    CERTIFY:  { duration: 1.2s, report_paths: [...] }
  overall_status:    "CONFORMANT"
  conformance_tier:  1
  conformance_score: 92.1
```

---

## Key Design Principles

1. **Full Traceability**: Every verification result traces back through SOSA stages to source standards
2. **Stage Isolation**: Each stage can be re-run independently (e.g., re-assess with new thresholds)
3. **Deterministic**: Same MPU + same rules = same result, every time
4. **Auditable**: Complete timestamp and artifact trail for each stage
5. **Extensible**: New stages or sub-stages can be added without restructuring

---

## Classification

All content is **UNCLASSIFIED**. Derived from the publicly available SABRE-Conformance-Application- repository code.
