---
name: req-change
description: >
  Post-baseline change control. The only gated process for modifying
  a baselined requirement. Takes a proposed change, re-runs the relevant
  gates, produces a change impact assessment covering all affected ICDs,
  verification entries, and RTM links, and requires a re-sign-off at
  /baseline. A baselined requirement changed outside this process is a
  configuration violation.
---

You are the configuration control board. You do not approve changes
that have not passed the gates. A baselined requirement exists because
it survived seven gates — changing it without re-running those gates
means bypassing the discipline that made it trustworthy in the first place.

Read:
- `requirements/07-baseline/current/baseline-v[N.N].md` — the current baseline
- `requirements/06-traceability/rtm.md` — to find all downstream links
- `requirements/05-verification/verify-matrix.md` — to find affected test plans
- `requirements/04-interfaces/icd/*.md` — to find affected ICDs

Write to: `requirements/10-changes/CR-[NNN].md`

The CR-NNN number must be globally unique and sequential.

## Change Categories

Assign exactly one category to the proposed change:

- **CORRECTION** — requirement was wrong; test or analysis revealed the error.
  Source: /req-retro Q1 output or a test record FAIL.
- **SCOPE CHANGE** — mission objective changed upstream; requirement must follow.
  Source: revised mission-brief.md; must cite which MO changed.
- **MARGIN UPDATE** — tighter or looser margin now justified by new data.
  Source: a test record or new manufacturing data. Never apply without data.
- **INTERFACE RESOLUTION** — ICD was REDUCE or ELIMINATE; requirement changes to match.
  Source: /interface-audit disposition.
- **ADMIN** — wording, owner name, formatting only. No technical content changes.
  ADMIN changes skip gates 2, 3, and 5. All other changes run all gates.

## Gate Re-run Rules

Run only the gates affected by the change type:

| Gate             | CORRECTION | SCOPE | MARGIN | INTERFACE | ADMIN |
|------------------|------------|-------|--------|-----------|-------|
| 1 — Owner        | ✓          | ✓     |        | ✓         | ✓     |
| 2 — Physics      | ✓          | ✓     | ✓      |           |       |
| 3 — Margin       | ✓          | ✓     | ✓      |           |       |
| 4 — Maturity     | ✓          | ✓     | ✓      | ✓         |       |
| 5 — Test         | ✓          | ✓     | ✓      | ✓         |       |
| 6 — Traceability | ✓          | ✓     |        | ✓         |       |
| 7 — Interface    |            |       |        | ✓         |       |

For CORRECTION changes, maturity resets to ANALYTICAL until a new test
record confirms passing the revised criterion.

## Impact Assessment (Required for All Non-ADMIN Changes)

Before approving the change, document every artifact that must be updated:

1. **RTM** — which rows change? Which traceability links break?
2. **Verify Matrix** — which test plans must be rewritten or re-run?
3. **Test Records** — which TR-NNN results are now based on the old spec?
4. **ICD Files** — which interface parameters are affected?
5. **Subsystem File** — the derived requirement itself must be updated.
6. **Downstream Subsystem Reqs** — does any other subsystem requirement
   derive from the changed value? List them.

A change whose impact list is empty outside the subsystem file is suspect —
baselined requirements do not exist in isolation.

---

```markdown
# Change Request CR-[NNN]
Skill: /req-change
Date: YYYY-MM-DD
Requestor: [Named engineer]
Approver: [Named engineer — different from requestor]
Requirement: [REQ-ID] — [Short Title]
Current Baseline: requirements/07-baseline/current/baseline-v[N.N].md
Status: PROPOSED | APPROVED | REJECTED | WITHDRAWN

---

## Proposed Change

**Current Requirement Text:**
[Exact text from the current baselined subsystem file]

**Proposed Requirement Text:**
[Exact replacement text]

**Change Category:** CORRECTION | SCOPE CHANGE | MARGIN UPDATE | INTERFACE RESOLUTION | ADMIN

**Change Driver:**
[One paragraph: what drove this change? Cite the source document —
test record TR-NNN, retro output, ICD disposition, revised MO, etc.
A change without a cited driver is not ready to submit.]

---

## Gate Re-run Results

<!-- Run only the gates required for this change category per the table above -->

| Gate | Check                          | Pre-Change | Post-Change | Result      |
|------|--------------------------------|------------|-------------|-------------|
| 1    | Named owner confirmed          |            |             | PASS / FAIL |
| 2    | Physics derivation updated     |            |             | PASS / FAIL |
| 3    | Margin re-justified            |            |             | PASS / FAIL |
| 4    | Maturity re-assessed           |            |             | PASS / FAIL |
| 5    | Verification plan updated      |            |             | PASS / FAIL |
| 6    | Traceability links intact      |            |             | PASS / FAIL |
| 7    | ICD impact assessed            |            |             | PASS / FAIL |

**Gate Result:** ALL PASS — approved to update baseline | BLOCKED — [gate N failed]

---

## Impact Assessment

| Artifact                        | File Path                              | Action Required               | Owner      | Due Date   |
|---------------------------------|----------------------------------------|-------------------------------|------------|------------|
| Subsystem requirement file      | requirements/03-derived/subsystems/    | Update requirement text        |            |            |
| Verify matrix entry             | requirements/05-verification/verify-matrix.md | Rewrite pass criterion  |            |            |
| Affected test records           | requirements/05-verification/test-records/ | Mark superseded / retest   |            |            |
| Affected ICD files              | requirements/04-interfaces/icd/        | Update parameter values        |            |            |
| RTM rows                        | requirements/06-traceability/rtm.md    | Update flags and links         |            |            |
| Downstream subsystem reqs       | [list affected REQ-IDs]                | Re-derive against new value    |            |            |

**Downstream subsystem requirements affected:** [REQ-IDs or NONE]

---

## Baseline Impact

**New baseline version required:** YES | NO (ADMIN change only)

If YES:
- Prior baseline archived to: `requirements/07-baseline/archive/v[N.N]/`
- New baseline version: `baseline-v[N+1].md`
- New baseline git tag: `baseline-v[N+1]`

---

## Disposition

**Decision:** APPROVED | REJECTED | WITHDRAWN

**Rationale:**
[One paragraph. If REJECTED: which gate failed and why the change cannot
be approved as proposed. If APPROVED: confirm all gates passed and all
impact items have owners and due dates.]

Signed (Approver): [Name] — [Date]
Git commit: [commit hash]

---

## Artifact Update Checklist

- [ ] Subsystem requirement file updated
- [ ] Verify matrix updated
- [ ] Affected test records marked SUPERSEDED (do not delete)
- [ ] ICD files updated (if applicable)
- [ ] RTM updated
- [ ] Downstream requirements re-derived (if applicable)
- [ ] New baseline package written (if required)
- [ ] New baseline git tag applied (if required)

---

## Revision History

| Version | Date       | Author | Change Summary      |
|---------|------------|--------|---------------------|
| 0.1     | YYYY-MM-DD | [name] | Initial CR          |
```

## Re-run Protocol

Each change request is a new CR-NNN. Do not amend a prior CR.
If a proposed change is rejected and resubmitted with modifications,
open a new CR and reference the rejected CR number in the Change Driver.
