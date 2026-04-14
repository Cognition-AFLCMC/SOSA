# Stage 2: PROFILE

## Profile the System Under Test

> **SOSA Stage**: 2 of 5
> **SABRE Component**: Rule Selector (Manifest Parser)
> **Classification**: UNCLASSIFIED

---

## Purpose

The PROFILE stage determines **which checks apply** to a specific target. Not all verification criteria apply to every system — the profiling stage filters the full rule set based on the target's characteristics.

## SOSA Framework

In the SOSA verification model, the PROFILE stage answers the question:

> "Which verification checks apply to THIS specific target?"

This involves:
1. Examining the target system's metadata (type, tier, version, capabilities)
2. Filtering the full verification criteria set to only applicable checks
3. Generating a verification profile — the specific list of checks to execute
4. Reporting the expected scope of verification to the operator

## SABRE Implementation

### Component: Rule Selector

SABRE implements the PROFILE stage through its **manifest parser and rule filtering** system. When an MPU is submitted for verification, SABRE reads its metadata and filters the loaded rule set accordingly.

### Profiling Inputs

| Input | Source | Used For |
|-------|--------|----------|
| MPU manifest | `manifest.yaml` in MPU package | Tier, type, version, capabilities |
| MPU metadata | Directory structure analysis | Language, file organization |
| Rule set | Selected YAML rules directory | Full set of available checks |
| Tier filter | CLI argument or manifest | Which tier thresholds to apply |

### Filtering Logic

```
Full Rule Set (e.g., 339 OMS checks)
    │
    ├── Filter by target_type (service/subsystem/platform)
    │   └── Remove rules not allocated to this level
    │
    ├── Filter by tier ([1, 2, 3])
    │   └── Remove rules not applicable to target's tier
    │
    ├── Filter by check_type availability
    │   └── Remove checks requiring unavailable analysis methods
    │
    └── Result: Filtered check list
        └── "87 checks will be executed for this MPU"
```

### Data Flow

```
┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│  MPU Package      │     │  Rule Selector    │     │  Verification    │
│  (manifest.yaml + │────▶│  (filter by tier, │────▶│  Profile         │
│   source code)    │     │   type, scope)    │     │  (N checks)      │
└──────────────────┘     └──────────────────┘     └──────────────────┘
```

### Example Profile Output

```
Verification Profile for: nav_mpu
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Target Type:     service
  Tier:            2 (Subsystem)
  Rule Set:        oms-v25
  Total Rules:     339
  Applicable:      87
  Breakdown:
    Interface:     32 checks
    Behavioral:    28 checks
    Documentation: 27 checks
  Duration:        < 1 second
```

### Key Design Decisions

1. **Metadata-Driven**: Profiling uses the MPU's own manifest rather than external configuration
2. **Transparent Filtering**: The operator sees exactly how many checks will run and why
3. **Fast Execution**: Profiling completes in under 1 second — no bottleneck
4. **Override Support**: CLI flags can override manifest values for testing scenarios

## SOSA Traceability

| SOSA Element | SABRE Implementation |
|-------------|---------------------|
| System Profiling | Manifest parser + metadata extraction |
| Criteria Filtering | Tier-based and type-based rule selection |
| Verification Scope | Filtered check list with count summary |
| Profile Artifacts | `manifest.yaml`, `mpu_metadata.json` |

---

## Classification

All content is **UNCLASSIFIED**. Derived from publicly available SOSA process descriptions and SABRE tool architecture.
