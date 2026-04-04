# Requirements Directory Schema

This directory holds all requirements artifacts for the program.
Each folder is produced and consumed by specific skills in `.claude/skills/hw-reqstack/`.
Work flows top to bottom through the numbered folders. Do not skip stages.

---

## Directory Structure

```
requirements/
├── 00-mission/                  ← /mission-challenge
│   └── mission-brief.md             Mission objectives, dispositions, change-impact log
│
├── 01-proposed/                 ← Input to /req-challenge; fed by /req-retro Q4
│   └── proposed-requirements.md     Raw proposed requirements before challenge
│
├── 02-challenged/               ← /req-challenge
│   └── challenge-log.md             Challenge results and dispositions per req
│
├── 02b-system/                  ← /req-sys  (skip for single-subsystem programs)
│   └── system-allocations.md        Budget partitions across subsystems before req-derive
│
├── 03-derived/                  ← /req-derive
│   └── subsystems/
│       └── [subsystem-name].md      Physics-derived requirements per subsystem
│
├── 04-interfaces/               ← /interface-audit
│   ├── icd-audit.md                 ICD audit report with dispositions + REDUCE tracking
│   └── icd/
│       └── [a]-to-[b].md            One file per KEEP interface (both owners must approve)
│
├── 05-verification/             ← /verify-plan, /test-record
│   ├── verify-matrix.md             Verification plan for every derived requirement
│   └── test-records/
│       └── TR-[NNN].md              One test record per completed test (by /test-record)
│
├── 06-traceability/             ← /traceability
│   ├── rtm.md                       Live requirements traceability matrix + flag report
│   └── rtm-[subsystem].md           Per-subsystem RTM (for programs >30 reqs)
│
├── 07-baseline/                 ← /baseline, /req-change
│   ├── current/
│   │   └── baseline-v[N.N].md       Active baseline package
│   └── archive/
│       └── v[prior]/                Prior baseline packages (never deleted)
│
├── 08-retros/                   ← /req-retro
│   └── retro-[YYYY]-[milestone].md  Post-test retrospective per milestone
│
├── 09-design-goals/             ← /baseline (ASPIRATIONAL overflow), /req-retro
│   └── design-goals.md              ASPIRATIONAL requirements tracked separately
│
├── 10-changes/                  ← /req-change
│   └── CR-[NNN].md                  Change requests for post-baseline modifications
│
└── OWNERS.md                    ← Updated by /req-challenge, /req-derive, /baseline
    Named owner registry + deleted requirement log
```

---

## Lifecycle Flow

```
/mission-challenge   →  00-mission/mission-brief.md
/req-challenge       reads 00-mission, 01-proposed  →  02-challenged/challenge-log.md
/req-sys             reads 02-challenged             →  02b-system/system-allocations.md  (if 2+ subsystems)
/req-derive          reads 02-challenged, 02b-system →  03-derived/subsystems/
/interface-audit     reads 02-challenged, 03-derived →  04-interfaces/
/verify-plan         reads 03-derived, 04-interfaces →  05-verification/verify-matrix.md
/test-record         reads 05-verification            →  05-verification/test-records/TR-NNN.md
/traceability        reads 00,03,04,05               →  06-traceability/rtm.md
/baseline            reads 03,04,05,06, OWNERS       →  07-baseline/current/
/req-retro           reads 05,06,07,08,09            →  08-retros/, updates 01-proposed, 09-design-goals
/req-change          reads 03,04,05,06,07            →  10-changes/CR-NNN.md, updates 03,04,05,06,07
```

---

## Key Files

| File | Owner Skill | Purpose |
|------|-------------|---------|
| `OWNERS.md` | All skills | Named owner registry. One row per active req. Deleted req log at bottom. |
| `00-mission/mission-brief.md` | /mission-challenge | Mission objectives with dispositions and change-impact log. |
| `02-challenged/challenge-log.md` | /req-challenge | Six-challenge disposition for every proposed requirement. |
| `02b-system/system-allocations.md` | /req-sys | Budget partitions per shared resource. Each subsystem req-derive file cites its SYS-NNN row. Omit for single-subsystem programs. |
| `04-interfaces/icd-audit.md` | /interface-audit | ICD dispositions + REDUCE confirmation tracking (checked by /baseline Gate 7). |
| `06-traceability/rtm.md` | /traceability | Live RTM with flags. No 🔴 flags allowed before /baseline. |
| `07-baseline/current/baseline-v[N.N].md` | /baseline | Current baselined requirement set. Tagged in git as `baseline-vN.N`. |
| `09-design-goals/design-goals.md` | /baseline, /req-retro | ASPIRATIONAL requirements with owners and target maturity dates. |

---

## Rules

1. **Never skip a stage.** A requirement that bypasses /req-challenge is not challenged.
   A requirement that bypasses /verify-plan has no test plan.

2. **Never modify a baselined requirement outside /req-change.**
   Post-baseline changes that bypass /req-change are configuration violations.

3. **Never delete test records.** Failed tests are data. Superseded TRs are marked
   SUPERSEDED but not removed.

4. **Never delete prior baselines.** Archive, never delete. The archive is the audit trail.

5. **Every named owner must be a person, not a team.**
   OWNERS.md entries that list a team name are invalid.
