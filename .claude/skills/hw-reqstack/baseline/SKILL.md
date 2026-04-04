---
name: baseline
description: >
  Seven-gate release check. The final gate before a requirement set is
  frozen for hardware build. Reads the RTM and all supporting files and
  confirms every requirement passes all seven gates: owner, physics,
  margin, maturity, test, traceability, and interface. Produces a
  Baseline Package. Any requirement that fails a gate is returned to
  the appropriate upstream skill — never forced through.
---

You are the release gate. You do not negotiate. You do not grant exceptions.
A requirement either passes all seven gates or it does not enter baseline.
A requirement that fails a gate is returned upstream — to /req-challenge,
/req-derive, /verify-plan, or /traceability as appropriate.
Forcing a failing requirement into baseline is a program risk that will
surface during test, not a schedule solution.

Read:
- `requirements/06-traceability/rtm.md` — the live RTM with flags
- `requirements/03-derived/subsystems/*.md` — all subsystem requirements
- `requirements/05-verification/verify-matrix.md` — verification entries
- `requirements/04-interfaces/icd-audit.md` — ICD dispositions
- `requirements/OWNERS.md` — owner registry

A requirement is only considered for baseline if the RTM shows no
🔴 red flags. Resolve all red flags before running this skill.

## Partial Baseline Policy

Subsystems may be baselined independently if:
- No cross-subsystem ICD dependencies are unresolved for that subsystem
- All KEEP ICDs touching that subsystem have APPROVED status (both owners signed)
- The RTM for that subsystem has no 🔴 red flags

Cross-subsystem baseline (two or more subsystems released together) requires
simultaneous release of all affected subsystem packages. A subsystem whose
interface is REDUCE-pending cannot be independently baselined.

## The Seven Gates

A requirement must pass all seven. "Partial pass" is not a disposition.

**GATE 1 — OWNER**
Named individual assigned in requirements/OWNERS.md.
Not a team. Not a department. One person.
Fail condition: no entry in OWNERS.md, or entry lists a team.
Return to: /req-challenge (NEEDS OWNER disposition)

**GATE 2 — PHYSICS**
Governing equation or analysis cited in the subsystem requirement file.
The derivation section must reference a specific equation and computed value.
Additionally: confirm that the derivation result, adjusted by the stated margin,
matches the requirement value. If the arithmetic does not check out, Gate 2 fails.
Fail condition: "per customer spec" / "inherited from prior design" / blank, OR
derivation result + margin ≠ requirement value.
Return to: /req-derive

**GATE 3 — MARGIN**
Margin value stated with explicit named justification.
Acceptable: statistical spread, environmental variation, manufacturing Cpk, first-proto uncertainty.
Unacceptable: "per guidelines" / "to be safe" / blank.
Fail condition: no named margin justification or unacceptable justification type.
Return to: /req-derive

**GATE 4 — MATURITY**
Requirement must be ANALYTICAL-MANUAL, ANALYTICAL-SIMULATION, or VALIDATED.
ASPIRATIONAL requirements are not baselined — they enter the Design Goals list.
Fail condition: maturity = ASPIRATIONAL.
Action: move to Design Goals registry; do not return upstream.

**GATE 5 — TEST**
Verification entry exists in verify-matrix.md with:
  - Specific test article (not TBD)
  - Concrete pass criterion (no subjective language)
  - No unresolved anti-pattern flags
  - All prerequisite tests identified (no circular dependencies)
  - All instruments available with valid calibration (expiry after planned test date)
Fail condition: missing entry, TBD article, subjective criterion, open anti-pattern,
circular prerequisite chain, or calibration gap.
Return to: /verify-plan

**GATE 6 — TRACEABILITY**
Parent mission objective cited and confirmed in RTM.
No ORPHAN or DEAD END flags in RTM.
Fail condition: ORPHAN or DEAD END flag in RTM.
Return to: /traceability for investigation, then upstream for resolution.

**GATE 7 — INTERFACE**
If requirement involves a subsystem interface:
  - Corresponding ICD file exists in requirements/04-interfaces/icd/
  - ICD status is APPROVED (both side-A and side-B owners have signed)
  - ICD disposition is KEEP (not REDUCE or ELIMINATE pending action)
  - No unresolved latent assumptions flagged in ICD file
  - REDUCE Confirmation Tracking table in icd-audit.md shows confirmed date for all
    previously REDUCE-flagged interfaces affecting this requirement
Fail condition: open ICD action, ICD not APPROVED, ICD in REDUCE/ELIMINATE with
no resolution, or REDUCE confirmation missing.
Return to: /interface-audit

## Design Goals Registry

Requirements failing Gate 4 (ASPIRATIONAL) are tracked in:
`requirements/09-design-goals/design-goals.md`

Design Goals represent real intent that is not yet ready to commit.
They are reviewed at every /req-retro session. An owner must be assigned
and a target maturity date set for each Design Goal. A Design Goal without
a named owner and target date is a wish, not a tracked commitment.

## Integrity Statement and Version Control

On completion, commit the baseline package to the repository with:
- Git commit message: `baseline-vN.N: [sprint/milestone] [approved-count] reqs approved`
- Git tag: `baseline-vN.N`
- Include the commit hash in the Baseline Package Revision History table

The git tag is the integrity statement. Signed names alone are insufficient
in a version-controlled repository — the commit hash ties the document
to the exact state of the repository at the time of release.

## Output Document

Write to: `requirements/07-baseline/current/baseline-v[N.N].md`
Archive prior baseline to: `requirements/07-baseline/archive/v[prior]/`

---

```markdown
# Baseline Package v[N.N]
Skill: /baseline
Date: YYYY-MM-DD
Author: [name]
Sprint / Milestone: [e.g., Laser Driver Board Rev A Bring-up]
RTM Reference: requirements/06-traceability/rtm.md
Prior Baseline: requirements/07-baseline/archive/v[prior]/baseline-v[prior].md

---

## Gate Results

| Gate | Check                                               | Result       | Notes                              |
|------|-----------------------------------------------------|--------------|-------------------------------------|
| 1    | Every req has named individual owner                | PASS / FAIL  |                                    |
| 2    | Every req cites governing equation + math checks out| PASS / FAIL  |                                    |
| 3    | Every req states named margin justification         | PASS / FAIL  |                                    |
| 4    | No ASPIRATIONAL reqs in baseline                    | PASS / DEFER | ASPIRATIONAL → Design Goals list   |
| 5    | Every req has complete verification plan            | PASS / FAIL  |                                    |
| 6    | No orphaned or dead-end reqs in RTM                 | PASS / FAIL  |                                    |
| 7    | All interface requirements justified + ICDs APPROVED| PASS / FAIL  |                                    |

**Overall Baseline Status:** APPROVED | BLOCKED

Blocked reason (if applicable): [Gate N failed — [count] requirements returned to [skill]]

---

## Approved Requirements

| Req ID | Title | Owner | Maturity | Verify Method | Parent Obj |
|--------|-------|-------|----------|---------------|------------|
|        |       |       |          |               |            |

**Total approved:** [N]

---

## Design Goals (ASPIRATIONAL — tracked separately, not baselined)

These entries are written to requirements/09-design-goals/design-goals.md.

| Req ID | Title | Owner | Target Maturity By | Blocking? |
|--------|-------|-------|--------------------|-----------|
|        |       |       | YYYY-MM-DD         | YES / NO  |

**Total design goals:** [N]

---

## Returned Requirements (failed one or more gates)

| Req ID | Title | Failed Gate(s) | Returned To  | Action Required                |
|--------|-------|----------------|--------------|--------------------------------|
|        |       |                |              |                                |

**Total returned:** [N]

---

## Open Issues Log

| Issue ID | Description | Owner | Gate | Due Date | Status |
|----------|-------------|-------|------|----------|--------|
|          |             |       |      |          |        |

---

## Requirement Velocity (this sprint)

| Metric                              | Count |
|-------------------------------------|-------|
| Requirements submitted to baseline  |       |
| Approved (in baseline)              |       |
| Moved to Design Goals (ASPIRATIONAL)|       |
| Returned (failed gate)              |       |
| Deleted this cycle                  |       |
| Net change from prior baseline      |       |

**Running deletion total (all cycles):** [N] requirements deleted since program start.
A healthy program deletes requirements faster than it adds them.

---

## Baseline Integrity Statement

I confirm that every requirement in this baseline package has passed all
seven gates as documented above. Requirements that failed gates have been
returned upstream and are not included. Design goals are tracked separately
and do not constitute performance commitments for this build.

Author: [Name] — [Date]
Git tag: baseline-v[N.N]
Git commit: [hash — populated after commit]

---

## Revision History

| Version | Date       | Author | Change Summary                     | Git Commit |
|---------|------------|--------|------------------------------------|------------|
| [N.N]   | YYYY-MM-DD | [name] | [Summary of changes from prior BL] | [hash]     |
```
