---
name: verify-plan
description: >
  Writes a verification plan for every derived requirement at the same
  time the requirement is written — not at CDR. Takes all subsystem
  requirement files and ICD files and produces a Verification Matrix
  with test method, test article, pass criterion, failure disposition,
  prerequisite tests, and earliest test date for every requirement.
  Flags any requirement that cannot be concretely tested as UNTESTABLE.
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

Test records for completed tests are written using the /test-record skill.
Once a test record is complete, update the test record path in this matrix.

## Verification Methods

Choose exactly one per requirement:

- **TEST** — physical measurement on hardware. Strongest. Required for all
  performance, electrical, mechanical, and environmental requirements.
- **ANALYSIS** — mathematical derivation or simulation. Acceptable for
  requirements that cannot be tested at component level (e.g., system-level
  thermal model). Must cite the specific tool and model.
- **INSPECTION** — visual or dimensional check. Only for form and fit
  requirements that have no measurable performance parameter.
- **DEMONSTRATION** — functional operation observed with a defined observable
  indicator. Only acceptable for mode/state requirements with binary pass/fail.
  The observable indicator must be explicitly named (e.g., "LED D3 illuminates
  and UART outputs 0x5A within 100ms of power loss"). "System enters safe mode"
  is not a DEMONSTRATION criterion — name the indicator.

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

## Test Sequencing

For each verification entry, identify any tests that must complete first.
Circular dependencies (Test A requires Test B which requires Test A)
must be resolved at /baseline before the test campaign begins.

## Equipment Calibration

All instruments listed in the test setup must have a valid calibration
expiry date entered in the Equipment Procurement List. An instrument
whose calibration expires before the planned test date is treated as
NOT PROCURED for planning purposes — flag it as a PHANTOM TEST risk.

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
**Maturity:** ASPIRATIONAL | ANALYTICAL-MANUAL | ANALYTICAL-SIMULATION | VALIDATED

---

**Verification Method:** TEST | ANALYSIS | INSPECTION | DEMONSTRATION

**Prerequisite Tests:** [TR-NNN that must PASS before this test runs, or NONE]

**Test Article:**
[Specific hardware: board revision, serial number if known, bench configuration]

**Test Setup:**
[Instruments required. Flag any not yet procured or with expired calibration.]
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

| Req ID | Title | Method | Prereqs | Article | Pass Criterion Summary | Test Date | Status |
|--------|-------|--------|---------|---------|------------------------|-----------|--------|
|        |       |        | TR-NNN / NONE |   |                        |           | NOT RUN / PASS / FAIL |

## Anti-Pattern Flags Summary

| Req ID | Flag Type                | Action Required            | Owner | Due   |
|--------|--------------------------|----------------------------|-------|-------|
|        |                          |                            |       |       |

## Equipment Procurement List

| Instrument | Model | Calibration Expiry | Required By | Status        | Owner |
|------------|-------|--------------------|-------------|---------------|-------|
|            |       | YYYY-MM-DD         | YYYY-MM-DD  | AVAILABLE /   |       |
|            |       |                    |             | ON ORDER /    |       |
|            |       |                    |             | NOT PROCURED  |       |

Note: If Calibration Expiry < Required By date, flag this row as PHANTOM TEST risk.

## Verification Coverage Metrics

| Metric                              | Count |
|-------------------------------------|-------|
| Requirements reviewed               |       |
| TEST                                |       |
| ANALYSIS                            |       |
| INSPECTION                          |       |
| DEMONSTRATION                       |       |
| UNTESTABLE (returned to /req-derive)|       |
| Anti-pattern flags raised           |       |
| Equipment gaps identified           |       |
| Calibration gaps identified         |       |
| Prerequisite dependency chains      |       |

## Revision History

| Version | Date       | Author | Change Summary        |
|---------|------------|--------|-----------------------|
| 0.1     | YYYY-MM-DD | [name] | Initial verify matrix |
```

## Re-run Protocol

When updating a single requirement's verification entry (returned from /baseline
or /req-change), update only that REQ-ID block in the matrix. Append the updated
block and update the Verification Matrix Summary row for that REQ-ID.
Increment the Revision History version.
