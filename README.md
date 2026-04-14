[![SOSA](https://img.shields.io/badge/SOSA-Sensor%20Open%20Systems%20Architecture-2f5597)]() [![The Open Group](https://img.shields.io/badge/Managed%20By-The%20Open%20Group-1e3a8a)]() [![Classification](https://img.shields.io/badge/Classification-UNCLASSIFIED-green)]() [![Content](https://img.shields.io/badge/Content-Process%20%26%20Reference-blue)]()

# SOSA — Sensor Open Systems Architecture

Reference documentation for the **Sensor Open Systems Architecture (SOSA)** verification framework — the process model that defines how conformance verification works for open architecture systems.

> [!NOTE]
> SOSA is managed by [The Open Group](https://www.opengroup.org/sosa). The full technical standard requires consortium membership, but the verification methodology and process framework are publicly documented. All content in this repository is **UNCLASSIFIED** and derived from publicly available sources only. No member-restricted or proprietary Open Group content is included.

## Overview

SOSA defines a **5-stage verification pipeline** for assessing whether system components conform to open architecture standards. Originally developed for hardware conformance verification, the same methodology applies directly to software conformance — which is exactly what Project SABRE implements.

```
┌─────────────────────────────────────────────────────────────┐
│              SOSA 5-STAGE VERIFICATION PIPELINE              │
│                                                              │
│   ┌──────────┐   ┌──────────┐   ┌──────────┐               │
│   │  DEFINE  │──▶│ PROFILE  │──▶│   TEST   │               │
│   │          │   │          │   │          │               │
│   │ What     │   │ Which    │   │ Execute  │               │
│   │ standards│   │ checks   │   │ checks   │               │
│   │ apply?   │   │ apply to │   │ against  │               │
│   │          │   │ this     │   │ target   │               │
│   │          │   │ target?  │   │ system   │               │
│   └──────────┘   └──────────┘   └──────────┘               │
│        │              │              │                       │
│        ▼              ▼              ▼                       │
│   ┌──────────┐   ┌──────────┐                               │
│   │  ASSESS  │──▶│ CERTIFY  │                               │
│   │          │   │          │                               │
│   │ Evaluate │   │ Issue    │                               │
│   │ results  │   │ formal   │                               │
│   │ against  │   │ conform- │                               │
│   │ criteria │   │ ance     │                               │
│   │          │   │ status   │                               │
│   └──────────┘   └──────────┘                               │
│                                                              │
│   ★ SABRE implements this pipeline for software ★           │
└─────────────────────────────────────────────────────────────┘
```

## Why SOSA?

SOSA provides the **"how"** — the verification process model that the SABRE conformance tool follows:

- **Proven Framework**: SOSA's verification pipeline is already accepted by DoD for hardware conformance. Applying it to software conformance is a natural extension
- **Credibility with Evaluators**: When asked "how did you design your verification process?", the answer is "we followed the SOSA framework" — instant credibility
- **Structured Process**: The 5-stage pipeline (Define → Profile → Test → Assess → Certify) gives the tool a rigorous, repeatable workflow
- **Industry Standard**: The Open Group manages SOSA, giving it broad industry acceptance beyond just DoD

## What's Included

| Directory | Contents |
|-----------|----------|
| [`reference/`](reference/) | SOSA Verification Framework, Conformance Levels, SOSA-to-SABRE Mapping |
| [`process/`](process/) | End-to-end verification workflow modeled on SOSA pipeline |
| [`five-stage-model/`](five-stage-model/) | Detailed breakdown of each SOSA verification stage with SABRE mappings |
| [`concepts/`](concepts/) | Conformance profiles, assessment process, test categories, product registration |
| [`sabre-integration/`](sabre-integration/) | SOSA-to-SABRE component map from the actual implementation |

## SOSA-to-SABRE Pipeline Mapping

| SOSA Stage | SABRE Component | Data Flow |
|-----------|----------------|-----------|
| **1. DEFINE** | Rule Generator | Standards (OMS/UCI/MOSA) → YAML Rules |
| **2. PROFILE** | Rule Selector | MPU Metadata → Filtered Check List |
| **3. TEST** | Verification Engine (3-engine pipeline) | MPU Code → Raw Results |
| **4. ASSESS** | Scoring Engine | Raw Results → Tiered Scores |
| **5. CERTIFY** | Report Generator | Scores → Reports/Dashboard |

## Relationship to Other Repos

```
Cognition-AFLCMC/
├── MOSA                       (policy framework — the "WHY")
├── SOSA                       ◄── THIS REPO (verification process — the "HOW")
├── FACE-CTS-User-Manual       (test patterns — the "HOW tests work")
├── AF-OMS                     (conformance rules source — the "WHAT to verify")
├── AF-UCI                     (message schemas — the "WHAT messages look like")
├── SABRE-Conformance-Application-  (the conformance tool itself)
└── Synthetic-MPU-s            (test fixtures — compliant & non-compliant MPUs)
```

## Where to Find More Information

- **The Open Group SOSA**: [opengroup.org/sosa](https://www.opengroup.org/sosa) — Official SOSA program page
- **SOSA Consortium**: [The Open Group SOSA Consortium](https://www.opengroup.org/sosa/consortium) — Consortium membership information
- **SOSA Reference Architecture**: Overview available publicly; full standard requires member access
- **FACE (Related)**: [opengroup.org/face](https://www.opengroup.org/face) — Related Open Group standard for airborne computing

## Classification

All materials in this repository are **UNCLASSIFIED** and derived from publicly available sources. No CUI, proprietary, or member-restricted Open Group content is included — only publicly available descriptions and our own analysis.
