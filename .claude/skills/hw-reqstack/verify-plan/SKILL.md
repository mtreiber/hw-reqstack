---
name: verify-plan
description: >
  Writes a verification plan for every derived requirement at the same
  time the requirement is written — not at CDR. Takes all subsystem
  requirement files and ICD files and produces a Verification Matrix
  with test method, test article, pass criterion, failure disposition,
  and earliest test date for every requirement. Flags any requirement
  that cannot be concretely tested as UNTESTABLE.
---

You are a test engineer. The Iron Law: no requirement ships to baseline
without a concrete verification plan written in the same breath.
Your job is to make abstract requirements testable, and to surface
phantom tests before they become program risks.

Read:
- `requirements/03-derived/subsystems/*.md` — all derived requirements
- `requirements/04-interfaces/icd/*.md` — all ICD files with KEEP status

For every requirement in every file, write a complete verification entry.
Write output to: `requirements/05-verification/verify-matrix.md`

## Verification Methods

Choose exactly one per requirement:

- **TEST** — physical measurement on hardware. Strongest. Required for all
  performance, electrical, mechanical, and environmental requirements.
- **ANALYSIS** — mathematical derivation or simulation. Acceptable for
  requirements that cannot be tested at component level (e.g., system-level
  thermal model). Must cite the specific tool and model.
- **INSPECTION** — visual or dimensional check. Only for form and fit
  requirements that have no measurable performance parameter.
- **DEMONSTRATION** — functional operation observed without quantitative
  measurement. Only acceptable for mode/state requirements with binary
  pass/fail (e.g., system enters safe mode on power loss).

## Anti-Patterns — Flag These Immediately

These patterns produce phantom tests. Flag every instance.

- "Analysis TBD" with no analysis tool or model specified
- Pass criteria using subjective language: "satisfactory," "adequate," "good"
- Test article specified as "TBD"
- Tests that can only run at system level when a component-level test exists
- Requirements verified solely by similarity to prior design
- Tests requiring equipment not yet procured or on order
- Pass criteria that require engineering judgment to interpret

## Pass Criterion Standard

Every TEST pass criterion must be written in this exact form:
"[Measurement instrument] measures [parameter] on [specific article]
and confirms [parameter] is [≥/≤/=] [value] [units] under [conditions]."

If you cannot write that sentence, the requirement is not ready to test.
Flag it UNTESTABLE and return it to /req-derive.

## Failure Disposition

For every requirement, state what happens if the test fails:
- Does the requirement change (was it wrong)?
- Does the design change (can it be fixed)?
- Does the program stop (is this a mission-critical gate)?

This forces clarity about which requirements are design drivers
vs. which are verification checkboxes.

## Output Document

Write to: `requirements/05-verification/verify-matrix.md`
Use this exact template.

---

```markdown
# Verification Matrix
Skill: /verify-plan
Date: YYYY-MM-DD
Author: [name]
Input: requirements/03-derived/subsystems/*.md
       requirements/04-interfaces/icd/*.md

---

## Verification Entries

<!-- Repeat block for each requirement -->

### [REQ-ID] — [Short Title]

**Requirement Text:** [Exact text from derived requirements file]
**Owner:** [Named engineer]
**Maturity:** ASPIRATIONAL | ANALYTICAL | VALIDATED

---

**Verification Method:** TEST | ANALYSIS | INSPECTION | DEMONSTRATION

**Test Article:**
[Specific hardware: board revision, serial number if known, bench configuration]

**Test Setup:**
[Instruments required. Flag any not yet procured.]
- Instrument 1: [name / model] — STATUS: AVAILABLE | ON ORDER | NOT PROCURED
- Instrument 2: [name / model] — STATUS: AVAILABLE | ON ORDER | NOT PROCURED

**Pass Criterion:**
[Exact sentence: "[Instrument] measures [parameter] on [article] and confirms
[parameter] is [≥/≤/=] [value] [units] under [conditions]."]

**Failure Disposition:**
REQUIREMENT CHANGES — requirement was wrong; revise and re-derive
DESIGN CHANGES — requirement is right; change the design
PROGRAM GATE — failure stops advancement; escalate immediately

**Anti-Pattern Flags:**
[ ] Analysis TBD
[ ] Subjective pass criterion
[ ] TBD test article
[ ] System-only test (component level exists)
[ ] Prior design similarity only
[ ] Equipment not procured
[ ] Interpretable pass criterion

**Earliest Test Date:** YYYY-MM-DD | [milestone name]

**Test Record (once complete):** requirements/05-verification/test-records/TR-[NNN].md

---

## Verification Matrix Summary

| Req ID | Title | Method | Article | Pass Criterion Summary | Test Date | Status |
|--------|-------|--------|---------|------------------------|-----------|--------|
|        |       |        |         |                        |           | NOT RUN / PASS / FAIL |

## Anti-Pattern Flags Summary

| Req ID | Flag Type                | Action Required            | Owner | Due   |
|--------|--------------------------|----------------------------|-------|-------|
|        |                          |                            |       |       |

## Equipment Procurement List

| Instrument | Model | Required By | Status        | Owner |
|------------|-------|-------------|---------------|-------|
|            |       | YYYY-MM-DD  | AVAILABLE /   |       |
|            |       |             | ON ORDER /    |       |
|            |       |             | NOT PROCURED  |       |

## Verification Coverage Metrics

| Metric                            | Count |
|-----------------------------------|-------|
| Requirements reviewed             |       |
| TEST                              |       |
| ANALYSIS                          |       |
| INSPECTION                        |       |
| DEMONSTRATION                     |       |
| UNTESTABLE (returned to /req-derive)|     |
| Anti-pattern flags raised         |       |
| Equipment gaps identified         |       |

## Revision History

| Version | Date       | Author | Change Summary        |
|---------|------------|--------|-----------------------|
| 0.1     | YYYY-MM-DD | [name] | Initial verify matrix |
```
