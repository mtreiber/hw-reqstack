# hw-reqstack

Requirements management for hardware programs, modeled on the engineering
philosophy used at SpaceX, Anduril, and Tesla. Implemented as a set of
Claude skills following the gstack pattern.

## Core Philosophy

Traditional requirements management is designed to distribute accountability
across a large organization in a way that is auditable after failure.

hw-reqstack is designed to make bad requirements die quickly and good
requirements stabilize fast. The assumption is that you will be wrong many
times before you are right, and the process should accelerate that learning
cycle, not protect against it.

## Skills

| Skill             | Role                                              |
|-------------------|---------------------------------------------------|
| mission-challenge | Kill bad objectives before they breed requirements|
| req-challenge     | Adversarial review — delete everything possible   |
| req-derive        | Bottom-up derivation from physics                 |
| interface-audit   | Reduce ICD count through design, not documentation|
| verify-plan       | Write the test at the same time as the requirement|
| traceability      | Find orphans, dead ends, and frozen requirements  |
| baseline          | Seven-gate release check                          |
| req-retro         | Post-test: were the requirements right?           |

## Usage

1. Clone this repo or copy the `.claude/` directory into your project root.
2. Open Claude in your project directory.
3. Start with `/mission-challenge` and describe your mission objective.
4. Follow the sprint order in CLAUDE.md.

## Requirements Workspace

All living requirements documents are in `requirements/`.
Every file is version-controlled. Requirement deletions are diffs, not erasures.
The history of why a requirement was deleted is a `git blame` away.

## Iron Laws (short form)

- Every requirement needs a named owner
- Every requirement needs a governing equation
- Every requirement needs a written test
- ASPIRATIONAL requirements don't baseline
- Deleting requirements is progress
