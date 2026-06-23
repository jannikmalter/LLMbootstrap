# LLMbootstrap — Requirements

Status: Active · Updated: 2026-06-24

## Goals
Why this exists. Everything below traces to one of these.

- **G1** — Give an LLM agent a single, portable file that imposes a consistent, durable, file-based structure and workflow on any project it is dropped into.
- **G2** — Lose no project state across context resets: goals, requirements, decisions, and tasks live in files, not the chat.
- **G3** — Stay lightweight and solo-friendly: simple requirements-engineering conventions, no heavyweight tooling.
- **G4** — Be safely self-applicable: the workflow can govern its own repo without self-misalignment.

## Out of scope
What this deliberately will *not* do.

- Replace heavyweight requirements/issue-tracking tooling — this is markdown only, solo-scale.
- Automate or enforce the workflow (no hooks/CI). It relies on the agent reading and following the file.

## Requirements
One row each. Use "shall". `Type`: F=function, Q=quality, C=constraint.

| ID | Type | Requirement                                                                                                                                                   | Pri | Goal | Done |
|----|------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|-----|------|------|
| R1 | F    | LLMbootstrap.md shall define a canonical file layout with one home per kind of information (`CLAUDE.md`, `reqs.md` + `reqs/`, `todo.md`, `info.md`, `docs/`, `README.md`). | M   | G1   | ☑    |
| R2 | F    | The file shall be portable — droppable into any project and usable without project-specific edits.                                                             | M   | G1   | ☑    |
| R3 | F    | The workflow shall keep durable state in files, with `reqs.md` as the single source of truth, so clearing the agent's context loses no goals, requirements, decisions, or tasks. | M   | G2   | ☑    |
| R4 | F    | The file shall define an "active alignment" (apply) operation that reorganizes an existing project into the layout losslessly — losing no information and inventing nothing — inferring requirements from code when records are sparse. | M   | G1   | ☑    |
| R5 | C    | The workflow shall use lightweight RE conventions (stable IDs, singular verifiable "shall" requirements, goal traceability, a two-layer reqs structure) with no heavyweight tooling. | S   | G3   | ☑    |
| R6 | F    | The file shall provide ready-to-use templates for its artifacts (`reqs.md`, `reqs/<ID>.md`, `todo.md`).                                                        | S   | G1   | ☑    |
| R7 | C    | The workflow shall be safely self-applicable: governing its own repo without causing self-misalignment, with the author/subject recursion explicitly guarded.  | M   | G4   | ☑    |

All rows are recorded **(as-built)** — the implementation is `LLMbootstrap.md` itself (R7's guardrails live in `CLAUDE.md`); the file is the evidence.

## Bugs
Deviations from a requirement. `Ref` = the requirement broken.

*None open.*

---
*Pri:* M/S/C (must/should/could). *Sev:* Hi/Md/Lo. IDs are permanent — never reuse.
*Detail files: `reqs/<ID>.md` (e.g. `reqs/R7.md`).*
