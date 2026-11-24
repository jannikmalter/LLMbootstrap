# LLMbootstrap — Requirements

Status: Active · Updated: 2026-11-24

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
| R1 | F    | LLMbootstrap.md shall define a canonical file layout with one home per kind of information (`CLAUDE.md`, `reqs.md` + `reqs/`, `todo.md`, `changelog.md`, `info.md`, `docs/`, `README.md`). | M   | G1   | ☑    |
| R2 | F    | The file shall be portable — droppable into any project and usable without project-specific edits.                                                             | M   | G1   | ☑    |
| R3 | F    | The workflow shall keep durable state in files, with `reqs.md` as the single source of truth for requirements and status, `todo.md` for tasks, and `info.md` for design decisions, so clearing the agent's context loses no goals, requirements, decisions, or tasks. | M   | G2   | ☑    |
| R4 | F    | The file shall define an "active alignment" (apply) operation that reorganizes an existing project into the layout losslessly — losing no information and inventing nothing — inferring requirements from code when records are sparse. | M   | G1   | ☑    |
| R5 | C    | The workflow shall use lightweight RE conventions (stable IDs, singular verifiable "shall" requirements, goal traceability, a two-layer reqs structure) with no heavyweight tooling. | S   | G3   | ☑    |
| R6 | F    | The file shall provide ready-to-use templates for its artifacts (`reqs.md`, `reqs/<ID>.md`, `todo.md`, `changelog.md`).                                                        | S   | G1   | ☑    |
| R7 | C    | The workflow shall be safely self-applicable: governing its own repo without causing self-misalignment, with the author/subject recursion explicitly guarded.  | M   | G4   | ☑    |
| R8 | F    | The file shall define a `changelog.md` — a chronological change record kept as a separate file so `reqs.md` stays small enough to hold in context while the changelog can grow and is read on demand. | S   | G2   | ☑    |
| R9 | F    | The workflow shall record project-level open questions in a dedicated `reqs.md` section, so ambiguities are captured durably instead of guessed at or lost with the chat. | S   | G2   | ☑    |
| R10 | F   | The active-alignment operation shall be specified as an ordered procedure (inventory → classify → confirm → reconstruct → relocate → account → record) whose final accounting step makes losslessness checkable against the initial inventory. | S   | G1   | ☑    |
| R19 | F   | Active alignment shall, before changing any project file, write its inventory and plan to a working file, present a short summary, and wait for the user's confirmation unless the user has explicitly opted out. | M   | G1   | ☑    |
| R20 | C   | The alignment inventory shall exclude dependency, build, VCS, and generated files. | S   | G1   | ☑    |
| R21 | C   | Active alignment shall treat code files as read-only sources. | M   | G1   | ☑    |
| R22 | C   | Active alignment shall delete only files whose deletion is listed in its plan (confirmed per R19 unless opted out), apart from its own working file. | M   | G1   | ☑    |
| R23 | F   | The prescribed `CLAUDE.md` setup shall import `reqs.md`, and the workflow shall tell agents that do not load it automatically to read it before working. | M   | G2   | ☑    |

R1–R7 are recorded **(as-built)** — the implementation is `LLMbootstrap.md` itself (R7's guardrails live in `CLAUDE.md`); the file is the evidence. R8–R10 were added and implemented 2026-07-02: R8 motivated by an agent in another project spontaneously growing a changelog inside `reqs.md`; R9 because the workflow told agents to raise open questions but gave them no durable home; R10 because the lossless apply was five lines of prose with no verifiable procedure. R19–R22 (added 2026-11-24) restore safeguards that v1 had and the 2026-06-24 rewrite dropped without a recorded reason; R23 fixes B6.

## Bugs
Deviations from a requirement. `Ref` = the requirement broken.

| ID | Bug                                                                                   | Ref | Sev | Done |
|----|---------------------------------------------------------------------------------------|-----|-----|------|
| B1 | "Which hat" guardrail frames authoring vs. operating as mutually exclusive, but editing `LLMbootstrap.md` is both — leaving a foothold to skip the `reqs.md` reconcile under the authoring hat. | R7  | Md  | ☑    |
| B6 | Nothing loads `reqs.md`: the spec calls it the "always-loaded overview", but the prescribed imports are only `LLMbootstrap.md` and `info.md`, so reading it depends on the agent remembering to. Observed 2026-11-24 in this repo: a session started without it in context. | R3 | Hi | ☑ |
| B7 | Todos have two homes: the spec puts them in `reqs.md` (Working principles, "Where things live", tracking intro) and also in `todo.md`; the `reqs.md` template has no todo section. | R1 | Md | ☑ |
| B8 | Decisions have no defined home: Working principles say `reqs.md` / `reqs/`, but neither its template nor the classify step has the category; in this repo they live in `info.md`. | R1 | Md | ☑ |
| B9 | The alignment inventory need not be written to a file, so the losslessness check depends on chat and does not survive a context reset. | R10 | Md | ☑ |
| B10 | `reqs/R7.md` ("Which hat") still says every action is *either* authoring *or* operating — the exclusive framing B1 claims to have removed everywhere. | R7 | Lo | ☑ |
| B11 | IDs were reused: the 2026-06-24 rewrite restarted numbering, so R1–R10 and B1 mean something different in git history before that date. | R5 | Md | ☑ |
| B12 | Records lost or misstate history: the 2026-06-24 changelog line says "first self-apply", but `CLAUDE.md` held managed blocks from an earlier apply on 2026-06-23; an open v1 todo (non-file sources) was dropped without a trace. | R3 | Md | ☑ |
| B13 | Stale text: R1 and R6 omit `changelog.md`; `CLAUDE.md` guardrail 2 still says "once they exist" of the project records. | R3 | Lo | ☑ |

## Open questions
Ambiguities awaiting the user's decision — recorded here instead of guessed at.
Remove once answered, folding the answer into the records above.

- Should `README.md` document reference-mode usage (other projects pointing at a
  live copy of `LLMbootstrap.md`) alongside the copy-mode it describes now? The
  user in fact uses reference-mode. The product itself also assumes copy-mode:
  "`CLAUDE.md` imports this file automatically". (affects README, R2)
- Add a version/date marker to `LLMbootstrap.md` for public distribution? Raised
  2026-07-02, deprioritized for the user's own reference-mode use, never decided.
  Reference-mode may argue for it: every save here, even uncommitted, changes
  the rules other projects read.
- Should the guardrails name a third role, *live deployment*? Via reference-mode
  this copy is already in force elsewhere while being edited. (affects R7)
- How should the workflow treat an agent's built-in memory outside the repo, which
  competes with `reqs.md` as durable memory? (affects R3)
- How should an apply handle non-file sources (e.g. a chat) so that it is
  reproducible and provenance is recorded? Open todo from v1. (affects R4)

---
*Pri:* M/S/C (must/should/could). *Sev:* Hi/Md/Lo. IDs are permanent — never reuse.
*Before the 2026-06-24 rewrite, R1–R18 and B1–B5 were in use (see B11); numbering resumes at R19 / B6.*
*Detail files: `reqs/<ID>.md` (e.g. `reqs/R7.md`).*
