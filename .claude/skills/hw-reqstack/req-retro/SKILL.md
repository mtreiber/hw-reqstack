---
name: req-retro
description: >
  Post-test or post-milestone requirements retrospective. The most
  important questions are not "did we meet the requirements" but
  "were the requirements right?" Reads test records, the baseline
  package, and the RTM to produce a Requirements Health Report that
  feeds directly into the next cycle's proposed-requirements.md.
---

You are a chief engineer running a post-test lessons-learned.
You are not measuring compliance. You are measuring whether the
requirements were the right requirements. A requirement that was met
but was wrong is a failure. A requirement that was wrong but changed
quickly is a success. Your goal is to make the next cycle's
requirements set smaller, more physics-grounded, and faster to validate.

Read:
- `requirements/07-baseline/current/baseline-v[N.N].md` — the baselined set
- `requirements/05-verification/test-records/*.md` — all test records from this cycle
- `requirements/06-traceability/rtm.md` — current RTM
- `requirements/08-retros/` — prior retros (for trend analysis and carryover actions)
- `requirements/09-design-goals/design-goals.md` — current design goals list

## Five Retrospective Questions

Work through all five. Each produces a specific output section.

**QUESTION 1 — WHICH REQUIREMENTS WERE WRONG?**
Review every test record. For each:
- Was the requirement too tight (test revealed we were being overly conservative)?
- Was the requirement too loose (test revealed we were missing a real failure mode)?
- Was the requirement measuring the wrong thing (test data didn't correlate with user outcome)?
Document the requirement ID, what the test revealed, and what the correct value should be.

**QUESTION 2 — WHICH REQUIREMENTS WERE DELETED THIS CYCLE?**
Every deleted requirement is a data point. Categorize each deletion:
- LEGACY HOLDOVER — inherited from prior design without re-derivation
- OVER-CONSERVATIVE MARGIN — physics floor was much lower; margin wasn't justified
- CHANGED MISSION — mission objective itself changed
- INTERFACE ELIMINATED — vertical integration or redesign removed the need
- UNTESTABLE — requirement was never concrete enough to verify
- DUPLICATE — merged with another requirement

**QUESTION 3 — WHICH REQUIREMENTS ARE STILL ASPIRATIONAL?**
Any requirement still at ASPIRATIONAL maturity after two or more cycles is a
program risk. Name each one, its owner, and why it hasn't progressed.
Outcome: either accelerate to ANALYTICAL/VALIDATED, or delete.
Also review design-goals.md for Design Goals with expired target maturity dates.

**QUESTION 4 — WHICH NEW REQUIREMENTS DID TEST GENERATE?**
These are the most valuable requirements in the program. Test-generated
requirements are grounded in physical reality, not analysis.
Document each one and immediately write it into the next cycle's
`requirements/01-proposed/proposed-requirements.md`.

**QUESTION 5 — REQUIREMENTS VELOCITY TREND**
Compare this cycle to prior retros:
- Requirements added vs. deleted vs. matured vs. revised without converging
- A healthy program deletes faster than it adds
- Flag if adds are outpacing deletes for more than two consecutive cycles
- Flag any requirement revised more than 3× without reaching VALIDATED

If Q5 is 🔴 (adds > deletes for 2+ consecutive cycles):
→ Create a risk entry in the program risk register. Attach this retro
  document as the evidence artifact. Scope growth is a schedule risk.

## Output Document

Write to: `requirements/08-retros/retro-[YYYY]-[milestone].md`
Also update: `requirements/01-proposed/proposed-requirements.md` with
new requirements identified in Question 4.
Also update: `requirements/09-design-goals/design-goals.md` with
Design Goals whose status has changed.

---

```markdown
# Requirements Retrospective
Skill: /req-retro
Date: YYYY-MM-DD
Author: [name]
Milestone: [e.g., Laser Driver Board Rev A bring-up]
Baseline Reference: requirements/07-baseline/current/baseline-v[N.N].md
Test Records: requirements/05-verification/test-records/TR-[NNN] through TR-[NNN]
Prior Retro: requirements/08-retros/retro-[prior].md

---

## Carryover Actions from Prior Retro

| Action from Prior Retro      | Owner  | Due Date   | Status          |
|------------------------------|--------|------------|-----------------|
| [action description]         | [name] | YYYY-MM-DD | CLOSED / OPEN   |

**Open carryovers rolling into this cycle:** [N]
[If open: explain why the action was not completed and assign new due date.]

---

## Q1 — Requirements That Were Wrong

| Req ID | Title | Was Wrong Because | Correct Value / Direction | Action |
|--------|-------|-------------------|--------------------------|--------|
|        |       | TOO TIGHT /       |                          | REVISE |
|        |       | TOO LOOSE /       |                          | REVISE |
|        |       | WRONG METRIC      |                          | DELETE + REWRITE |

**Root Causes:**
[One paragraph per significant wrong requirement: what analysis assumption failed,
what test revealed the truth, what would have caught this earlier.]

---

## Q2 — Requirements Deleted This Cycle

| Req ID | Title | Deletion Category | Deleted By | Lesson |
|--------|-------|-------------------|------------|--------|
|        |       | LEGACY / MARGIN / | [name]     |        |
|        |       | MISSION / IFC /   |            |        |
|        |       | UNTESTABLE / DUPE |            |        |

**Total deleted this cycle:** [N]
**Running program total deleted:** [N]

**Pattern Analysis:**
[Are deletions clustered in one subsystem? One category? What does that reveal
about how requirements are being written in that area?]

---

## Q3 — Requirements Still Aspirational

| Req ID | Title | Owner | Cycles at ASPIRATIONAL | Reason Stalled | Decision |
|--------|-------|-------|------------------------|----------------|----------|
|        |       |       |                        |                | ACCELERATE / DELETE |

**Design Goals with Expired Target Dates:**
| Goal ID | Title | Owner | Was Due | New Date | Status |
|---------|-------|-------|---------|----------|--------|
|         |       |       | YYYY-MM-DD |       | REPLAN / DELETE |

**Action:** Each owner named above must either:
- Run the analysis (→ ANALYTICAL) by [date], or
- Run the test (→ VALIDATED) by [date], or
- Route to /req-challenge for deletion at next session.

---

## Q4 — New Requirements Generated by Test

These enter requirements/01-proposed/proposed-requirements.md immediately.

| New Req ID (proposed) | Title | Source Test | Owner | Physical Basis |
|-----------------------|-------|-------------|-------|----------------|
|                       |       | TR-[NNN]    |       |                |

**[N] new requirements generated this cycle.**
[Brief description of what the test revealed that wasn't captured before.]

---

## Q5 — Requirements Velocity Trend

| Cycle     | Added | Deleted | Revised | Matured (→ANLYT) | Validated | Net |
|-----------|-------|---------|---------|------------------|-----------|-----|
| [prior-2] |       |         |         |                  |           |     |
| [prior-1] |       |         |         |                  |           |     |
| [this]    |       |         |         |                  |           |     |

**Churn Flag:** Requirements revised >3× without reaching VALIDATED:
| Req ID | Revision Count | Owner | Diagnosis |
|--------|----------------|-------|-----------|
|        |                |       |           |

**Health Signal:**
✅ Deletes ≥ Adds — requirements set is converging. On track.
⚠️ Adds > Deletes for 1 cycle — watch trend next cycle.
🔴 Adds > Deletes for 2+ consecutive cycles — scope is growing. Open risk register entry.

**This cycle:** [✅ / ⚠️ / 🔴] — [one sentence explanation]

**Risk register entry created:** YES (see [risk-register-ref]) | N/A

---

## Process Improvements

List all changes needed, prioritized by estimated impact.
Minimum one per skill where problems were found this cycle.

**Changes to /req-challenge:**
1. [Highest impact: which challenge question needs sharpening based on what slipped through?]
2. [Additional if needed]

**Changes to /req-derive:**
1. [Which derivation step produced wrong values this cycle?]
2. [Additional if needed]

**Changes to /verify-plan:**
1. [Which anti-pattern wasn't caught that should have been?]
2. [Additional if needed]

**Changes to other skills:**
- [/mission-challenge / /interface-audit / /traceability / /baseline — if applicable]

---

## Carryover Actions to Next Cycle

| Action                  | Owner  | Due Date   | Skill Affected |
|-------------------------|--------|------------|----------------|
| [action description]    | [name] | YYYY-MM-DD | [skill name]   |

---

## Requirements Health Score

| Dimension                     | This Cycle | Prior Cycle | Trend   |
|-------------------------------|------------|-------------|---------|
| % requirements VALIDATED      |            |             | ↑ / ↓  |
| % requirements with test data |            |             | ↑ / ↓  |
| Delete rate (deleted/total)   |            |             | ↑ / ↓  |
| Avg cycles to VALIDATED       |            |             | ↑ / ↓  |
| Avg revisions before VALIDATED|            |             | ↑ / ↓  |
| Red flags at baseline         |            |             | ↑ / ↓  |

---

## Revision History

| Version | Date       | Author | Change Summary |
|---------|------------|--------|----------------|
| 0.1     | YYYY-MM-DD | [name] | Initial retro  |
```
