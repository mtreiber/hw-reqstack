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
Fail condition: "per customer spec" / "inherited from prior design" / blank.
Return to: /req-derive

**GATE 3 — MARGIN**
Margin value stated with explicit justification.
Acceptable: statistical spread, environmental variation, manufacturing Cpk, first-proto uncertainty.
Unacceptable: "per guidelines" / "to be safe" / blank.
Fail condition: no margin justification or unacceptable justification type.
Return to: /req-derive

**GATE 4 — MATURITY**
Requirement must be ANALYTICAL or VALIDATED.
ASPIRATIONAL requirements are not baselined — they enter the Design Goals list.
Fail condition: maturity = ASPIRATIONAL.
Action: move to Design Goals; do not return upstream.

**GATE 5 — TEST**
Verification entry exists in verify-matrix.md with:
  - Specific test article (not TBD)
  - Concrete pass criterion (no subjective language)
  - No unresolved anti-pattern flags
Fail condition: missing entry, TBD article, subjective criterion, or open anti-pattern.
Return to: /verify-plan

**GATE 6 — TRACEABILITY**
Parent mission objective cited and confirmed in RTM.
No ORPHAN or DEAD END flags in RTM.
Fail condition: ORPHAN or DEAD END flag in RTM.
Return to: /traceability for investigation, then upstream for resolution.

**GATE 7 — INTERFACE**
If requirement involves a subsystem interface:
  - Corresponding ICD file exists in requirements/04-interfaces/icd/
  - ICD disposition is KEEP (not REDUCE or ELIMINATE pending action)
  - No unresolved latent assumptions flagged in ICD file
Fail condition: open ICD action, or ICD in REDUCE/ELIMINATE with no resolution.
Return to: /interface-audit

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

| Gate | Check                                         | Result       | Notes                              |
|------|-----------------------------------------------|--------------|------------------------------------|
| 1    | Every req has named individual owner          | PASS / FAIL  |                                    |
| 2    | Every req cites governing equation            | PASS / FAIL  |                                    |
| 3    | Every req states explicit margin justification| PASS / FAIL  |                                    |
| 4    | No ASPIRATIONAL reqs in baseline              | PASS / DEFER | ASPIRATIONAL → Design Goals list   |
| 5    | Every req has complete verification plan      | PASS / FAIL  |                                    |
| 6    | No orphaned or dead-end reqs in RTM           | PASS / FAIL  |                                    |
| 7    | All interface requirements justified          | PASS / FAIL  |                                    |

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

Signed: [Name] — [Date]

---

## Revision History

| Version | Date       | Author | Change Summary                     |
|---------|------------|--------|------------------------------------|
| [N.N]   | YYYY-MM-DD | [name] | [Summary of changes from prior BL] |
```
