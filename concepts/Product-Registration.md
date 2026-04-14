# Product Registration

## SOSA Product Registration → SABRE Fleet Dashboard

> **Classification**: UNCLASSIFIED — Derived from publicly available SOSA and SABRE architecture documentation.

---

## Overview

In the SOSA framework, **product registration** is the process of recording verified products in a registry that tracks their conformance status over time. This enables procurement authorities to identify which products have been verified against which conformance profiles, and to track conformance trends.

SABRE implements this concept through its **fleet dashboard** — a web-based interface that provides fleet-wide visibility into conformance status across all verified MPUs and vendors.

---

## SOSA Product Registration Concept

SOSA's product registration process includes:

1. **Registration Entry**: Each verified product gets a registry entry with its conformance status
2. **Version Tracking**: Product versions are tracked, with re-verification required for new versions
3. **Profile Association**: Each entry records which conformance profile was verified against
4. **Status History**: Registration tracks conformance status changes over time
5. **Public Listing**: Conformant products can be listed in a public registry

### Registry Entry Components

| Component | Description |
|-----------|-------------|
| Product Identity | Name, vendor, version |
| Conformance Profile | Which standard and level verified against |
| Verification Date | When the last verification was performed |
| Status | Conformant / Non-Conformant / Pending |
| Score | Numerical conformance score |
| Verification Authority | Who performed the verification |

---

## SABRE Fleet Dashboard Implementation

SABRE implements product registration through a **Flask-based web dashboard** that serves as the conformance registry:

```
┌─────────────────────────────────────────────────────────────┐
│              SABRE FLEET DASHBOARD                            │
│                                                              │
│  ┌─────────────────────────────────────────────────────┐    │
│  │  Fleet Overview                                      │    │
│  │  ┌──────────┬──────────┬──────────┬──────────┐      │    │
│  │  │ Total    │ Conform. │ Non-Conf │ Pending  │      │    │
│  │  │ 12 MPUs  │ 8 (67%)  │ 3 (25%)  │ 1 (8%)   │      │    │
│  │  └──────────┴──────────┴──────────┴──────────┘      │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                              │
│  ┌─────────────────────────────────────────────────────┐    │
│  │  Per-Vendor View                                     │    │
│  │  Vendor A: ████████░░ 82% (Tier 3)                  │    │
│  │  Vendor B: █████████░ 91% (Tier 1)                  │    │
│  │  Vendor C: ███████░░░ 74% (Non-Conformant)          │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                              │
│  ┌─────────────────────────────────────────────────────┐    │
│  │  Per-MPU Drill-Down                                  │    │
│  │  nav_mpu          92%  Tier 1  ✓ CONFORMANT         │    │
│  │  comms_mpu        87%  Tier 2  ✓ CONFORMANT         │    │
│  │  sensor_fusion    85%  Tier 2  ✓ CONFORMANT         │    │
│  │  missing_cal_api  45%  —       ✗ NON-CONFORMANT     │    │
│  └─────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
```

### Dashboard Features Mapped to SOSA Registration

| SOSA Registration Element | SABRE Dashboard Feature |
|--------------------------|------------------------|
| Product registry | Fleet view with all MPUs |
| Version tracking | Per-MPU version and re-verification history |
| Profile association | Rule set and tier displayed per MPU |
| Status history | Trend tracking over time |
| Public listing | Dashboard accessible to authorized users |
| Multi-vendor view | Vendor comparison across fleet |

### Fleet Score Calculation

The fleet dashboard computes an **aggregate fleet score** across all registered MPUs:

- Fleet score = average of all individual MPU weighted scores
- Fleet conformance requires ALL individual MPUs to be conformant
- A single non-conformant MPU makes the entire fleet non-conformant
- This mirrors SOSA's principle that system-level conformance depends on component-level conformance

---

## Classification

All content is **UNCLASSIFIED**. Derived from publicly available SOSA process descriptions and SABRE tool architecture.
