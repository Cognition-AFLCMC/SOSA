# Test Case Categories

## Interface, Behavioral, and Documentation Verification

> **Classification**: UNCLASSIFIED — Derived from publicly available SOSA and FACE CTS documentation.

---

## Overview

SOSA organizes verification checks into distinct categories based on what aspect of the system is being tested. This categorization drives the design of verification tools — each category requires different analysis techniques and produces different types of evidence.

SABRE adopts this categorization directly, mapping each category to a specialized verification engine.

---

## Category 1: Interface Checks

### What Is Verified

Interface checks verify that the system's **external interfaces** conform to the applicable standard. This includes API signatures, message formats, protocol compliance, and interface documentation.

### Source Standards

| Standard | Interface Requirements |
|----------|----------------------|
| OMS v2.5 §5.3 | CAL API — All messaging via Critical Abstraction Layer |
| OMS v2.5 §5.4 | Transport Services — Standard transport mechanisms |
| UCI v2.5 | Message schemas — All messages use UCI XSD types |
| AMS GRA | Interface specifications (CUI — not in this repo) |

### SABRE Implementation

| Check Type | Method | Example |
|-----------|--------|---------|
| `interface_signature` | AST parsing | Verify CAL API import statements |
| `schema_validation` | XSD matching | Validate message types against UCI schema |
| `static_analysis` | Pattern matching | Detect direct ASB access (prohibited) |

### Evidence Format

```
Finding: CAL API usage verification
File:    nav_mpu/src/navigation/cal_interface.py
Line:    42
Excerpt: from oms.cal import publish, subscribe
Result:  PASS — Messaging uses CAL API abstraction
```

---

## Category 2: Behavioral Checks

### What Is Verified

Behavioral checks verify that the system's **runtime behavior** conforms to the standard. This includes lifecycle state management, timing constraints, error handling, and operational patterns.

### Source Standards

| Standard | Behavioral Requirements |
|----------|------------------------|
| OMS v2.5 §6.1 | Service lifecycle states (DORMANT → STARTING → RUNNING → STOPPING → STOPPED) |
| OMS v2.5 §6.2 | QoS timing constraints |
| OMS v2.5 §6.3 | Error handling and graceful degradation |

### SABRE Implementation

| Check Type | Method | Example |
|-----------|--------|---------|
| `state_machine` | State analysis | Verify 5-state lifecycle implementation |
| `behavioral_test` | Pattern matching | Check error handling patterns |
| `static_analysis` | Flow analysis | Verify state transition ordering |

### Evidence Format

```
Finding: Lifecycle state machine verification
File:    nav_mpu/src/navigation/lifecycle.py
Line:    15-45
Excerpt: class NavigationLifecycle(ServiceLifecycle):
         states = [DORMANT, STARTING, RUNNING, STOPPING, STOPPED]
Result:  PASS — All 5 lifecycle states implemented
```

---

## Category 3: Documentation Checks

### What Is Verified

Documentation checks verify that the system includes **required documentation** that is complete and consistent with the implementation. This includes service contracts, compliance checklists, architecture documents, and interface control documents.

### Source Standards

| Standard | Documentation Requirements |
|----------|---------------------------|
| OMS OMSC-TMP-003 | Service Contract template (14 sections) |
| OMS OMSC-CHK-005 | Compliance checklist |
| OMS §7.1 | Architecture documentation |
| OMS §7.2 | Interface Control Document (ICD) |

### SABRE Implementation

| Check Type | Method | Example |
|-----------|--------|---------|
| `document_inspection` | File presence + content | Check Service Contract sections |
| `checklist_validation` | Section completeness | Verify checklist items populated |

### Evidence Format

```
Finding: Service Contract completeness
File:    nav_mpu/docs/service-contract.md
Sections: 14/14 required sections present
Missing:  None
Result:   PASS — Service Contract is complete
```

---

## Category Mapping from FACE CTS

SABRE's test categories were informed by the FACE CTS segment-based testing approach:

| FACE CTS Segment | SABRE Category | OMS/UCI Source |
|------------------|----------------|----------------|
| IOSS (I/O Services) | Interface Compliance | OMS §5.3 (CAL API) |
| PCS (Platform Computing) | Structural Compliance | OMS §4.1 (Service Model) |
| PSSS (Platform-Specific) | Behavioral Compliance | OMS §6.1 (Lifecycle) |
| TSS (Transport) | Documentation | OMSC-TMP-003 (Service Contract) |

---

## Classification

All content is **UNCLASSIFIED**. Derived from publicly available SOSA process descriptions, OMS documentation, and FACE CTS patterns.
