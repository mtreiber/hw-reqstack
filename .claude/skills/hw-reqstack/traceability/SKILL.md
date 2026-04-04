---
name: traceability
description: >
  Builds and audits the live requirements traceability matrix. Reads all
  derived subsystem requirements, the verification matrix, and mission
  brief, then produces an RTM that flags orphaned requirements (no parent
  objective), dead ends (no verification), frozen requirements (stale
  maturity), phantom tests (equipment TBD or calibration expired), and
  duplicates. Produces the RTM and a flag report for action before baseline.
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

## Program Layer Note

**Single-subsystem programs** (one engineer owns everything, no shared resources):

  MISSION OBJECTIVE → SUBSYSTEM REQUIREMENT → VERIFICATION → TEST DATA

**Multi-subsystem programs** (two or more subsystems share power, timing, mass,
bandwidth, or any conserved budget, OR an external integration boundary exists):

  MISSION OBJECTIVE → SYS-NNN ALLOCATION → SUBSYSTEM REQUIREMENT → VERIFICATION → TEST DATA

If /req-sys was run, `requirements/02b-system/system-allocations.md` exists and
SYS-NNN IDs will appear as the Parent for subsystem requirements derived against
an allocation. Build the traceability tree accordingly.

If /req-sys was NOT run, proceed with the single-subsystem tree and note the decision:
"Single-subsystem program — SYS allocation layer omitted by design."
Without this note, an ORPHAN flag will fire for any subsystem requirement whose
parent is a SYS-NNN that was never written.

Build the complete traceability map as described above.
Write output to: `requirements/06-traceability/rtm.md`

## RTM Versioning

Use two-part version numbers:
- **Patch increment** [x.N]: flag changes only (no requirements added, deleted, or matured)
- **Minor increment** [N.x]: requirements added, deleted, matured, or ownership changed

Never regenerate the full RTM from scratch if only a few rows changed.
Append changed rows and update the version and Revision History.

## Large Program Guidance

For programs with more than 30 requirements:
- Produce one RTM file per subsystem: `requirements/06-traceability/rtm-[subsystem].md`
- Produce a summary roll-up in `requirements/06-traceability/rtm.md` that shows
  total counts, flag counts, and maturity distribution per subsystem.
- The /baseline skill reads the summary roll-up for gate checks.

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
instrument marked TBD, NOT PROCURED, or with a calibration expiry date
before the planned test date.
The test plan exists on paper but cannot be executed as written.
Action required before test campaign: procure equipment, renew calibration,
or accept risk with documented justification.

**⚠️ FROZEN**
A requirement still at ASPIRATIONAL or ANALYTICAL maturity for two or
more design cycles without progression.
Either the analysis needs to be done, or the test needs to run, or
the requirement is not real and should be deleted.
Action required: advance maturity or route back to /req-challenge.

**⚠️ DUPLICATE**
Two requirements that are functionally identical or that would be
satisfied by the same test. Resolve before the test campaign begins —
duplicate requirements either double-count test effort or hide a logic gap.
Action required: merge into one requirement; delete the other.
Track deleted requirement ID in OWNERS.md deleted log.

## Output Document

Write to: `requirements/06-traceability/rtm.md`

---

```markdown
# Requirements Traceability Matrix
Skill: /traceability
Date: YYYY-MM-DD
Version: [N.N]  (patch: flag-only changes; minor: reqs added/deleted/matured)
Author: [name]
Inputs:
  Mission Brief: requirements/00-mission/mission-brief.md
  Subsystem Reqs: requirements/03-derived/subsystems/*.md
  ICDs: requirements/04-interfaces/icd/*.md
  Verify Matrix: requirements/05-verification/verify-matrix.md

---

## Traceability Map

<!-- Full tree: Mission → Subsystem → Verification -->

### MO-[NNN] — [Mission Objective Title]

- [SUB]-[NNN] — [Subsystem Requirement] → [Verification Method] → [TR-NNN or NOT RUN]
- [SUB]-[NNN] — [Subsystem Requirement] → [Verification Method] → [TR-NNN or NOT RUN]

---

## Live RTM Table

| Req ID | Title | Parent | Owner | Maturity | Verify Method | Test Ref | Flags |
|--------|-------|--------|-------|----------|---------------|----------|-------|
|        |       |        |       |          |               |          |       |

Flag legend: 🔴 NO OWNER | 🔴 ORPHAN | 🔴 DEAD END | ⚠️ PHANTOM | ⚠️ FROZEN | ⚠️ DUPLICATE

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
| Req ID | Missing Resource         | Owner | Procurement / Calibration Due |
|--------|--------------------------|-------|-------------------------------|
|        | NOT PROCURED / EXPIRED CAL |     | YYYY-MM-DD                    |

#### FROZEN
| Req ID | Current Maturity | Cycles at This Level | Recommended Action     |
|--------|------------------|----------------------|------------------------|
|        |                  |                      | Run test / Run analysis|
|        |                  |                      | / Delete               |

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
| Ready for /baseline             |       |

## Maturity Distribution

| Maturity              | Count | % of Total |
|-----------------------|-------|------------|
| VALIDATED             |       |            |
| ANALYTICAL-SIMULATION |       |            |
| ANALYTICAL-MANUAL     |       |            |
| ASPIRATIONAL          |       |            |

## Revision History

| Version | Date       | Author | Change Summary              | Change Type   |
|---------|------------|--------|-----------------------------|---------------|
| 0.1     | YYYY-MM-DD | [name] | Initial RTM                 | Minor         |
```
