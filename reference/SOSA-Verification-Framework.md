# SOSA Verification Framework

## The 5-Stage Verification Pipeline

SOSA (Sensor Open Systems Architecture) defines a structured verification process used by the DoD to verify conformance of system components to open architecture standards. This document details each stage and its purpose.

---

## Stage 1: DEFINE

### Purpose
Establish the set of standards, requirements, and conformance criteria that apply to the system being verified.

### Activities
- Identify applicable standards (e.g., OMS v2.5, UCI v2.5, AMS GRA)
- Extract verifiable requirements from standards documents
- Categorize requirements by type (interface, behavioral, documentation, structural)
- Define pass/fail criteria for each requirement
- Establish severity levels (Critical, High, Medium, Low)

### Inputs
- Published standards documents
- Reference architecture specifications
- Program-specific conformance requirements

### Outputs
- Requirements registry (machine-readable)
- Verification rule set
- Conformance criteria matrix

```
DEFINE Stage in Our Tool:
─────────────────────────
Standards (OMS, UCI, FACE CTS)
    │
    ▼
┌──────────────────────────┐
│  YAML Rule Generator     │
│                          │
│  Parse standards docs    │
│  Extract requirements    │
│  Generate YAML rules     │
│  Assign severity levels  │
└──────────────────────────┘
    │
    ▼
rules/
├── oms-v25.yaml      (104 requirements)
├── uci-v25.yaml      (message schema rules)
└── ams-gra-stub.yaml (placeholder for CUI rules)
```

---

## Stage 2: PROFILE

### Purpose
Select the subset of verification checks that apply to a specific target component, based on its type, tier, and capabilities.

### Activities
- Classify the target component (type, tier, category)
- Select applicable verification rules from the full rule set
- Exclude rules that don't apply to this component type
- Configure tier-specific thresholds
- Generate a verification profile for the target

### Inputs
- Target component metadata (type, tier, version)
- Full verification rule set (from DEFINE stage)
- Component documentation (service contract, manifest)

### Outputs
- Tailored verification profile
- List of applicable checks with expected results
- Test execution plan

```
PROFILE Stage in Our Tool:
──────────────────────────
MPU Metadata
    │
    ▼
┌──────────────────────────┐
│  Rule Selector           │
│                          │
│  Read MPU manifest       │
│  Determine OMS Tier      │
│  Filter applicable rules │
│  Generate check list     │
└──────────────────────────┘
    │
    ▼
Verification Profile:
  MPU: nav_mpu
  Tier: 2 (Subsystem)
  Applicable Rules: 87/104
  Excluded: 17 (Tier 3 only)
```

---

## Stage 3: TEST

### Purpose
Execute the verification checks against the target component and collect raw results.

### Activities
- Ingest target component (source code, binaries, documentation)
- Execute each applicable check from the verification profile
- Collect evidence for each check (code references, excerpts, measurements)
- Record pass/fail/warning status for each check
- Capture execution metadata (timestamp, duration, environment)

### Inputs
- Target component (source code, documentation, artifacts)
- Verification profile (from PROFILE stage)
- Test environment configuration

### Outputs
- Raw test results (per-check pass/fail/warning)
- Evidence collection (code references, screenshots, measurements)
- Execution log

```
TEST Stage in Our Tool:
───────────────────────
MPU Source Code + Docs
    │
    ▼
┌──────────────────────────┐
│  Verification Engine     │
│                          │
│  Static Analysis:        │
│    ├─ AST parsing        │
│    ├─ Pattern matching   │
│    ├─ API usage checks   │
│    └─ Schema validation  │
│                          │
│  Document Analysis:      │
│    ├─ Service Contract   │
│    ├─ Checklist review   │
│    └─ Message validation │
│                          │
│  Behavioral Analysis:    │
│    ├─ State machine      │
│    ├─ Lifecycle checks   │
│    └─ Timing constraints │
└──────────────────────────┘
    │
    ▼
Raw Results:
  95 checks executed
  87 PASS
   5 FAIL
   3 WARNING
  Duration: 42 seconds
```

---

## Stage 4: ASSESS

### Purpose
Evaluate raw test results against conformance criteria to determine overall conformance status.

### Activities
- Aggregate per-check results into category scores
- Apply severity-weighted scoring
- Compare against conformance thresholds
- Identify critical failures (any CRITICAL severity fail = non-conformant)
- Generate risk assessment for warnings
- Produce summary assessment

### Inputs
- Raw test results (from TEST stage)
- Conformance criteria (from DEFINE stage)
- Severity weights and thresholds

### Outputs
- Conformance score (weighted percentage)
- Category-level scores
- Risk assessment
- Remediation recommendations
- Assessment summary

```
ASSESS Stage in Our Tool:
─────────────────────────
Raw Results
    │
    ▼
┌──────────────────────────┐
│  Assessment Engine       │
│                          │
│  Score Calculation:       │
│    CRITICAL fails: 0     │
│    HIGH fails: 3         │
│    MEDIUM fails: 2       │
│    Weighted Score: 91.4% │
│                          │
│  Threshold Check:        │
│    Tier 2 requires: 85%  │
│    Result: PASS          │
│                          │
│  Risk Assessment:        │
│    3 warnings need review│
└──────────────────────────┘
    │
    ▼
Assessment Report:
  Overall: CONDITIONALLY CONFORMANT
  Score: 91.4%
  Blockers: 0 CRITICAL
  Requires: 5 remediations
```

---

## Stage 5: CERTIFY

### Purpose
Issue a formal conformance determination and generate the official conformance report.

### Activities
- Review assessment results for completeness
- Apply any manual review overrides (for edge cases)
- Generate formal conformance report
- Issue conformance status (Conformant / Conditionally Conformant / Non-Conformant)
- Archive evidence and results for audit trail
- Generate change package for non-conformant items

### Inputs
- Assessment summary (from ASSESS stage)
- Manual review inputs (if any)
- Certification authority requirements

### Outputs
- Conformance certificate/status
- Formal conformance report
- Evidence archive
- Change package (for non-conformant items)
- Audit trail

```
CERTIFY Stage in Our Tool:
──────────────────────────
Assessment Report
    │
    ▼
┌──────────────────────────┐
│  Report Generator        │
│                          │
│  Conformance Status:     │
│    ☐ CONFORMANT          │
│    ☑ CONDITIONAL         │
│    ☐ NON-CONFORMANT      │
│                          │
│  Deliverables:           │
│    ├─ PDF Report         │
│    ├─ JSON Machine Data  │
│    ├─ Change Package     │
│    └─ Evidence Archive   │
└──────────────────────────┘
    │
    ▼
Dashboard Display:
  ┌─────────────────────┐
  │ nav_mpu      ✓ 91%  │
  │ comms_mpu    ✓ 94%  │
  │ sensor_mpu   ⚠ 88%  │
  │ ew_mpu       ✗ 72%  │
  └─────────────────────┘
```

---

## SOSA Conformance Levels

SOSA defines multiple conformance levels that we adapted for the Sabre tool:

```
┌─────────────────────────────────────────────────────────────┐
│              CONFORMANCE LEVELS (SOSA-Derived)              │
├──────────────┬──────────────────────────────────────────────┤
│ CONFORMANT   │ All CRITICAL and HIGH checks pass            │
│              │ Weighted score ≥ 95%                         │
│              │ No unresolved warnings                       │
├──────────────┼──────────────────────────────────────────────┤
│ CONDITIONALLY│ All CRITICAL checks pass                     │
│ CONFORMANT   │ Weighted score ≥ 80%                         │
│              │ Remediation plan for failures                │
├──────────────┼──────────────────────────────────────────────┤
│ NON-         │ Any CRITICAL check fails                     │
│ CONFORMANT   │ OR weighted score < 80%                      │
│              │ Requires significant remediation             │
└──────────────┴──────────────────────────────────────────────┘
```

---

## Classification

All content is **UNCLASSIFIED**. Derived from publicly available SOSA program descriptions and our own architectural analysis. No proprietary Open Group member content is included.
