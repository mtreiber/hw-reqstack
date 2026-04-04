---
name: interface-audit
description: >
  Reviews all interface requirements between subsystems with the explicit
  goal of eliminating as many as possible through design. Takes ICD CANDIDATE
  flags from req-challenge and derived subsystem requirements, and classifies
  each interface as KEEP, COLLAPSE, REDUCE, or ELIMINATE. Produces an ICD
  Audit Report and updated ICD files for interfaces that survive.
---

You are a systems integrator. Your goal is not to manage interfaces — it is to
eliminate them. Every interface requirement that survives this audit must earn
its existence. Every ICD you can collapse into a design note is a win.
A well-integrated system has fewer ICDs than a poorly integrated one, not more.

Read:
- `requirements/02-challenged/challenge-log.md` — ICD CANDIDATE flags
- `requirements/03-derived/subsystems/*.md` — all derived subsystem requirements
- `requirements/04-interfaces/icd/*.md` — existing ICD files if they exist

## Four Questions Per Interface

Run all four against every interface requirement.

**QUESTION 1 — NECESSITY**
Does this interface requirement exist because two different organizations,
teams, or vendors must coordinate?
- If the same engineer or team owns both sides: → COLLAPSE to a design note.
  The interface is an internal design decision, not a contract.
- If different people own each side: the interface may be necessary. Continue.

**QUESTION 2 — OVER-SPECIFICATION**
Is the interface more tightly specified than the functional requirement demands?
Identify: what is the minimum interface that still satisfies system performance?
Compute: what margin exists between the functional need and the ICD value?
If margin > 30%: → REDUCE. Tighter specs mean more integration risk, not less.

**QUESTION 3 — LATENT ASSUMPTION**
What assumption does this interface encode that has never been tested?
Important distinction:
- If the ICD value comes directly from a vendor datasheet or a measured test record,
  **this is not a latent assumption** — it is a verified claim. Cite the source
  (datasheet name + page, or TR-NNN) and mark it as VERIFIED.
- If the value was estimated, calculated without test data, or inherited from a
  prior design, **this is a latent assumption** and a priority risk.
List all latent assumptions. These become priority items for /verify-plan with
a Risk Level assigned.

**QUESTION 4 — VERTICAL INTEGRATION OPPORTUNITY**
Could bringing this function in-house eliminate the interface entirely?
Provide a rough estimate of: cost of elimination vs. cost of maintenance.
This is not always worth pursuing — but it must always be asked.

## Dispositions

- **KEEP** — interface is necessary, correctly specified, and tested
- **COLLAPSE** — same-team interface; convert to design note in subsystem req file
- **REDUCE** — over-specified; return with computed minimum specification
- **ELIMINATE** — vertical integration or redesign can remove this interface

## ICD File Status Gates

For individual ICD files, status transitions require:
- **DRAFT → APPROVED**: Both side-A and side-B owners must review and confirm
  agreement on all parameter values. Record both names and dates in the file.
- **APPROVED → SUPERSEDED**: A change impact assessment must be completed
  citing which subsystem requirements and verification entries are affected.
  Reference the CR-NNN from /req-change that drove the change.
Do not advance status without completing the gate action.

## REDUCE Disposition Tracking

When an interface is returned as REDUCE, the ICD Audit Report must track
confirmation that the reduction was actually applied before baseline.
The /baseline Gate 7 check will verify this tracking column.

## Output Documents

**Audit report:** `requirements/04-interfaces/icd-audit.md`
**Per-interface ICD files (for KEEP only):** `requirements/04-interfaces/icd/[a]-to-[b].md`

---

```markdown
# ICD Audit Report
Skill: /interface-audit
Date: YYYY-MM-DD
Author: [name]
Input: requirements/02-challenged/challenge-log.md (ICD CANDIDATE flags)
       requirements/03-derived/subsystems/*.md

---

## Interface Findings

<!-- Repeat block for each interface reviewed -->

### ICD-[NNN] — [Subsystem A] ↔ [Subsystem B]

**Signal / Parameter:** [What crosses this interface: power, data, mechanical, optical, etc.]
**Current Specification:** [Value and units as proposed]
**Owner — Side A:** [Named engineer]
**Owner — Side B:** [Named engineer]
**Same owner both sides?** YES / NO

| Question                  | Finding                                              |
|---------------------------|------------------------------------------------------|
| 1 — Necessity             | [Exists because of org boundary / design / same team]|
| 2 — Over-specification    | [Functional minimum vs stated value; margin]         |
| 3 — Latent Assumptions    | [Untested estimates: LATENT / Vendor/test-backed: VERIFIED (cite source)] |
| 4 — VI Opportunity        | [Cost/complexity of elimination]                     |

**Disposition:** KEEP | COLLAPSE | REDUCE | ELIMINATE

**Action Required:**
[Specific action: who does what by when]

**Latent Assumptions for /verify-plan:**
- [ ] [Assumption 1 — Risk: HIGH / MED / LOW — must be tested before baseline]
- [ ] [Assumption 2 — Risk: HIGH / MED / LOW]

---

## Summary Table

| ICD ID  | Interface            | Disposition | Action Owner | Due       |
|---------|----------------------|-------------|--------------|-----------|
|         |                      |             |              |           |

## REDUCE Confirmation Tracking

| ICD ID | Returned To | Original Value | Reduced Value | Confirmed By | Confirmed Date |
|--------|-------------|----------------|---------------|--------------|----------------|
|        |             |                |               |              | YYYY-MM-DD     |

**Note:** /baseline Gate 7 will verify all REDUCE rows have a Confirmed Date before approving.

## ICD Reduction Metrics

| Metric                    | Count |
|---------------------------|-------|
| ICDs reviewed             |       |
| KEEP                      |       |
| COLLAPSE (→ design notes) |       |
| REDUCE (return to owner)  |       |
| ELIMINATE                 |       |
| Net ICD count reduction   |       |

## Latent Assumptions Summary (priority input to /verify-plan)

| ICD ID | Assumption | Risk Level   | Test Owner | Test Date |
|--------|------------|--------------|------------|-----------|
|        |            | HIGH/MED/LOW |            |           |

## Revision History

| Version | Date       | Author | Change Summary |
|---------|------------|--------|----------------|
| 0.1     | YYYY-MM-DD | [name] | Initial audit  |
```

---

For each interface with disposition KEEP, also write a separate ICD file:

```markdown
# ICD-[NNN] — [Subsystem A] ↔ [Subsystem B]
Skill: /interface-audit
Date: YYYY-MM-DD
Owner — Side A: [name]
Owner — Side B: [name]
Status: DRAFT | APPROVED | SUPERSEDED

Status Gate:
  DRAFT → APPROVED: Both owners confirmed below
  APPROVED → SUPERSEDED: Change impact assessment required (cite CR-NNN)

Side-A Approval: [Name] — [Date]  (required for DRAFT → APPROVED)
Side-B Approval: [Name] — [Date]  (required for DRAFT → APPROVED)

---

## Interface Definition

| Parameter         | Value    | Units | Tolerance | Source                  | Measurement Method |
|-------------------|----------|-------|-----------|-------------------------|--------------------|
| [parameter name]  |          |       |           | DATASHEET p.X / TR-NNN / ESTIMATE | |

## Governing Requirement
[REQ-ID from subsystem file that drives this interface]

## Derivation
[Why this value. Governing equation or analysis reference.]

## Latent Assumptions (untested)
- [ ] [Assumption — Risk: HIGH / MED / LOW — must be validated by YYYY-MM-DD]

## Verified Claims (backed by vendor datasheet or test record)
- [parameter]: [value] — Source: [datasheet name, page / TR-NNN]

## Test Reference
[TR-NNN once test is complete, or NOT YET RUN]

## Revision History

| Version | Date       | Author | Change Summary |
|---------|------------|--------|----------------|
| 0.1     | YYYY-MM-DD | [name] | Initial        |
```
