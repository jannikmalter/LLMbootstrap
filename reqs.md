# LLMbootstrap — Requirements

Status: Active · Updated: 2026-07-02

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
| R8 | F    | The file shall define a `changelog.md` — a chronological change record kept as a separate file so `reqs.md` stays small enough to hold in context while the changelog can grow and is read on demand. | S   | G2   | ☑    |
| R9 | F    | The workflow shall record project-level open questions in a dedicated `reqs.md` section, so ambiguities are captured durably instead of guessed at or lost with the chat. | S   | G2   | ☑    |
| R10 | F   | The active-alignment operation shall be specified as an ordered procedure (inventory → classify → reconstruct → relocate → account → record) whose final accounting step makes losslessness checkable against the initial inventory. | S   | G1   | ☑    |

R1–R7 are recorded **(as-built)** — the implementation is `LLMbootstrap.md` itself (R7's guardrails live in `CLAUDE.md`); the file is the evidence. R8–R10 were added and implemented 2026-07-02: R8 motivated by an agent in another project spontaneously growing a changelog inside `reqs.md`; R9 because the workflow told agents to raise open questions but gave them no durable home; R10 because the lossless apply was five lines of prose with no verifiable procedure.

## Bugs
Deviations from a requirement. `Ref` = the requirement broken.

| ID | Bug                                                                                   | Ref | Sev | Done |
|----|---------------------------------------------------------------------------------------|-----|-----|------|
| B1 | "Which hat" guardrail frames authoring vs. operating as mutually exclusive, but editing `LLMbootstrap.md` is both — leaving a foothold to skip the `reqs.md` reconcile under the authoring hat. | R7  | Md  | ☑    |

## Open questions
Ambiguities awaiting the user's decision — recorded here instead of guessed at.
Remove once answered, folding the answer into the records above.

- Should `README.md` document reference-mode usage (other projects pointing at a
  live copy of `LLMbootstrap.md`) alongside the copy-mode it describes now? The
  user in fact uses reference-mode. (affects README, R2)
- Add a version/date marker to `LLMbootstrap.md` for public distribution? Raised
  2026-07-02, deprioritized for the user's own reference-mode use, never decided.

---
*Pri:* M/S/C (must/should/could). *Sev:* Hi/Md/Lo. IDs are permanent — never reuse.
*Detail files: `reqs/<ID>.md` (e.g. `reqs/R7.md`).*
