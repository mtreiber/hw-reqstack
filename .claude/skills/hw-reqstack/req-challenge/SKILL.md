---
name: req-challenge
description: >
  Adversarial requirements review. Takes proposed requirements from
  requirements/01-proposed/ and the Mission Brief, and challenges every
  requirement on five axes: existence, ownership, physics, testability,
  and interface necessity. Produces a Challenge Log with disposition
  for every requirement. Nothing proceeds to req-derive without passing.
---

You are running a SpaceX-style requirements review. Your Iron Law:
every requirement is guilty until proven innocent.
You are not here to approve requirements. You are here to kill bad ones.
A requirement that survives your challenge is stronger for it.
A requirement that does not survive should not exist.

Read:
- `requirements/00-mission/mission-brief.md` — the approved mission objectives
- `requirements/01-proposed/proposed-requirements.md` — requirements to challenge

## Your Five Challenges

Run all five against every requirement. Document your finding for each.

**CHALLENGE 1 — EXISTENCE**
Why does this requirement exist?
Acceptable answers: physics demands it / customer will be harmed without it / regulation mandates it.
Unacceptable answers: "it's always been done this way" / "the customer asked for it with no justification" / "inherited from prior design."
→ Unacceptable answer: FLAG FOR DELETION.

**CHALLENGE 2 — OWNERSHIP**
Who is the named engineer who owns this requirement?
Ownership means: they wrote the governing analysis, they know the test method,
they are accountable if the requirement is wrong.
A team name, a department, or a standard number is not an owner.
→ No named individual: FLAG NEEDS OWNER.

**CHALLENGE 3 — PHYSICS FLOOR**
What physical law establishes the minimum?
State the governing equation and the computed value.
If the requirement is more conservative than physics demands by more than 20%:
→ FLAG REDUCE — justify the margin explicitly or reduce it.
If no physics grounding exists at all:
→ FLAG POLICY — note it is a design choice, not a physics-derived requirement.

**CHALLENGE 4 — TESTABILITY**
Write the test right now in one sentence.
Format: "[Measurement method] measures [parameter] on [article] and confirms [pass criterion]."
If you cannot write that sentence:
→ FLAG UNTESTABLE — requirement is a wish, not a requirement.
If the test requires equipment not yet procured or a system level not yet built:
→ FLAG PHANTOM TEST — note the dependency.

**CHALLENGE 5 — INTERFACE NECESSITY**
Does this requirement exist because two different organizations, teams, or vendors
need to coordinate?
If yes: could vertical integration or a design change eliminate this interface?
→ Yes, eliminable: FLAG ICD CANDIDATE — route to /interface-audit.
→ No, genuinely necessary: note the interface and proceed.

## Dispositions

Assign exactly one disposition per requirement:

- **APPROVED** — passes all five challenges; proceed to /req-derive
- **DELETE** — fails existence test; remove from set; log reason
- **REDUCE** — physics margin is excessive; return to originator with computed floor
- **DEFER** — requirement is real but premature; re-evaluate after named milestone
- **NEEDS OWNER** — valid requirement but no named individual assigned
- **UNTESTABLE** — rewrite with concrete pass/fail criterion before proceeding
- **ICD CANDIDATE** — route to /interface-audit before deriving subsystem reqs

## Output Document

Write the output to: `requirements/02-challenged/challenge-log.md`
Use this exact template. One entry per requirement reviewed.

---

```markdown
# Requirements Challenge Log
Skill: /req-challenge
Date: YYYY-MM-DD
Reviewer: [name]
Input: requirements/01-proposed/proposed-requirements.md
Mission Brief: requirements/00-mission/mission-brief.md (version: [x.x])

---

## Challenged Requirements

<!-- Repeat block for each requirement reviewed -->

### [REQ-ID] — [Short Title]

**Proposed Text:** [Exact text from proposed-requirements.md]
**Parent Objective:** [MO-NNN or NONE]

| Challenge        | Finding                                      | Flag         |
|------------------|----------------------------------------------|--------------|
| 1 — Existence    | [Why it exists / why it fails]               | OK / FLAG    |
| 2 — Ownership    | [Named owner or gap]                         | OK / FLAG    |
| 3 — Physics      | [Governing eq. / margin analysis / gap]      | OK / FLAG    |
| 4 — Testability  | [One-sentence test or gap]                   | OK / FLAG    |
| 5 — Interface    | [Interface nature / eliminability]           | OK / FLAG    |

**Disposition:** APPROVED | DELETE | REDUCE | DEFER | NEEDS OWNER | UNTESTABLE | ICD CANDIDATE

**Rationale:**
[One paragraph explaining the disposition. If DELETE, state what engineering
judgment or physics justifies removal. If REDUCE, state the computed floor.]

---

## Summary Table

| Req ID  | Title             | Disposition    | Owner              | Next Action               |
|---------|-------------------|----------------|--------------------|---------------------------|
|         |                   |                |                    |                           |

## Velocity Metrics

| Metric                           | Count |
|----------------------------------|-------|
| Requirements submitted           |       |
| APPROVED → /req-derive           |       |
| DELETE (removed from set)        |       |
| REDUCE (returned to originator)  |       |
| DEFER                            |       |
| NEEDS OWNER                      |       |
| UNTESTABLE                       |       |
| ICD CANDIDATE → /interface-audit |       |

## Revision History

| Version | Date       | Author | Change Summary |
|---------|------------|--------|----------------|
| 0.1     | YYYY-MM-DD | [name] | Initial review |
```
