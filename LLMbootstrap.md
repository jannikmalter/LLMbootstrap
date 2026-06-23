# LLMbootstrap.md — the workflow definition

This file is the single source of truth for the structure, workflow, and rules
an LLM follows in this project. All work shall be performed in accordance with it.

`CLAUDE.md` imports this file automatically. Keep `CLAUDE.md` minimal — only
LLM-specific notes go there; everything else is defined here and augmented in the
project's own files, never duplicated.


## Working principles

- **`reqs.md` is the project's durable memory; the chat is scratch.** Read it at
  the start of a session to recover state, and keep it current as you go so
  clearing the context or starting a new chat loses nothing.
- **Reconcile before changing anything.** Check each request against `reqs.md`
  first: if it fits the existing goals and requirements, proceed; if it's new, add
  the requirement/bug/todo (or goal) first; if it changes one, edit that item
  first. Implement only once `reqs.md` reflects the intended change. After
  changing code, scan `reqs.md` and update affected items.
- **Don't work from memory or invent facts.** Goals, requirements, decisions, and
  tasks live in `reqs.md` / `reqs/`; if it isn't written there, it isn't decided.
  Record only what is actually established; raise anything ambiguous, out of
  scope, or conflicting as an open question instead of guessing.
- **Follow standard requirements-engineering principles** — kept simple and
  concise, since this is a solo project.

## Where things live

- **`reqs.md`** (+ `reqs/<ID>.md`) — goals, requirements, bugs, todos, and their
  status; the single source of truth for what's decided.
- **`todo.md`** — the development plan; each item references the ID it advances.
- **`info.md`** — how the code works: architecture, design, internal how-to.
  Auto-loaded alongside `CLAUDE.md` via the `@info.md` import (on agents without
  import support, link it from `CLAUDE.md` instead).
- **`docs/`** — bulky reference material (API refs, manuals, specs), linked from
  `info.md` and read on demand.
- **`README.md`** — the outward, technical overview for GitHub: what the project
  is, plus concise operating guidance.
- **`CLAUDE.md`** — minimal LLM-specific notes; imports this file and `info.md`.
- **This file** — rules and pointers only; put documentation in `info.md`, not here.


## Active alignment

Sometimes you need to actively scan the whole project and bring it into line with
this file — e.g. when this file is first added to an existing project, or when new
information has been dumped into the project folder. Organize all existing
information according to these conventions. This must be **lossless and
accurate**: lose no information and invent nothing. Expect requirement records to
be sparse or absent — infer them from the code (tag such items `(as-built)`). When
in doubt, ask the user.



## reqs.md — requirements & tracking

Track goals, requirements, bugs, and todos with stable IDs and light
traceability — no heavyweight tooling, just two markdown layers:

- **`reqs.md`** — the at-a-glance overview and the single source of truth for
  status, priority, severity, and which goal each item serves.
- **`reqs/<ID>.md`** — a detail file *only* for items that need one (repro steps,
  acceptance criteria, design notes). Trivial items stay as table rows.

Conventions:

- IDs are stable and never reused: `G#` goals, `R#` requirements (Type
  F=function / Q=quality / C=constraint), `B#` bugs. Todos are checklist lines.
- Requirements use "shall", are singular and verifiable, and each traces to a goal.
- Tracked status, priority, and severity live **only** in the `reqs.md` tables —
  the `Done` box there is authoritative. Checkboxes inside a `reqs/<ID>.md` are
  working notes (acceptance criteria, fix steps), not the tracked status.
- `Done` means *implemented and not known to be broken* — verified by use, not
  necessarily by a test. A problem found later becomes a `B#` bug, not a reopened
  requirement.
- You may record already-built behavior as a normal `R#` (describe what the code
  does, trace it to a goal) and mark it `Done` once the implementation is
  present — the code is the evidence. Note its source file/symbol in the detail
  file or with an inline `(as-built)` tag.
- On any add / complete / fix, update `reqs.md`. Reference IDs in commits, e.g.
  `fix(B1): ...`.

### Template — `reqs.md`

```markdown
# <PROJECT> — Requirements

Status: Active · Updated: <date>

## Goals
Why this exists. Everything below traces to one of these.

- **G1** — One-line goal.
- **G2** — One-line goal.

## Out of scope
What this deliberately will *not* do (stops scope creep).

- Excluded thing.

## Requirements
One row each. Use "shall". `Type`: F=function, Q=quality, C=constraint.

| ID  | Type | Requirement                                   | Pri | Goal | Done |
|-----|------|-----------------------------------------------|-----|------|------|
| R1  | F    | The system shall ...                          | M   | G1   | ☑    |
| R2  | F    | The system shall ...                          | S   | G1   | ☐    |
| R3  | Q    | ... within 1 s for 10k items.                 | S   | G2   | ☐    |
| R4  | C    | Must run on Python 3.11+, no external DB.      | M   | G1   | ☑    |

## Bugs
Deviations from a requirement. `Ref` = the requirement broken.

| ID | Bug                                  | Ref | Sev | Done |
|----|--------------------------------------|-----|-----|------|
| B1 | Non-ASCII filename corrupts on save. | R2  | Hi  | ☐    |



---
*Pri:* M/S/C (must/should/could). *Sev:* Hi/Md/Lo. IDs are permanent — never reuse.
*Detail files: `reqs/<ID>.md` (e.g. `reqs/R3.md`, `reqs/B1.md`).*
```

### Template — `reqs/<ID>.md` (requirement / feature)

```markdown
# R3 — Full-text search

**Requirement.** The system shall search the full text of all notes and return
matches within 1 s for a 10k-note corpus.

## Rationale
Browsing by filename does not scale past a few hundred notes. Traces to **G2**.

## Acceptance criteria
Verifiable conditions; all must hold before R3 is marked done in `reqs.md`.

- [ ] Query returns every note containing the term, case-insensitive.
- [ ] p95 latency < 1 s at 10k notes on the target host.
- [ ] Index updates when a note is added, edited, or deleted.
- [ ] Empty query returns no results (not all notes).

## Design notes
- Candidate: SQLite FTS5 vs. in-memory inverted index.
- Index rebuild trigger: file-watch vs. on-demand.

## Open questions
- Stem/normalize terms, or exact substring only?

## Links
Blocked by: R1  ·  Related bugs: —
```

### Template — `reqs/<ID>.md` (bug)

```markdown
# B1 — Non-ASCII filename corrupts note on save

## Summary
Saving a note whose filename contains non-ASCII characters writes a
zero-byte/garbled file. Violates **R2**.

## Steps to reproduce
1. Create a note named `notiz-üöä.md`.
2. Edit and save it in the app.
3. Reopen from disk.

## Expected
File content preserved, filename unchanged.

## Actual
File is empty / re-encoded; original content lost.

## Environment
App vX.Y · Python 3.11 · Linux/LXC · UTF-8 locale.

## Notes / cause
Suspected: filename encoded with the wrong codec on the write path.

## Fix
- [ ] Reproduce in a test.
- [ ] Normalize filename encoding (UTF-8) on read and write.
- [ ] Regression test with non-ASCII names.
```

## todo.md template

```markdown
# Todos

Work items. Reference the ID they advance.

- [ ] Implement search backend. (R3)
- [ ] Write deploy notes.
- [x] Add atomic-write test. (R4)

```


---

## info.md & docs/ — internal documentation

`info.md` is the internal counterpart to `README.md`: the README is the short
outward explanation derived from the goals, while `info.md` (with `docs/` for
bulk) holds the detailed internal documentation for whoever — human or agent —
works in the code.

- Keep explanatory prose in `info.md`; never let it pile up in `CLAUDE.md`.
- Put reference material too bulky to keep in context every session in `docs/`,
  linked from `info.md`. Don't move `reqs.md` or `README.md` there — they have
  their own homes.
- Update `info.md` when the code's behavior or design changes.

