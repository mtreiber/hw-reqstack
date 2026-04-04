---
name: test-record
description: >
  Writes a formal test record for a completed verification activity.
  A test record is the only evidence that advances a requirement from
  ANALYTICAL to VALIDATED maturity. Takes a verification matrix entry
  and physical test data and produces a TR-NNN document that is cited
  in the RTM. Without a completed test record, VALIDATED maturity
  cannot be claimed.
---

You are the engineer of record for a test. You are writing a permanent
document that another engineer must be able to reproduce from scratch
using only what you write here. If the test cannot be reproduced from
your record, the record is incomplete.

Read:
- `requirements/05-verification/verify-matrix.md` — the planned verification entry for this requirement
- The specific REQ-ID and TR-NNN assigned for this test

Write to: `requirements/05-verification/test-records/TR-[NNN].md`

The TR-NNN number must be globally unique across the program and must
match the reference in verify-matrix.md. Assign sequentially from
the highest existing TR number, or start at TR-001.

## Completing This Record

Fill every field. Mark truly unknown fields `N/A [reason]` — never leave
blank. A blank field means the test cannot be evaluated.

For PASS/FAIL determination: apply the pass criterion exactly as written
in verify-matrix.md. Do not interpret. Do not average marginal results.
If the measurement is within tolerance: PASS. If not: FAIL.
If the pass criterion is ambiguous at this point: STOP. Return the
requirement to /verify-plan to rewrite the criterion before running the test.

## Maturity Advancement

On completion of this record:
1. Update the requirement's maturity to VALIDATED in the subsystem file
2. Update the verify-matrix.md entry status to PASS or FAIL
3. Update the RTM maturity distribution table
4. If FAIL: open an item in the baseline Open Issues Log — do not delete
   the test record. A failed test is data; it is not a gap.

---

```markdown
# Test Record TR-[NNN]
Skill: /test-record
Date: YYYY-MM-DD
Engineer of Record: [Named individual — not a team]
Requirement: [REQ-ID] — [Short Title]
Verify-Matrix Entry: requirements/05-verification/verify-matrix.md § [REQ-ID]
Program / Board: [e.g., Laser Driver Board Rev A]

---

## 1. Planned vs. Actual

| Item                | Planned (from verify-matrix)       | Actual                              |
|---------------------|------------------------------------|-------------------------------------|
| Test article        | [planned article]                  | [actual article and S/N if known]   |
| Test method         | TEST / ANALYSIS / INSPECTION / DEMO| [same or note deviation]            |
| Pass criterion      | [exact text from verify-matrix]    | [confirm same — no rewrites here]   |
| Test date           | [planned date]                     | [actual date]                       |

**Deviations from plan:** [None, or list each deviation with justification]

---

## 2. Test Configuration

**Board / Article Revision:** [Rev A / Rev B / etc.]
**Serial Number / Identifier:** [if applicable]
**Firmware / Software Version:** [if applicable]
**Ambient Conditions:** Temperature: [°C]   Humidity: [%RH]   [Other if relevant]

### Instruments Used

| Instrument      | Model / ID     | Calibration Expiry | Status at Test Time     |
|-----------------|----------------|--------------------|-------------------------|
| [name]          | [model + S/N]  | YYYY-MM-DD         | VALID / EXPIRED / N/A   |
| [name]          | [model + S/N]  | YYYY-MM-DD         | VALID / EXPIRED / N/A   |

> WARNING: A test conducted with an expired-calibration instrument is
> invalid. Do not claim PASS on a requirement tested with expired equipment.
> Retest with calibrated instruments or document as INVALID with reason.

### Test Setup Description

[Describe the physical configuration: what is connected to what, signal
flow, power supply settings, probe placement, etc. Sufficient detail
to reproduce the setup from scratch.]

**Setup Photo / Schematic Reference:** [filename or path, or N/A]

---

## 3. Raw Data

**Data File:** [path to raw data file, e.g., test-records/data/TR-001-raw.csv, or NONE]

### Measurements

<!-- Repeat row for each measurement taken -->

| Measurement # | Parameter       | Instrument Used | Measured Value | Units | Pass Criterion    | Pass? |
|---------------|-----------------|-----------------|----------------|-------|-------------------|-------|
| 1             | [parameter]     | [instrument]    |                |       | [≥/≤/=] [value]   | Y / N |
| 2             |                 |                 |                |       |                   |       |

**Statistical summary (if multiple samples):**
- N = [sample count]
- Mean = [value] [units]
- Std Dev = [value] [units]
- Min = [value]   Max = [value]

---

## 4. Pass / Fail Determination

**Overall Result: PASS | FAIL | INVALID**

**Determination:**
[One sentence applying the planned pass criterion to the measured data.
Example: "Oscilloscope measures rise time of 4.2 ns on Rev A board S/N 001,
confirming rise time ≤ 5 ns under 25°C ambient. PASS."]

If FAIL:
**Failure Description:** [What was measured vs. what was required]
**Suspected Root Cause:** [Preliminary engineering judgment — not a formal FA]
**Immediate Action:** [REQUIREMENT CHANGES / DESIGN CHANGES / PROGRAM GATE]

If INVALID:
**Reason for Invalid:** [Expired calibration / deviation not acceptable / wrong article]
**Retest Required By:** [YYYY-MM-DD]

---

## 5. Maturity Advancement

| Action                               | Status      | Date       |
|--------------------------------------|-------------|------------|
| Requirement maturity updated to VALIDATED | DONE / PENDING | YYYY-MM-DD |
| verify-matrix.md status updated      | DONE / PENDING | YYYY-MM-DD |
| RTM maturity distribution updated    | DONE / PENDING | YYYY-MM-DD |
| Open issue logged (if FAIL)          | N/A / DONE  | YYYY-MM-DD |

---

## 6. Engineer Sign-off

I confirm that the data in this record accurately reflects what was
measured, that the pass criterion was applied as written in the
verification matrix without modification, and that deviations
from the test plan are documented above.

Signed: [Name] — [Date]
Git commit: [commit hash when this file is committed to the repository]

---

## Revision History

| Version | Date       | Author | Change Summary                    |
|---------|------------|--------|-----------------------------------|
| 1.0     | YYYY-MM-DD | [name] | Initial test record               |
```

## Re-run Protocol

This skill is not re-run — each test produces exactly one record.
If a test must be repeated (retest after a FAIL or INVALID), write a
new TR-NNN document. Reference the prior TR number in the Deviations
section. Do not overwrite or amend prior test records.
