# Stage 3: TEST

## Execute Verification Checks

> **SOSA Stage**: 3 of 5
> **SABRE Component**: Verification Engine (3-Engine Pipeline)
> **Classification**: UNCLASSIFIED

---

## Purpose

The TEST stage **executes verification checks** against the target system. This is the core of the verification process — where the actual analysis happens and raw results are collected.

## SOSA Framework

In the SOSA verification model, the TEST stage answers the question:

> "Does this system meet the defined verification criteria?"

This involves:
1. Executing each applicable check from the verification profile
2. Collecting pass/fail results with supporting evidence
3. Recording code-level evidence (file paths, line numbers, code excerpts)
4. Handling edge cases (unsupported languages, missing files, timeouts)

## SABRE Implementation

### Component: Verification Engine (3-Engine Pipeline)

SABRE implements the TEST stage through a **three-engine verification pipeline**, each engine specialized for a different category of checks:

```
┌─────────────────────────────────────────────────────────────┐
│              SABRE 3-ENGINE VERIFICATION PIPELINE             │
│                                                              │
│   ┌──────────────────┐                                      │
│   │ Interface Engine  │  Checks:                             │
│   │                   │  - CAL API usage verification        │
│   │                   │  - UCI message schema validation     │
│   │                   │  - Transport service compliance      │
│   │                   │  - Interface signature matching      │
│   └────────┬─────────┘                                      │
│            │                                                 │
│   ┌──────────────────┐                                      │
│   │ Behavioral Engine │  Checks:                             │
│   │                   │  - Lifecycle state machine (5 states)│
│   │                   │  - Timing constraint verification    │
│   │                   │  - Error handling patterns           │
│   │                   │  - Dependency management             │
│   └────────┬─────────┘                                      │
│            │                                                 │
│   ┌──────────────────┐                                      │
│   │ Documentation     │  Checks:                             │
│   │ Engine            │  - Service Contract completeness     │
│   │                   │  - Compliance checklist verification │
│   │                   │  - API documentation coverage        │
│   │                   │  - Architecture documentation        │
│   └──────────────────┘                                      │
│                                                              │
│   All three engines run against the same MPU and produce     │
│   unified results with code-level evidence.                  │
└─────────────────────────────────────────────────────────────┘
```

### Analysis Methods

| Method | Engine | Description |
|--------|--------|-------------|
| `ast_pattern` | Interface | Parse source code into AST, match patterns |
| `schema_validation` | Interface | Validate message types against UCI XSD |
| `interface_signature` | Interface | Check API surface against spec |
| `state_machine` | Behavioral | Verify lifecycle state implementation |
| `behavioral_test` | Behavioral | Check runtime behavior patterns |
| `static_analysis` | All | General static code analysis |
| `document_inspection` | Documentation | Verify document completeness |
| `checklist_validation` | Documentation | Validate compliance checklists |

### Evidence Collection

Every check produces structured evidence:

```yaml
result:
  rule_id: OMS-CAL-001
  status: PASS
  evidence:
    file: "nav_mpu/src/navigation/cal_interface.py"
    line: 42
    excerpt: "from oms.cal import publish, subscribe"
    finding: "CAL API import found — messaging uses standard abstraction layer"
  standard_ref: "OMS v2.5 §5.3"
```

### Execution Characteristics

| Metric | Value |
|--------|-------|
| Static analysis duration | 30-60 seconds per MPU |
| With Devin deep scan | 2-5 minutes per MPU |
| Parallelization | All three engines can run concurrently |
| Determinism | Same input → same output, every time |

## SOSA Traceability

| SOSA Element | SABRE Implementation |
|-------------|---------------------|
| Test Execution | 3-engine verification pipeline |
| Test Methods | AST parsing, schema validation, document inspection |
| Evidence Collection | File path, line number, code excerpt per check |
| Test Artifacts | `engine_results.json` |

---

## Classification

All content is **UNCLASSIFIED**. Derived from publicly available SOSA process descriptions and SABRE tool architecture.
