# LLMbootstrap.md

Configuration source for setting up an AI coding agent (e.g. Claude Code / Cowork)
to follow my workflow in a project — whether brand new or already established.

## What this file is

This is the **source**. Running it *compiles* its modules into the project. Each
module does up to two jobs:

- **Structure** — gather information that already exists in the project, however
  scattered (README, docs, existing `CLAUDE.md`, `TODO`/`FIXME` comments, issue
  tracker, ad-hoc notes), and reorganize it into this file's conventions —
  e.g. into a structured `reqs.md`. This is migration of *what's already there*,
  not invention of new content.
- **Install** — write short, persistent rules into the project's `CLAUDE.md`
  (the context the agent auto-loads) so that future work *stays* in this
  structure without re-reading this file.

After a run, day-to-day behavior is driven by `CLAUDE.md`, **not** by re-reading
this file. Applying to a messy existing project is the main event: it ingests the
mess once, then keeps it tidy thereafter.

There is exactly **one operation: Apply** (converge the project to the desired
state). The user may invoke it as **"execute LLMbootstrap.md"** (typically a new
project) or **"apply LLMbootstrap.md"** (typically updating an existing one) —
both run the same procedure. The only difference is what's already present:

- nothing present → everything is created;
- a module's managed block is present but outdated → it is replaced in place;
- already compliant → left untouched.

So re-running is always safe and never duplicates.

## Apply procedure

1. **Read the whole file**, including every module under **Modules**.
2. **Detect state.** For each module, check: do its working files exist and are
   they populated? Is its managed `CLAUDE.md` block present and at the current
   version (see *Managed blocks*)? This decides whether the module needs the
   structuring phase, the install phase, both, or nothing.
3. **Structure (first-run ingestion), module by module.** If the module's working
   files are absent or empty, sweep the project for relevant information wherever
   it lives (within the *sweep scope* below) and reorganize it into the module's
   format. Then **confirm before writing — unless the user has pre-authorized
   proceeding.** A direct instruction to apply ("apply LLMbootstrap.md", "set up
   this project", "infer from chat", "don't ask") *is* pre-authorization: write
   directly and, in the report, state what was inferred and from where so it can
   be corrected. Absent such authorization (e.g. an unattended run, or a large
   ambiguous project), present the proposed result with the source of each item
   and wait for confirmation. Once written, the working file is the user's; do not
   re-scan or rewrite it on later applies unless explicitly asked to re-ingest.
   This keeps ingestion idempotent.
4. **Install / refresh, module by module, in order.** Ensure scaffolding (dirs)
   exists, then insert-or-replace the module's managed `CLAUDE.md` block per the
   *Managed blocks* rules. This phase is fully idempotent and runs on every apply.
   Never undo a user's edits outside managed blocks.
5. **Report.** Print a short per-module summary of what happened
   (structured / installed / updated vX→vY / unchanged / skipped + why).
   See *State report*.

**The structuring/invention line.** Relocating, deduplicating, and reformatting
information that is *explicitly present* in the project is the job — do it freely.
Fabricating requirements, goals, or facts that are not stated anywhere is not —
never do it. When something is implied but ambiguous, list it as an open question
for the user rather than committing it.

**Sweep scope.** When ingesting, read human-authored project text: README and
docs, design notes, an existing `CLAUDE.md`, the issue tracker, source comments
(`TODO`/`FIXME`/`HACK`), and loose `*.md` at or near the root. **Skip** dependency,
build, and VCS directories (`.git`, `node_modules`, `dist`, `build`, `target`,
`vendor`, `.venv`, caches) and generated/minified files. Prefer breadth over
exhaustive reading; if the project is large, sample representative files and ask
the user where to look rather than reading everything.

### Safety rules

**Application-time** (govern *this* procedure):
- **Never modify `LLMbootstrap.md` as a side effect of applying.** Modules compile
  *out of* it; they never write back to it.
- **Only managed blocks are owned by this tool.** Anything outside the markers is
  the user's; preserve it. When unsure whether something is a user edit or drift,
  ask before overwriting.

**Ongoing** (must govern day-to-day work *after* a run): because the agent then
works from `CLAUDE.md` and not this file, these rules can't live only here — they
are installed into `CLAUDE.md` by the **Core** module (Module 0). Applying the file
without Module 0 would leave a project unprotected.

**Meta / recursive use.** A project may contain this file and apply it to itself
(this repo does). "Never modify `LLMbootstrap.md`" means *as a side effect of
applying*; deliberately developing the workflow file is the exception — and the
whole point of the meta-project.

## Managed blocks (idempotency mechanism)

Every block this file writes into `CLAUDE.md` (or any shared file) is wrapped in
markers carrying the module id and a version. This is what lets *Apply* replace
an outdated block instead of duplicating it.

```markdown
<!-- LLMbootstrap:module=<id> v=<n> START — managed block, edit the source not here -->
...block content...
<!-- LLMbootstrap:module=<id> END -->
```

Rules for the agent:
- **Insert** the block (with markers) if no `START` marker for that `<id>` exists.
- **Replace** everything between the matching `START`/`END` markers if the marker
  exists but `v` differs from the module's current version. Note the bump in the
  report (`updated v1→v2`).
- **Leave untouched** if the marker exists at the current version.
- Match on `module=<id>` only; the version lives in the marker, so a changed
  version still matches the same block.
- The **current version** of a block is the `v=` written in that block's marker in
  this file — the single source of truth. It is not recorded anywhere else.

## Module conventions (for adding modules later)

Each module has, in this order:
**Id** (e.g. `id: requirements`; the block version lives in the marker, not here) ·
**Purpose** ·
**Setup actions**, split into **Structure** (first-run ingestion of existing
content into the module's format; confirm with user; skip once populated) and
**Install** (idempotent every-run scaffolding + managed-block injection). A module
may have only one of the two if the other doesn't apply. ·
**CLAUDE.md block** (the short persistent rules, shown wrapped in markers) ·
**How it works** (explanation/rationale) ·
**Templates** (inline, fenced; optional).

Keep `CLAUDE.md` blocks short — put detail and rationale in **How it works**, not
in the block. Bump the `v=` in the block's marker whenever its content changes, so
*Apply* knows to replace deployed copies.

## Modules
0. Core conventions — below.
1. Requirements & tracking — below.
2. GitHub README — below.
3. Working loop — below.
<!-- add further modules here as they are defined -->

## State report (printed after every run)

```
LLMbootstrap apply — <project>
  core           installed               (CLAUDE.md block v1)
  requirements   structured + installed  (reqs.md from 7 sources, CLAUDE.md v2)
  <module>       updated v1→v2            (CLAUDE.md block)
  <module>       unchanged
  <module>       skipped                 (reqs.md already populated)
```

---

## Module 0 — Core conventions

**Id:** `core` (block version lives in its marker)

### Purpose
Install the rules that must govern day-to-day work *after* a run — the ones that
can't live only in `LLMbootstrap.md` because the agent then reads `CLAUDE.md`, not
this file. Always runs first so later modules' blocks sit beneath it.

### Setup actions
**Install (every run):**
- Ensure `CLAUDE.md` exists (create if absent), then insert-or-replace the
  **CLAUDE.md block** below using the managed-block rules. (No Structure phase —
  there is nothing to ingest.)

### CLAUDE.md block
Insert or replace as a managed block:

```markdown
<!-- LLMbootstrap:module=core v=1 START — managed block, edit the source not here -->
## Project conventions (managed by LLMbootstrap)
- This project's workflow is defined by `LLMbootstrap.md`. The blocks between
  `<!-- LLMbootstrap:module=... -->` markers are compiled from it — edit the
  source and re-apply, never hand-edit a managed block.
- Do not modify or restructure `LLMbootstrap.md` as a side effect of routine work.
  Change it only when altering the workflow itself is the explicit task.
- Record only facts explicitly present in the project; don't fabricate. When
  something is implied but ambiguous, raise it as an open question.
<!-- LLMbootstrap:module=core END -->
```

### How it works
`LLMbootstrap.md` holds two kinds of rules: those that govern *applying* it
(application-time) and those that must govern *ongoing* work. Only the latter
belong in `CLAUDE.md`, since that is the context loaded in every future session.
This module is the bridge: it deploys the ongoing rules so a project — including
this meta-project — stays protected without re-reading the source. Keeping it as
Module 0 guarantees the protection is installed before anything else.

---

## Module 1 — Requirements & tracking

**Id:** `requirements` (block version lives in its marker)

### Purpose
Track goals, requirements, bugs, and todos with stable IDs and traceability,
without heavyweight tooling.

**Structure (first run only — when `reqs.md` is absent or empty):**
- Sweep the project for anything describing what it's for or what's left to do:
  README and other docs, an existing `CLAUDE.md`, `TODO`/`FIXME`/`HACK` comments,
  the issue tracker, design notes, scattered `*.md` files.
- Reorganize the findings into the `reqs.md` template below: derive **Goals** and
  **Out of scope**, turn explicit "shall/should/must" statements into `R#`
  requirements (each traced to a goal), known defects into `B#` bugs, loose work
  items into todos. Spin off a `reqs/<ID>.md` only where real detail exists.
- **Present the proposed `reqs.md` (and any detail files) to the user with the
  source of each item, and confirm before writing.** Items that are implied but
  ambiguous go under an "Open questions" list, not into the tables.
- If `reqs.md` already exists and is populated, skip this phase entirely — it is
  now user-owned content. (Re-ingest only on explicit request.)

**Install (every run):**
- Ensure a `reqs/` directory exists (create if absent).
- Ensure `CLAUDE.md` exists (create if absent), then insert-or-replace the
  **CLAUDE.md block** below using the managed-block rules.

### CLAUDE.md block
Insert or replace as a managed block:

```markdown
<!-- LLMbootstrap:module=requirements v=2 START — managed block, edit the source not here -->
## Requirements & tracking
- Goals, requirements, bugs, todos are tracked in `reqs.md` (overview + source of
  truth for status/priority/severity/trace) and `reqs/<ID>.md` detail files.
- IDs are stable and never reused: `G#` goals, `R#` requirements
  (Type F=function / Q=quality / C=constraint), `B#` bugs. Todos are checklist lines.
- Requirements use "shall", are singular and verifiable, and each traces to a goal.
- An item's tracked status, priority, and severity live ONLY in the `reqs.md`
  tables — the `Done` flag there is authoritative. Detail files (headed
  `# <ID> — <name>`) hold description plus *working* checklists (acceptance
  criteria, fix steps); those checkboxes are progress notes, not the tracked status.
- Give an item its own `reqs/<ID>.md` file only when it needs detail (repro steps,
  acceptance criteria, design notes). Trivial items stay as table rows / checklist lines.
- On add/complete/fix: update `reqs.md`. When implementing a requirement, satisfy
  its acceptance criteria before flipping its `Done` flag. Reference IDs in commits,
  e.g. `fix(B1): ...`.
<!-- LLMbootstrap:module=requirements END -->
```

### How it works
Two layers. `reqs.md` is the at-a-glance overview and the single source of truth
for status, priority, severity, and traceability (which goal a requirement serves,
which requirement a bug breaks). `reqs/<ID>.md` files hold only the detail a table
row can't — and exist only for items that have such detail. The split keeps the
stable spec (goals, requirements) separate from high-churn items (bugs, todos) and
keeps status in exactly one place, so nothing desyncs. The ID is the link between
the overview row and its detail file. Requirements are written to be verifiable;
"done" for a feature is defined by its acceptance criteria passing, not by judgment.

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

## Todos
Work items. Reference the ID they advance.

- [ ] Implement search backend. (R3)
- [ ] Write deploy notes.
- [x] Add atomic-write test. (R4)

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

---

## Module 2 — GitHub README

**Id:** `readme` (block version lives in its marker)

### Purpose
Maintain a `README.md` for GitHub readers: a technical document that states what
the project does and how to use it, derived from the project's goals. Not marketing.

### Setup actions
**Structure (first run — when `README.md` is absent or a placeholder, e.g. just a
title or empty):**
- Read the project's **Goals** and **Out of scope** from `reqs.md` (Module 1),
  plus any existing README, docs, and usage examples within the *sweep scope*.
- Draft a technical README from the template below: a one-line summary and an
  **Overview** from the goals; **Requirements**/**Installation**/**Usage** from how
  the project is actually run; an optional short **How it works**. Document only
  what is real — where usage isn't established yet, leave a marked `TODO` rather
  than inventing commands or options.
- Confirm before writing unless pre-authorized (see Apply step 3).
- If `README.md` already has real content, treat it as user-owned: do not
  overwrite; regenerate only on explicit request ("refresh the README").

**Install (every run):**
- Ensure `CLAUDE.md` exists, then insert-or-replace the **CLAUDE.md block** below.

### CLAUDE.md block
Insert or replace as a managed block:

```markdown
<!-- LLMbootstrap:module=readme v=1 START — managed block, edit the source not here -->
## README
- `README.md` is the GitHub-facing technical document: what the project does
  (from the goals in `reqs.md`) and how to use it. Keep it factual, not marketing.
- When the goals (`G#`) or the way the project is run change, update `README.md`.
- Don't document commands, options, or behavior that don't exist.
<!-- LLMbootstrap:module=readme END -->
```

### How it works
The README is the outward view of the same goals `reqs.md` tracks internally:
`reqs.md` is the working spec, the README is the public explanation derived from
it. Goals drive the Overview; the requirements and actual run steps drive Usage.
It's generated once from existing material, then owned by the user, with an
explicit refresh path so it can be re-derived when goals shift — the same
idempotency model as other working files.

### Template — `README.md`
(Outer fence is four backticks so the inner code fences render.)

````markdown
# <Project>

<One sentence: what it is and what it does.>

## Overview
<The problem it solves and why it exists — distilled from the project goals.>

## Requirements
- <Runtime / tooling / platform prerequisites.>

## Installation
```sh
<install / setup steps>
```

## Usage
<The primary workflow, with a minimal concrete example.>

```sh
<example invocation>
```

## How it works
<Short technical explanation of the mechanism, for readers who want it.>

## License
<License, or "TBD".>
````

---

## Module 3 — Working loop

**Id:** `workflow` (block version lives in its marker)

### Purpose
Make the work itself reqs-first: every requested change is reconciled with
`reqs.md` *before* it is implemented, nothing is done "from memory", and every
goal, task, and decision is written down — so clearing the context window or
starting a new chat loses no information.

### Setup actions
**Install (every run):**
- Ensure `CLAUDE.md` exists, then insert-or-replace the **CLAUDE.md block** below.
  (No Structure phase — Module 1 already ingests existing content; this module only
  installs the working discipline.)

### CLAUDE.md block
Insert or replace as a managed block:

```markdown
<!-- LLMbootstrap:module=workflow v=1 START — managed block, edit the source not here -->
## Working loop (reqs-first)
- Before making any change, reconcile it with `reqs.md` FIRST: if it fits the
  existing goals/requirements, proceed; if it is new, add the requirement / todo
  (or goal) first; if it changes one, edit that requirement first. Implement only
  after `reqs.md` reflects the intended change.
- Never work from memory. Goals, requirements, decisions, alignments, and tasks
  live in `reqs.md` / `reqs/`. If it isn't written there, it isn't decided — write
  it down rather than carrying it in your head.
- Keep `reqs.md` current as you go, so clearing the context or starting a new chat
  loses nothing. At the start of a session, read `reqs.md` to recover state before
  acting.
- If a request is ambiguous, out of scope, or conflicts with a goal, surface that
  against `reqs.md` and resolve it there before doing the work.
<!-- LLMbootstrap:module=workflow END -->
```

### How it works
`reqs.md` is treated as the project's durable memory, and the chat as scratch. The
loop is: reconcile → record → implement. Recording the intent in `reqs.md` *before*
implementing means the spec is always at least as current as the code, the chat
becomes disposable, and any future session can reconstruct the full state by
reading `reqs.md`. This is what lets context windows be cleared without loss: the
decisions never lived only in the conversation. It builds directly on Module 1
(which defines the structure) and Module 0 (which forbids fabricating undocumented
facts).

---

<!-- ## Module N — <name>
**Id:** `<id>` (block version lives in its marker)
### Purpose
### Setup actions
### CLAUDE.md block
### How it works
### Templates -->
