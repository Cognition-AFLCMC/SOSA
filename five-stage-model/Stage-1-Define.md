# Stage 1: DEFINE

## Define Verification Criteria

> **SOSA Stage**: 1 of 5
> **SABRE Component**: Rule Generator (YAML Loader)
> **Classification**: UNCLASSIFIED

---

## Purpose

The DEFINE stage establishes **what standards apply** to the system under test. Before any verification can occur, the verification authority must identify the applicable standards, extract testable requirements, and encode them as verification criteria.

## SOSA Framework

In the SOSA verification model, the DEFINE stage answers the question:

> "What standards and requirements must this system conform to?"

This involves:
1. Identifying the applicable reference architecture or technical standard
2. Extracting testable requirements from the standard
3. Defining pass/fail criteria for each requirement
4. Organizing requirements into categories and severity levels
5. Publishing the verification criteria in a machine-readable format

## SABRE Implementation

### Component: Rule Generator

SABRE implements the DEFINE stage through its **YAML rule system**. Standards are encoded as declarative YAML rule files that the verification engine consumes.

```
Standards Source                    SABRE Rule Output
──────────────────                  ─────────────────
OMS v2.5 Excel Checklists    →    rules/oms-v25/*.yaml (339 checks)
UCI v2.5 XSD Schema           →    rules/uci-v25/*.yaml (message types)
MOSA Principles (10 USC 4401) →    rules/mosa/*.yaml (67 checks)
AMS GRA v14.0+ (future)       →    rules/ams-gra-14/*.yaml
```

### Data Flow

```
┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  Published        │     │  Rule Authoring   │     │  YAML Rule Files │
│  Standards        │────▶│  Process          │────▶│  (Version-       │
│  (OMS, UCI, MOSA) │     │                   │     │   Controlled)    │
└──────────────────┘     └──────────────────┘     └──────────────────┘
```

### Rule Structure

Each rule in the YAML files contains:

| Field | Purpose | Example |
|-------|---------|---------|
| `id` | Unique identifier | `OMS-CAL-001` |
| `description` | Human-readable requirement | "All messaging must use CAL API" |
| `standard_reference` | Traceability to source | "OMS v2.5 §5.3" |
| `check_type` | Verification method | `ast_pattern`, `state_machine`, `document_inspection` |
| `pass_criteria` | Testable conditions | List of conditions that must be true |
| `severity` | Impact level | `critical`, `major`, `minor` |
| `tier` | Applicability | `[1, 2, 3]` |

### Key Design Decisions

1. **Declarative over Imperative**: Rules describe WHAT to check, not HOW to check it. The engine handles the how.
2. **Version-Controlled**: Rules are YAML files in Git, enabling diff, review, and history tracking.
3. **Swappable**: Different rule sets can be loaded at runtime (`--rules oms-v25` vs `--rules ams-gra-14`).
4. **Extensible**: New rules can be added without modifying the engine code.

## SOSA Traceability

| SOSA Element | SABRE Implementation |
|-------------|---------------------|
| Verification Criteria | YAML rule files |
| Standards Reference | `standard_reference` field in each rule |
| Requirement Categorization | `check_type` and severity fields |
| Criteria Publication | Rules published in Git repository |

---

## Classification

All content is **UNCLASSIFIED**. Derived from publicly available SOSA process descriptions and SABRE tool architecture.
