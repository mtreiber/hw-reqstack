---
name: design-goals
description: >
  Tracks ASPIRATIONAL requirements that failed /baseline Gate 4.
  Design Goals represent real engineering intent that is not yet
  ready to commit as a baseline requirement. Every Design Goal must
  have a named owner and a target maturity date. Reviewed at every
  /req-retro session. A Design Goal that sits here for more than two
  cycles without progressing is a program risk.
---

This file is maintained by:
- /baseline — adds entries when ASPIRATIONAL requirements are moved here
- /req-retro Q3 — reviews all entries and updates status each cycle
- /req-derive — updates entries when analysis advances maturity

## Rules

1. A Design Goal is not a baseline commitment. It does not constrain the hardware build.
2. Every Design Goal must have a named owner (a person, not a team) and a target date.
   A Design Goal without both is a wish. Assign or delete.
3. At every /req-retro: each goal is either advancing or being deleted.
   "We will address this later" is not an acceptable disposition at retro.
4. When a Design Goal reaches ANALYTICAL or VALIDATED maturity, route it through
   /req-challenge → /req-derive → /verify-plan → /baseline in the next cycle.
5. A Design Goal that does not advance for two consecutive retro cycles is deleted.
   Log the deletion reason in the Deletion Log at the bottom of this file.

---

```markdown
# Design Goals Registry
Maintained by: /baseline, /req-retro, /req-derive
Last updated: YYYY-MM-DD

---

## Active Design Goals

<!-- Repeat block for each active goal -->

### DG-[NNN] — [Short Title]

| Field                | Value                                                       |
|----------------------|-------------------------------------------------------------|
| Origin               | [REQ-ID that was ASPIRATIONAL at baseline vN.N]            |
| Owner                | [Named engineer]                                           |
| Current Maturity     | ASPIRATIONAL                                                |
| Target Maturity      | ANALYTICAL-MANUAL / ANALYTICAL-SIMULATION / VALIDATED       |
| Target Date          | YYYY-MM-DD                                                  |
| Blocking Build?      | YES — [describe impact] / NO                               |
| Retro Cycles Here    | [N] (flag if ≥ 2)                                          |

**Goal Description:**
[One sentence: what the engineer wants to achieve, outcome-focused, no mechanism.]

**Why Not yet ANALYTICAL:**
[What analysis or test is needed to advance maturity, and what is preventing it.]

**Next Action:**
- [ ] [Specific action — owner — by date]

---

## Summary

| DG ID  | Title | Owner | Target Date | Cycles | Blocking? |
|--------|-------|-------|-------------|--------|-----------|
|        |       |       | YYYY-MM-DD  |        | YES / NO  |

---

## Deletion Log

<!-- Requirements deleted from this registry (permanent record) -->

| DG ID  | Title | Deletion Reason                   | Deleted By | Date       |
|--------|-------|-----------------------------------|------------|------------|
|        |       | TWO-CYCLE STALL / MISSION CHANGE  |            | YYYY-MM-DD |
|        |       | / GRADUATED TO BASELINE / OWNER   |            |            |
|        |       | RESIGNED / SCOPE CUT              |            |            |

## Revision History

| Version | Date       | Author | Change Summary          |
|---------|------------|--------|-------------------------|
| 0.1     | YYYY-MM-DD | [name] | Initial registry        |
```
