# Conformance Assessment

## SOSA Assessment Process → SABRE 3-Engine Pipeline

> **Classification**: UNCLASSIFIED — Derived from publicly available SOSA and SABRE architecture documentation.

---

## Overview

In the SOSA framework, **conformance assessment** is the process of systematically evaluating a product against its applicable conformance profile. SOSA defines the process methodology; SABRE implements it through a three-engine verification pipeline that covers interface, behavioral, and documentation checks.

---

## SOSA Assessment Process

SOSA's conformance assessment methodology follows these principles:

1. **Systematic** — Every applicable requirement is checked, none skipped
2. **Evidence-Based** — Each result includes supporting evidence
3. **Repeatable** — Same product, same checks, same results
4. **Traceable** — Every finding traces back to a specific standard requirement
5. **Independent** — Assessment is performed by a party separate from the developer

### Assessment Categories

SOSA organizes conformance checks into categories based on what aspect of the product is being verified:

| Category | Verifies | Method |
|----------|----------|--------|
| Interface Conformance | External interfaces match the standard | API inspection, protocol analysis |
| Behavioral Conformance | Runtime behavior matches requirements | State machine analysis, timing checks |
| Documentation Conformance | Required documentation is complete | Document inspection, completeness checks |

---

## SABRE 3-Engine Pipeline

SABRE maps SOSA's assessment categories directly to three specialized verification engines:

```
┌─────────────────────────────────────────────────────────────┐
│              SOSA → SABRE ENGINE MAPPING                     │
│                                                              │
│  SOSA Category              SABRE Engine                     │
│  ─────────────              ────────────                     │
│  Interface Conformance  →   Interface Engine                 │
│                              - AST parsing                   │
│                              - Schema validation             │
│                              - Import analysis               │
│                              - API surface inspection        │
│                                                              │
│  Behavioral Conformance →   Behavioral Engine                │
│                              - State machine verification    │
│                              - Lifecycle analysis            │
│                              - Error handling patterns       │
│                              - Dependency management         │
│                                                              │
│  Documentation Conf.    →   Documentation Engine             │
│                              - Service Contract review       │
│                              - Checklist verification        │
│                              - API doc coverage              │
│                              - Architecture doc check        │
└─────────────────────────────────────────────────────────────┘
```

### Engine Dispatch

Each rule in SABRE's YAML rule set has a `check_type` field that determines which engine handles it:

| check_type | Engine | Description |
|-----------|--------|-------------|
| `interface_signature` | Interface | Match API signatures against spec |
| `schema_validation` | Interface | Validate message types against XSD |
| `static_analysis` | Interface | General static code analysis |
| `state_machine` | Behavioral | Verify lifecycle state transitions |
| `behavioral_test` | Behavioral | Check runtime behavior patterns |
| `document_inspection` | Documentation | Verify document completeness |
| `checklist_validation` | Documentation | Validate compliance checklists |

### Specialized vs Generic Checkers

SABRE uses **specialized checkers** for well-known rule patterns and a **generic checker** as fallback:

- **Specialized**: Rules with recognized ID prefixes (e.g., `CAL-API-*`, `SVC-LIFECYCLE-*`, `DOC-SC-*`) are routed to purpose-built verification functions
- **Generic**: All other rules fall through to the generic checker, which extracts keywords from `pass_criteria` and matches against the MPU's code and documentation

---

## Assessment Output

Each engine produces structured results that feed into the scoring stage:

```yaml
assessment_results:
  interface:
    total: 32
    passed: 28
    failed: 4
    findings:
      - rule_id: OMS-CAL-001
        status: PASS
        evidence: "CAL API import at line 42"
      - rule_id: OMS-CAL-003
        status: FAIL
        evidence: "Direct ASB access at line 87"
  behavioral:
    total: 28
    passed: 25
    failed: 3
  documentation:
    total: 27
    passed: 24
    failed: 3
```

---

## Classification

All content is **UNCLASSIFIED**. Derived from publicly available SOSA process descriptions and SABRE tool architecture.
