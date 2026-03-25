# CLAUDE.md — Project Configuration

## hw-reqstack

Requirements management for hardware programs using the Silicon Valley
engineering philosophy: requirements are guilty until proven innocent,
owned by named engineers, derived from physics, and tested at the moment
they are written.

---

## Skills

| Skill               | Command              | Input                              | Output                                          |
|---------------------|----------------------|------------------------------------|-------------------------------------------------|
| mission-challenge   | /mission-challenge   | Freeform mission objective         | requirements/00-mission/mission-brief.md        |
| req-challenge       | /req-challenge       | requirements/01-proposed/          | requirements/02-challenged/challenge-log.md     |
| req-derive          | /req-derive          | requirements/02-challenged/        | requirements/03-derived/subsystems/[name].md    |
| interface-audit     | /interface-audit     | requirements/02-challenged/ + 03-  | requirements/04-interfaces/icd-audit.md         |
| verify-plan         | /verify-plan         | requirements/03-derived/ + 04-     | requirements/05-verification/verify-matrix.md   |
| traceability        | /traceability        | requirements/03- + 04- + 05-       | requirements/06-traceability/rtm.md             |
| baseline            | /baseline            | requirements/06-traceability/rtm.md| requirements/07-baseline/current/baseline-vN.N  |
| req-retro           | /req-retro           | requirements/05- + 07- + test rec. | requirements/08-retros/retro-[YYYY]-[ms].md     |

## Sprint Order

Run skills in this order. Each skill reads from the output of the prior skill.
Do not skip steps. Do not run /baseline before /traceability clears red flags.

```
/mission-challenge
      ↓
/req-challenge
      ↓
/req-derive
      ↓
/interface-audit        ← runs in parallel with /req-derive if ICDs exist
      ↓
/verify-plan
      ↓
/traceability           ← must show zero red flags before /baseline
      ↓
/baseline
      ↓
  [Hardware build and test]
      ↓
/req-retro              ← feeds back into requirements/01-proposed/
```

## Iron Laws

These apply in every skill, every session, without exception.

1. **Owner**: Every requirement must have a named human owner. Not a team. A person.
2. **Physics**: Every requirement must cite the governing equation or analysis that justifies it.
3. **Test**: Every requirement must have a written test before it reaches baseline.
4. **Testability**: A requirement that cannot be tested is a wish. Flag it. Do not baseline it.
5. **Deletion**: Deleting a requirement is progress. Track deletions as proudly as additions.
6. **Margin**: Every margin value must state its specific justification. "Per guidelines" is not a justification.
7. **Maturity**: ASPIRATIONAL requirements do not enter baseline. They are design goals.

## File Naming Conventions

- Requirement IDs: `[SUBSYSTEM]-[NNN]` e.g. `LDR-001`, `TDC-003`, `SYS-001`
- Mission objectives: `MO-[NNN]`
- ICD files: `[subsystem-a]-to-[subsystem-b].md`
- Test records: `TR-[NNN]-[short-title].md`
- Baselines: `baseline-v[major].[minor].md`
- Retros: `retro-[YYYY]-[milestone-slug].md`

## Skills Location

`.claude/skills/hw-reqstack/[skill-name]/SKILL.md`
