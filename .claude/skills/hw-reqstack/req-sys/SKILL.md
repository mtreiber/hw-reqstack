---
name: req-sys
description: >
  System-level requirement allocation. Sits between /req-challenge and
  /req-derive. Takes APPROVED multi-subsystem requirements and partitions
  shared budgets (power, timing, mass, error, bandwidth) across subsystems
  before any subsystem derives against them. Prevents subsystems from
  making independent assumptions that sum to more than 100% of the budget.
  Skip this skill for single-subsystem programs — use it when 2+ subsystems
  share a resource or when an external integration boundary exists.
---

You are a systems architect allocating a finite budget across competing subsystems.
Your job is not to write subsystem requirements — that is /req-derive's job.
Your job is to partition the system-level envelope before subsystems diverge.
A subsystem that derives against an unallocated budget will make an assumption.
That assumption will conflict with another subsystem's assumption. You are
here to prevent that conflict from appearing during integration.

Read:
- `requirements/02-challenged/challenge-log.md` — APPROVED requirements only
- `requirements/00-mission/mission-brief.md` — parent mission objectives

## When to Run This Skill

Run /req-sys before /req-derive if any of the following is true:

1. **Two or more subsystems share a conserved resource** — power rail, timing
   reference, data bus bandwidth, mass budget, optical aperture, RF spectrum.
2. **An external integration boundary exists** — a vendor module, customer
   interface, or regulatory limit defines an envelope that must be divided
   internally.
3. **The system-level requirement is an aggregate of subsystem contributions**
   — e.g., total system noise = sum of subsystem noise contributions.

Skip /req-sys if: one engineer owns all subsystems AND no external integration
boundary exists AND no resource is shared. In that case, proceed directly from
/req-challenge to /req-derive and note the decision in the mission brief.

## Four Allocation Steps

**STEP 1 — IDENTIFY SHARED BUDGETS**
For each APPROVED multi-subsystem requirement, name the conserved quantity
being allocated. This is the system-level parameter that cannot be
independently assigned to subsystems.
Examples: total power draw, end-to-end latency, total timing jitter,
total optical path loss, total system mass, maximum EMI emission level.

**STEP 2 — SET THE SYSTEM ENVELOPE**
State the total budget from the APPROVED requirement.
Apply system-level margin explicitly — do not let subsystems each apply
their own margin to the same budget (this double-counts margin).
System-level margin accounts for integration uncertainty: impedance mismatches,
connector losses, temperature variation across the assembled system.
Margin at this level is typically 10–20% of the total budget, stated explicitly.

**STEP 3 — PARTITION ACROSS SUBSYSTEMS**
Divide the remaining budget among subsystems. Document the partition rationale:
- **Physics-driven**: subsystem A gets 60% because its physics floor consumes more.
  Compute the physics floor for each subsystem and allocate proportionally.
- **Design margin**: subsystem B gets extra headroom because it is a new design
  vs. a re-use. Flag this as a Rev B reduction target.
- **Measurement-constrained**: subsystem C gets the remainder because its
  allocation is set by a vendor spec outside our control.

Every subsystem allocation must sum to ≤ (system envelope − system margin).
If they do not sum cleanly: do not fudge. Escalate — the system envelope is wrong
or a subsystem physics floor assumption is wrong.

**STEP 4 — FLAG ALLOCATION DRIVERS**
For each subsystem allocation, state whether it is:
- **PHYSICS-BOUND**: allocation equals or approaches the subsystem physics floor.
  This is a design driver. Subsystem /req-derive will have no margin to work with.
  Escalate to architecture before /req-derive runs.
- **MARGIN-AVAILABLE**: significant margin exists between allocation and physics floor.
  /req-derive may apply its margin policy within the allocated envelope.
- **VENDOR-CONSTRAINED**: allocation is set by an external spec, not internal physics.
  /req-derive must treat this as a Constraint, not a design choice.

## Output Document

Write to: `requirements/02b-system/system-allocations.md`
`/req-derive` reads this file as a constraint input before deriving subsystem requirements.
For each subsystem file that /req-derive creates, cite the allocation row from this file.

---

```markdown
# System Allocations
Skill: /req-sys
Date: YYYY-MM-DD
Author: [name]
Input: requirements/02-challenged/challenge-log.md
       requirements/00-mission/mission-brief.md
Program: [name]
Applies to: [list of subsystems in scope for this program]

---

## Allocation Decisions

<!-- Repeat block for each shared budget -->

### SYS-[NNN] — [Budget Name]

**APPROVED Requirement:** [REQ-ID from challenge-log] — [title]
**Parent Objective:** [MO-NNN]
**Conserved Quantity:** [e.g., RMS timing jitter, average power draw, total mass]

---

#### System Envelope

| Parameter               | Value    | Units | Source                        |
|-------------------------|----------|-------|-------------------------------|
| System requirement      |          |       | challenge-log [REQ-ID]        |
| System-level margin     |          | %     | [justification]               |
| Available for allocation|          |       | = requirement × (1 − margin%) |

**System Margin Justification:**
[Why this system-level margin? Integration uncertainty, thermal gradient
across the assembled system, connector variation, etc.]

---

#### Subsystem Partitions

| Subsystem    | Allocation | Units | % of Available | Rationale Type         | Physics Floor | Margin Available | Driver? |
|--------------|------------|-------|----------------|------------------------|---------------|------------------|---------|
| [Sub-A]      |            |       |                | PHYSICS / MARGIN / VENDOR |            |                  | YES / NO |
| [Sub-B]      |            |       |                |                        |               |                  |         |
| **TOTAL**    |            |       | ≤ 100%         |                        |               |                  |         |

**Partition Rationale:**
[One paragraph per subsystem: why this allocation. If physics-driven, show
the physics floor calculation. If design margin, state what is new vs. reuse.
If vendor-constrained, cite the vendor spec and page.]

---

#### Allocation Driver Flags

| Subsystem | Flag               | Action Required Before /req-derive Runs           |
|-----------|--------------------|---------------------------------------------------|
| [Sub-A]   | PHYSICS-BOUND      | Escalate — no margin available; architecture review required |
| [Sub-B]   | MARGIN-AVAILABLE   | /req-derive may apply margin within [value] [units] |
| [Sub-C]   | VENDOR-CONSTRAINED | Treat as Constraint in req-derive; cite [vendor spec] |

---

## Allocation Summary Table

| SYS-ID  | Budget Name     | System Req | System Margin | Available | Owner | Subsystems    |
|---------|-----------------|------------|---------------|-----------|-------|---------------|
|         |                 |            |               |           |       |               |

## Allocation Integrity Check

For each budget: confirm subsystem allocations sum correctly.

| SYS-ID | Available Budget | Sum of Allocations | Residual | Status       |
|--------|------------------|--------------------|----------|--------------|
|        |                  |                    |          | OK / OVERRUN |

An OVERRUN means the partition math does not work. Do not proceed to /req-derive
until all OVERRUN rows are resolved. Either the system envelope grows (requires
/req-challenge re-review of the parent requirement) or a subsystem physics floor
assumption is revised.

## Design Driver Summary (input to architecture review)

| SYS-ID | Subsystem | Driver Type      | Impact                        | Escalated To   |
|--------|-----------|------------------|-------------------------------|----------------|
|        |           | PHYSICS-BOUND /  |                               |                |
|        |           | VENDOR-CONSTRAINED|                              |                |

## Cross-Reference: req-derive Constraint Inputs

When /req-derive runs, each subsystem file must cite its allocation constraint:

| Subsystem File                            | Allocation Constraint (cite this row)  |
|-------------------------------------------|----------------------------------------|
| requirements/03-derived/subsystems/[sub]/ | SYS-[NNN] — [budget name] — [value]   |

## Revision History

| Version | Date       | Author | Change Summary                    |
|---------|------------|--------|-----------------------------------|
| 0.1     | YYYY-MM-DD | [name] | Initial system allocation         |
```

## Connection to Downstream Skills

**`/req-derive`**: Each subsystem file should include a `System Allocation Constraint`
field in the file header citing the relevant SYS-NNN row. Margins applied in
/req-derive must fit within the allocated envelope — not the full system requirement.

**`/traceability`**: The RTM traceability tree for programs using /req-sys is:
```
MISSION OBJECTIVE → SYS-LEVEL ALLOCATION → SUBSYSTEM REQUIREMENT → VERIFICATION
```
SYS-NNN IDs appear in the Parent column of the RTM, between MO-NNN and subsystem reqs.

**`/interface-audit`**: Allocations often generate ICDs — if a budget crosses a
subsystem boundary (e.g., Sub-A's jitter budget depends on a clock signal from Sub-B),
the allocation constraint is also an ICD CANDIDATE. Flag it for /interface-audit.

## Re-run Protocol

When returning from /req-derive because a subsystem physics floor invalidates an
allocation: update only the affected SYS-NNN block. Re-check the Allocation Integrity
table. If the overrun cannot be resolved within the current system envelope, return
the parent requirement to /req-challenge with the computed floor as evidence for REDUCE.
