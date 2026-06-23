# LLMbootstrap — Requirements

Status: Active · Updated: 2026-06-23 (B1–B5 fixed)

## Goals
Why this exists. Everything below traces to one of these.

- **G1** — Provide a single source-of-truth markdown file that configures an AI
  coding agent to follow the user's workflow in any project.
- **G2** — Reconfigure both new and existing projects with one safe, repeatable
  operation that never duplicates or clobbers.
- **G3** — Be applicable recursively, including to the project that contains the
  file itself.
- **G4** — Make project documentation the durable memory so AI coding sessions are
  disposable: clearing the context or starting a new chat loses no information.

## Out of scope
What this deliberately will *not* do (stops scope creep).

- Heavyweight tooling, external services, or databases — the mechanism is plain
  markdown the agent reads.
- Inventing project content that is not explicitly present in the project.
- Modifying `LLMbootstrap.md` itself while applying it.

## Requirements
One row each. Use "shall". `Type`: F=function, Q=quality, C=constraint.

| ID  | Type | Requirement                                                                       | Pri | Goal | Done |
|-----|------|-----------------------------------------------------------------------------------|-----|------|------|
| R1  | F    | The system shall expose one idempotent operation, invokable as "execute" or "apply". | M   | G2   | ☑    |
| R2  | F    | Apply shall converge a project: create what's missing, replace drift, leave compliant items untouched. | M   | G2   | ☑    |
| R3  | F    | Managed `CLAUDE.md` blocks shall carry id+version markers enabling insert/replace/leave-alone. | M   | G2   | ☑    |
| R4  | F    | On first apply to an existing project, the system shall gather scattered info and restructure it into the conventions, confirming before writing. | M   | G1   | ☑    |
| R5  | C    | The system shall never modify `LLMbootstrap.md` during application.                | M   | G3   | ☑    |
| R6  | C    | The system shall never fabricate content not explicitly present; ambiguous items become open questions. | M   | G1   | ☑    |
| R7  | F    | Ingestion shall be idempotent: populated working files are user-owned and not re-scanned unless requested. | M   | G2   | ☑    |
| R8  | F    | The agent shall print a per-module state report after each run.                    | S   | G2   | ☑    |
| R9  | F    | The file shall be organized as ordered, self-contained modules with a defined structure. | M   | G1   | ☑    |
| R10 | Q    | `CLAUDE.md` blocks shall stay short; detail and rationale live in `LLMbootstrap.md`. | S   | G1   | ☑    |
| R11 | F    | The system shall support recording requirements that describe already-implemented work (reverse-engineered spec), with defined "done" semantics. | S   | G1   | ☐    |
| R12 | F    | The system shall provide a module that generates/maintains a GitHub `README.md` inferred from the project's goals — a technical document covering function and usage. | S   | G1   | ☑    |
| R13 | F    | The system shall provide a working-loop module: every change is reconciled with `reqs.md` before implementation, no work is done from memory, and all goals/decisions/tasks are documented so a cleared context or new chat loses no state. | M   | G4   | ☑    |

## Bugs
Deviations from a requirement. `Ref` = the requirement broken.

| ID | Bug                                                                                              | Ref | Sev | Done |
|----|--------------------------------------------------------------------------------------------------|-----|-----|------|
| B1 | Confirm-before-writing has no pre-authorization escape; following R4 forces violating a direct "apply" instruction. | R4  | Hi  | ☑    |
| B2 | Internal contradiction: rule says status lives ONLY in `reqs.md` tables, but `reqs/<ID>.md` templates carry acceptance-criteria / fix checkboxes (status). | R9  | Md  | ☑    |
| B3 | Safety rules (don't touch `LLMbootstrap.md`, blocks-only ownership) live in `LLMbootstrap.md`, not installed into `CLAUDE.md`, so they don't govern day-to-day work. | R5  | Hi  | ☑    |
| B4 | Ingestion sweep is underspecified — no scope, exclusions, or stopping rule for "scan scattered files". | R4  | Md  | ☑    |
| B5 | Version duplicated in module heading (`v: n`) and marker (`v=n`); manual sync drifts.             | R3  | Lo  | ☑    |

## Todos
Work items. Reference the ID they advance.

- [ ] Define further workflow modules beyond Module 1 (Requirements & tracking). (R9)
- [ ] Decide whether `reqs.md` *template* changes should propagate to existing projects, or stay user-owned only. (R7)
- [ ] Decide how to handle non-file sources (e.g. a chat) so an apply is reproducible / its provenance is recorded. (R4)

---
*Pri:* M/S/C (must/should/could). *Sev:* Hi/Md/Lo. IDs are permanent — never reuse.
*Detail files: `reqs/<ID>.md` (e.g. `reqs/R3.md`, `reqs/R4.md`, `reqs/B1.md`, `reqs/B3.md`).*
