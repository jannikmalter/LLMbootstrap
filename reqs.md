# LLMbootstrap — Requirements

## Goals
Why this exists. Everything below traces to one of these.

Purpose: let one person build software with LLM agents across many projects and
sessions, so that the agent builds what that person decided, nothing important depends
on the chat, and the process costs little effort and context.

- **G1** — Adoption: any of the user's projects, new or existing, can adopt the workflow and stay on its current version.
- **G2** — Continuity: lose no project state across context resets; goals, requirements, decisions, and tasks live in files, not the chat.
- **G3** — Low cost: little tooling, little context, few interruptions.
- **G4** — Self-application (a goal of this repo, not of the product): the workflow can govern its own repo without self-misalignment.
- **G5** — Intent: the agent builds what the user decided, neither inventing nor silently changing it.

## Out of scope
What this deliberately will *not* do.

- Replace heavyweight requirements/issue-tracking tooling — this is markdown only, solo-scale.
- Automate or enforce the workflow (no hooks/CI). It relies on the agent reading and following the file.

## Requirements
One row each. Use "shall". `Type`: F=function, Q=quality, C=constraint.

| ID | Type | Requirement                                                                                                                                                   | Pri | Goal | Status |
|----|------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|-----|------|------|
| R1 | F    | LLMbootstrap.md shall define a canonical file layout with one home per kind of information (`CLAUDE.md`, `reqs.md` + `reqs/`, `todo.md`, `changelog.md`, `info.md`, `docs/`, `README.md`). | M   | G1   | Implemented |
| R2 | F    | The file shall govern any of the user's Claude Code projects, by reference or as a copy, without project-specific edits to it. | M   | G1   | Implemented |
| R3 | F    | The workflow shall keep durable state in files, with `reqs.md` as the single source of truth for requirements and status, `todo.md` for tasks, and `info.md` for design decisions, so clearing the agent's context loses no goals, requirements, decisions, or tasks. | M   | G2   | Implemented |
| R4 | F    | The file shall define an "active alignment" (apply) operation that reorganizes an existing project into the layout, losing no information that still holds and inventing nothing, inferring requirements from code when records are sparse. | M   | G1   | Implemented |
| R5 | C    | The workflow shall use lightweight RE conventions (stable IDs, singular verifiable "shall" requirements, goal traceability, a two-layer reqs structure) with no heavyweight tooling. | S   | G3   | Implemented |
| R6 | F    | The file shall provide ready-to-use templates for its artifacts (`reqs.md`, `reqs/<ID>.md`, `todo.md`, `changelog.md`).                                                        | S   | G1   | Implemented |
| R7 | C    | This repo's `CLAUDE.md` shall state guardrails against the author/subject recursion. | M   | G4   | Implemented |
| R8 | F    | The file shall define a `changelog.md` — a chronological change record kept as a separate file so `reqs.md` stays small enough to hold in context while the changelog can grow and is read on demand. | S   | G2   | Implemented |
| R9 | F    | The workflow shall record project-level open questions in a dedicated `reqs.md` section, so ambiguities are captured durably instead of guessed at or lost with the chat. | S   | G5   | Implemented |
| R10 | F   | The active-alignment operation shall be specified as an ordered procedure (inventory → classify → confirm → reconstruct → relocate → account → record) whose final accounting step makes losslessness checkable against the initial inventory. | S   | G1   | Implemented |
| R19 | F   | Active alignment shall, before changing any project file, write its inventory and plan to a working file, present a short summary, and wait for the user's confirmation unless the user has explicitly opted out. | ?   | G5   | Implemented |
| R20 | C   | The alignment inventory shall exclude dependency, build, VCS, and generated files. | ?   | G3   | Implemented |
| R21 | C   | Active alignment shall treat code files as read-only sources. | ?   | G1   | Implemented |
| R22 | C   | Active alignment shall delete only files whose deletion is listed in its plan (confirmed per R19 unless opted out), apart from its own working file. | ?   | G1   | Implemented |
| R23 | F   | The prescribed `CLAUDE.md` setup shall import `reqs.md`, and the workflow shall tell agents that do not load it automatically to read it before working. | ?   | G2   | Implemented |
| R24 | F   | The `reqs.md` tables shall track the lifecycle of each requirement and bug in a `Status` column. | ?   | G5   | Implemented |
| R25 | C   | The workflow shall reserve accepting a requirement to the user. | ?   | G5   | Implemented |
| R26 | C   | The workflow shall permit implementing only accepted requirements. | ?   | G5   | Implemented |
| R27 | C   | Behavior inferred from code shall be recorded as Proposed until the user accepts it. | ?   | G5   | Implemented |
| R28 | F   | Dropping a requirement shall remove its row and record what was dropped and why in `changelog.md`. | ?   | G2   | Verified |
| R29 | Q   | The templates in `LLMbootstrap.md` shall follow the rules that file states. | ?   | G1   | Implemented |
| R30 | C   | The workflow shall confine `reqs.md` to the current state. | ?   | G3   | Implemented |
| R31 | C   | The workflow shall confine `todo.md` to unfinished work. | ?   | G3   | Verified |
| R32 | F   | The workflow shall have the agent fix unambiguous issues it comes across in the project's structure and records without being asked. | ?   | G2   | Implemented |
| R33 | F   | The workflow shall have the agent obtain the user's confirmation before fixing an issue whose fix is in doubt. | ?   | G5   | Implemented |
| R34 | F   | The workflow shall have the agent reconcile each request with the records before changing anything. | ?   | G5   | Implemented |
| R35 | C   | The workflow shall forbid recording or building anything that is not established. | ?   | G5   | Implemented |
| R36 | F   | The file shall define how a new project is started, including asking the user for its goals. | ?   | G1   | Implemented |
| R37 | F   | The workflow shall have the agent record the state of unfinished work in `todo.md`. | ?   | G2   | Implemented |
| R38 | Q   | `LLMbootstrap.md` shall not exceed 2,500 words. | ?   | G3   | Verified |
| R39 | C   | The workflow shall keep project facts out of the agent's built-in memory. | ?   | G2   | Implemented |
| R40 | C   | The workflow shall leave a requirement's priority unset until the user states it. | ?   | G5   | Implemented |
| R41 | F   | Alignment shall name sources that are not files in its changelog entry. | ?   | G2   | Implemented |

## Bugs
Deviations from a requirement. `Ref` = the requirement broken.

| ID | Bug                                                                                   | Ref | Sev | Status |
|----|---------------------------------------------------------------------------------------|-----|-----|------|
| B9 | The alignment inventory need not be written to a file, so the losslessness check depends on chat and does not survive a context reset. | R10 | Md | Fixed |
| B15 | Moving history out of `reqs.md` can lose it: agents delete it unmoved, or report it as moved when part of it was not. | R4 | Md | Fixed |
| B16 | An alignment run without confirmation leaves no `alignment.md`, although it must stay until the user accepts the result; whether it was ever written is unknown. | R19 | Md | Fixed |

## Open questions
Ambiguities awaiting the user's decision — recorded here instead of guessed at.
Remove once answered, folding the answer into the records above.


---
*R status:* Proposed → Accepted → Implemented → Verified. *B status:* Open → Fixed.
*Pri:* M/S/C (must/should/could), ? = not set by the user. *Sev:* Hi/Md/Lo.
*Next IDs:* G6, R42, B20 — removed IDs are not reused.
*Detail files: `reqs/<ID>.md` (e.g. `reqs/R7.md`).*
