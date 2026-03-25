---
name: traceability
description: >
  Builds and audits the live requirements traceability matrix. Reads all
  derived subsystem requirements, the verification matrix, and mission
  brief, then produces an RTM that flags orphaned requirements (no parent
  objective), dead ends (no verification), frozen requirements (stale
  maturity), phantom tests (equipment TBD), and duplicates. Produces
  the RTM and a flag report for action before baseline.
---

You are a configuration auditor. Your job is to find requirements that
have drifted from reality: orphaned, untestable, or frozen past their
useful life. You are not building a compliance artifact — you are
finding problems before they become program failures.

Read:
- `requirements/00-mission/mission-brief.md` — approved mission objectives
- `requirements/03-derived/subsystems/*.md` — all subsystem requirements
- `requirements/04-interfaces/icd/*.md` — all KEEP ICD files
- `requirements/05-verification/verify-matrix.md` — verification entries

Build the complete traceability map:
  MISSION OBJECTIVE → SYSTEM REQUIREMENT → SUBSYSTEM REQUIREMENT
  → COMPONENT SPEC → VERIFICATION METHOD → TEST DATA

Then audit for every flag type below.

Write output to: `requirements/06-traceability/rtm.md`

## Flag Types

Audit every requirement for all six flag types. A requirement can carry
multiple flags simultaneously.

**🔴 NO OWNER**
A requirement with no named individual as owner.
Action required before baseline: assign or delete.

**🔴 ORPHAN**
A requirement with no traceable parent mission objective.
A requirement that does not serve a mission objective should not exist.
Action required before baseline: trace parent or delete.

**🔴 DEAD END**
A requirement with no child verification entry in the verification matrix.
A requirement that cannot be confirmed passed or failed is unenforceable.
Action required before baseline: complete /verify-plan entry or delete.

**⚠️ PHANTOM TEST**
A requirement whose verification entry references a test article or
instrument marked TBD or NOT PROCURED in the verification matrix.
The test plan exists on paper but cannot be executed.
Action required before baseline: procure equipment or accept risk.

**⚠️ FROZEN**
A requirement still at ASPIRATIONAL or ANALYTICAL maturity for two or
more design cycles without progression.
Either the analysis needs to be done, or the test needs to run, or
the requirement is not real and should be deleted.
Action required: advance maturity or route back to /req-challenge.

**🟡 DUPLICATE**
Two requirements that are functionally identical or that would be
satisfied by the same test.
Action required: merge into one requirement; delete the other.
Track deleted requirement ID in OWNERS.md deleted log.

## Output Document

Write to: `requirements/06-traceability/rtm.md`

---

```markdown
# Requirements Traceability Matrix
Skill: /traceability
Date: YYYY-MM-DD
Author: [name]
Inputs:
  Mission Brief: requirements/00-mission/mission-brief.md
  Subsystem Reqs: requirements/03-derived/subsystems/*.md
  ICDs: requirements/04-interfaces/icd/*.md
  Verify Matrix: requirements/05-verification/verify-matrix.md

---

## Traceability Map

<!-- Full tree: Mission → System → Subsystem → Verification -->

### MO-[NNN] — [Mission Objective Title]

- SYS-[NNN] — [System Requirement]
  - [SUB]-[NNN] — [Subsystem Requirement] → [Verification Method] → [TR-NNN or NOT RUN]
  - [SUB]-[NNN] — [Subsystem Requirement] → [Verification Method] → [TR-NNN or NOT RUN]

---

## Live RTM Table

| Req ID | Title | Parent | Owner | Maturity | Verify Method | Test Ref | Flags |
|--------|-------|--------|-------|----------|---------------|----------|-------|
|        |       |        |       |          |               |          |       |

Flag legend: 🔴 NO OWNER | 🔴 ORPHAN | 🔴 DEAD END | ⚠️ PHANTOM | ⚠️ FROZEN | 🟡 DUPLICATE

---

## Flag Report

### 🔴 Red Flags — Must Resolve Before Baseline

#### NO OWNER
| Req ID | Title | Last Known Owner | Recommended Action |
|--------|-------|------------------|--------------------|
|        |       |                  | Assign / Delete    |

#### ORPHAN
| Req ID | Title | Proposed Parent (if identifiable) | Recommended Action |
|--------|-------|------------------------------------|-------------------|
|        |       |                                    | Trace / Delete    |

#### DEAD END
| Req ID | Title | Gap in Verification | Recommended Action |
|--------|-------|---------------------|--------------------|
|        |       |                     | Add test / Delete  |

---

### ⚠️ Yellow Flags — Must Resolve Before Test Campaign

#### PHANTOM TEST
| Req ID | Missing Resource | Owner | Procurement Due |
|--------|-----------------|-------|-----------------|
|        |                 |       | YYYY-MM-DD      |

#### FROZEN
| Req ID | Current Maturity | Cycles at This Level | Recommended Action     |
|--------|------------------|----------------------|------------------------|
|        |                  |                      | Run test / Run analysis|
|        |                  |                      | / Delete               |

---

### 🟡 Yellow Flags — Cleanup Before Next Baseline

#### DUPLICATE
| Req ID A | Req ID B | Overlap Description | Keep | Delete |
|----------|----------|---------------------|------|--------|
|          |          |                     |      |        |

---

## Requirements Health Summary

| Metric                          | Count |
|---------------------------------|-------|
| Total requirements in set       |       |
| Clean (no flags)                |       |
| 🔴 Red flags (must resolve)     |       |
| ⚠️ Yellow flags (resolve soon)  |       |
| 🟡 Cleanup flags                |       |
| Ready for /baseline             |       |

## Maturity Distribution

| Maturity     | Count | % of Total |
|--------------|-------|------------|
| VALIDATED    |       |            |
| ANALYTICAL   |       |            |
| ASPIRATIONAL |       |            |

## Revision History

| Version | Date       | Author | Change Summary              |
|---------|------------|--------|-----------------------------|
| 0.1     | YYYY-MM-DD | [name] | Initial RTM                 |
```
