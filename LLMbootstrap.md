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
this file. Applying to a messy existing project is the main event, and it is a
**maximum-effort** operation: every apply scans the *whole* project — every
markdown file and every code file — and aligns both the structure and the content
of the project's tracking files to this file's conventions. It is not a one-time
ingestion; it re-converges on every run.

There is exactly **one operation: Apply** (converge the project to the desired
state). The user may invoke it as **"execute LLMbootstrap.md"** (typically a new
project) or **"apply LLMbootstrap.md"** (typically updating an existing one) —
both run the same procedure. The only difference is what's already present:

- nothing present → everything is created;
- a module's managed block is present but outdated → it is replaced in place;
- a working file is present but doesn't match the conventions → it is restructured
  in place, **losslessly** (content relocated/reformatted, never dropped);
- already compliant → left untouched.

So re-running is always safe, never duplicates, and never loses information.

## Apply procedure

1. **Read the whole file**, including every module under **Modules**.
2. **Detect state.** For each module, check: do its working files exist, are they
   populated, and do they match the conventions (structure + content)? Is its
   managed `CLAUDE.md` block present and at the current version (see *Managed
   blocks*)? This decides whether the module needs to create its working file,
   restructure an existing one, refresh its block, or nothing.
3. **Plan (every run, no writes yet), module by module.** Sweep the *whole* project
   (within the *sweep scope* below) for the information each module tracks, wherever
   it lives, and work out — *in memory, writing nothing* — the changes the apply
   would make:
   - **Structure + align:** for each module's working file, decide whether it must
     be created (absent or empty), **restructured in place, losslessly** (present
     but off-template — relocate and reformat existing content, preserve every user
     addition: IDs, rows, notes, prose; delete nothing and invent nothing), or left
     untouched (already compliant).
   - **Install / refresh:** which scaffolding (dirs) is missing and which managed
     `CLAUDE.md` blocks need insert / replace (vX→vY) / leave-alone per *Managed
     blocks*.
   Compute the full proposed result; do not touch any project file in this step.
4. **Confirm — present a short summary and wait.** Show the user a *concise* summary
   of the plan from step 3: per module, what would be created / restructured /
   installed, the source of each inferred item, and any open questions (implied-but-
   ambiguous items, never invented entries). Keep it short — counts and the key
   items, not a full file dump — and offer the full diff on request. **Write nothing
   until the user confirms.** The only exception is an explicit opt-out ("don't ask",
   "just do it", "apply without confirming"): then skip the gate, proceed straight to
   step 5, and report afterwards what was changed and from where so it can be
   corrected. (A plain "apply"/"execute" is *not* an opt-out — it still gets the gate.)
5. **Execute, module by module, in order.** Only after confirmation (or under an
   explicit opt-out): apply the structure/align changes, then ensure scaffolding
   exists and insert-or-replace the managed `CLAUDE.md` blocks. This phase is
   idempotent and convergent — a file already matching the conventions is left
   untouched. Never undo a user's edits outside managed blocks.
6. **Report.** Print a short per-module summary of what happened
   (structured / aligned / installed / updated vX→vY / unchanged + why).
   See *State report*.

**The structuring/invention line.** Relocating, deduplicating, and reformatting
information that is *explicitly present* in the project is the job — do it freely.
Fabricating requirements, goals, or facts that are not stated anywhere is not —
never do it. When something is implied but ambiguous, list it as an open question
for the user rather than committing it.

**Sweep scope.** Apply is maximum-effort: scan the **whole** project, not a sample.
Read **every** markdown file (README, docs, design notes, an existing `CLAUDE.md`,
loose `*.md` anywhere) and **every** code file — mining source comments
(`TODO`/`FIXME`/`HACK`), docstrings, and header comments for trackable information.
Also read the issue tracker if reachable. **Code files are read-only sources: scan
them, never edit them during apply.** **Skip** only dependency, build, and VCS
directories (`.git`, `node_modules`, `dist`, `build`, `target`, `vendor`, `.venv`,
caches) and generated/minified files — they contain nothing human-authored. Read
exhaustively rather than sampling; only if the project is so large that a full scan
is impractical, tell the user what you are skipping and why rather than silently
narrowing.

### Safety rules

**Application-time** (govern *this* procedure):
- **Never modify `LLMbootstrap.md` as a side effect of applying.** Modules compile
  *out of* it; they never write back to it.
- **Restructuring is lossless — this is the highest-priority rule.** When aligning a
  populated working file to the conventions, relocate and reformat its content;
  never delete it, drop a field, or change its meaning. Invent nothing — anything
  implied but unstated becomes an open question, not a new entry. If a restructure
  cannot be done without losing or altering information, stop and ask.
- **Code and other non-tracking files are read-only.** Scan them as sources; never
  edit source code (logic, comments, or layout) during apply. Only managed
  `CLAUDE.md` blocks and the modules' own tracking files are written.
- **Managed blocks are owned by this tool; tracking files are restructured, not
  owned.** A managed block is replaced wholesale to its current version. A tracking
  file (e.g. `reqs.md`) is realigned to its template but its content stays the
  user's — preserved losslessly. When unsure whether something is a user edit or
  drift, ask before changing it.

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
**Setup actions**, split into **Structure** (every-run gathering of existing
content and lossless alignment into the module's format; confirm with user) and
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
4. Documentation — below.
<!-- add further modules here as they are defined -->

## State report (printed after every run)

```
LLMbootstrap apply — <project>
  core           installed               (CLAUDE.md block v1)
  requirements   structured + installed  (reqs.md from 7 sources, CLAUDE.md v2)
  readme         aligned                 (README.md restructured, 0 items lost)
  docs           outsourced + installed  (info.md from CLAUDE.md, 3 refs→docs/, CLAUDE.md v1)
  <module>       updated v1→v2            (CLAUDE.md block)
  <module>       unchanged               (already matches conventions)
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

**Structure + align (every run):**
- Sweep the *whole* project (per *Sweep scope*) for anything describing what it's
  for or what's left to do: every markdown file, every code file's
  `TODO`/`FIXME`/`HACK` comments and docstrings, an existing `CLAUDE.md`, the issue
  tracker, design notes.
- Reorganize the findings into the `reqs.md` template below: derive **Goals** and
  **Out of scope**, turn explicit "shall/should/must" statements into `R#`
  requirements (each traced to a goal), known defects into `B#` bugs, loose work
  items into todos. Spin off a `reqs/<ID>.md` only where real detail exists.
- **Reverse-engineered (as-built) requirements.** Behavior that is implemented in
  code but never written as a spec may be recorded as a normal `R#` requirement
  describing what the code does, traced to a goal. This stays within R6 — you are
  describing behavior *explicitly present* in the code, not inventing intent;
  anything the code does not make evident is an open question, not a requirement.
  Record provenance (the source file/symbol it was read from) in the item's
  `reqs/<ID>.md` (an **As-built** note) or as an inline `(as-built)` tag, and state
  in the report that it was inferred from code. **Done semantics:** mark an as-built
  requirement `Done` ☑ when its implementation is present in the code — the code is
  the evidence. These projects generally carry no test suite; the user verifies by
  *using* the software and files a `B#` bug when something is wrong, so an
  implemented requirement is not left open waiting for tests that won't be written.
  A defect found later becomes a bug, not a reopened requirement. (`Done` = behavior
  implemented and not known to be broken — see *How it works*.)
- If `reqs.md` is absent or empty, create it. If it exists but does not match this
  template (missing columns, malformed tables, orphaned `reqs/<ID>.md` files,
  status tracked outside the tables), **restructure it in place, losslessly**: keep
  every existing ID, row, and note, never renumber or drop an item, only normalize
  structure and fold in newly-found items. Newly-found statements are *added*; they
  never overwrite an existing row.
- **Feed the proposed `reqs.md` (and any detail files) into the confirmation
  summary** (Apply step 4): list the changes with the source of each item; items
  implied but ambiguous go under an "Open questions" list, not into the tables.
  Writing happens only after the user confirms (or under an explicit opt-out); the
  full diff is available on request.
- A `reqs.md` that already matches the template is left untouched (convergent).

**Install (every run):**
- Ensure a `reqs/` directory exists (create if absent).
- Ensure `CLAUDE.md` exists (create if absent), then insert-or-replace the
  **CLAUDE.md block** below using the managed-block rules.

### CLAUDE.md block
Insert or replace as a managed block:

```markdown
<!-- LLMbootstrap:module=requirements v=4 START — managed block, edit the source not here -->
## Requirements & tracking
- Goals, requirements, bugs, todos are tracked in `reqs.md` (overview + source of
  truth for status/priority/severity/trace) and `reqs/<ID>.md` detail files.
- IDs are stable and never reused: `G#` goals, `R#` requirements
  (Type F=function / Q=quality / C=constraint), `B#` bugs. Todos are checklist lines.
- Requirements use "shall", are singular and verifiable, and each traces to a goal.
- `Done` means implemented and not known to be broken — verified by use, not
  necessarily by a test; a problem found later becomes a `B#` bug, not a reopened
  requirement. You may record already-built behavior (as-built) and mark it `Done`
  once the implementation is present in the code (the code is the evidence).
- An item's tracked status, priority, and severity live ONLY in the `reqs.md`
  tables — the `Done` flag there is authoritative. Detail files (headed
  `# <ID> — <name>`) hold description plus *working* checklists (acceptance
  criteria, fix steps); those checkboxes are progress notes, not the tracked status.
- Give an item its own `reqs/<ID>.md` file only when it needs detail (repro steps,
  acceptance criteria, design notes). Trivial items stay as table rows / checklist lines.
- On add/complete/fix: update `reqs.md`. Reference IDs in commits, e.g. `fix(B1): ...`.
<!-- LLMbootstrap:module=requirements END -->
```

### How it works
Two layers. `reqs.md` is the at-a-glance overview and the single source of truth
for status, priority, severity, and traceability (which goal a requirement serves,
which requirement a bug breaks). `reqs/<ID>.md` files hold only the detail a table
row can't — and exist only for items that have such detail. The split keeps the
stable spec (goals, requirements) separate from high-churn items (bugs, todos) and
keeps status in exactly one place, so nothing desyncs. The ID is the link between
the overview row and its detail file. Requirements are written to be verifiable, but
verification here is normally by *using* the software — these projects rarely carry
a test suite. So `Done` means the behavior is implemented and not known to be broken;
a problem found later is filed as a `B#` bug rather than reopening the requirement.
This is what makes as-built requirements practical: when applying to a codebase built
before its spec, an `R#` recorded from existing code is marked `Done` as soon as its
implementation is present — the code is the evidence — instead of being parked open
waiting for tests nobody writes. The `Done` flag therefore means the same thing for
hand-authored and reverse-engineered requirements: implemented and believed correct,
with defects tracked as bugs.

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
**Structure + align (every run):**
- Read the project's **Goals** and **Out of scope** from `reqs.md` (Module 1),
  plus any existing README, docs, and usage examples within the *sweep scope*.
- If `README.md` is absent or a placeholder (just a title or empty), draft a
  technical README from the template below: a one-line summary and an **Overview**
  from the goals; **Requirements**/**Installation**/**Usage** from how the project
  is actually run; an optional short **How it works**. Document only what is real —
  where usage isn't established yet, leave a marked `TODO` rather than inventing
  commands or options.
- If `README.md` already has real content, align it **losslessly**: reconcile its
  structure with the template (section order, headings) and bring its Overview into
  line with the current goals, but preserve the user's prose — relocate or reword
  for the goals, never delete sections or invent features. If the goals and the
  README now disagree, surface the discrepancy rather than silently rewriting.
- Feed the proposed README changes into the confirmation summary (Apply step 4);
  write only after the user confirms (or under an explicit opt-out). A `README.md`
  that already matches the goals and template is left untouched.

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
It's generated from existing material, and on every apply re-aligned losslessly to
the current goals — structure and overview reconciled, the user's prose preserved —
so it can never silently drift from the goals it documents.

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

## Module 4 — Documentation

**Id:** `docs` (block version lives in its marker)

### Purpose
Keep `CLAUDE.md` focused on rules. Explanatory project documentation — how the code
works, architecture, design decisions, internal usage — lives in a dedicated
`info.md` that is **auto-loaded into context alongside `CLAUDE.md`**, and bulky
reference material (API references, manuals, specs, long-form docs) is consolidated
under a `docs/` folder. This stops `CLAUDE.md` from bloating into a manual and gives
documentation one durable home.

### Setup actions
**Structure + align (every run):**
- Sweep the *whole* project (per *Sweep scope*) for explanatory documentation: prose
  in an existing `CLAUDE.md` that explains how the system works (architecture, module
  walkthroughs, design rationale, internal how-to), plus stray docs and over-long
  docstrings/header comments that are really documentation rather than code.
- **Outsource from `CLAUDE.md` into `info.md`, losslessly.** Move explanatory prose
  out of `CLAUDE.md` into `info.md`; leave behind only the workflow rules, managed
  blocks, and (optionally) a one-line pointer. Relocate and reformat — never delete,
  reword beyond formatting, or invent.
- **Consolidate reference material under `docs/`.** Gather scattered reference
  material — API references, manuals, specs, design docs, loose `*.md` that are
  documentation rather than tracking files — into a `docs/` folder, and link them
  from `info.md`. Move files rather than copy; preserve their content verbatim. Never
  pull `reqs.md`, `README.md`, or `LLMbootstrap.md` into `docs/` — those are owned by
  other modules.
- If `info.md` is absent or empty, create it from what you find (or a short stub for
  a new project). If it exists but is off-template, align it losslessly.
- Feed the proposed `info.md`, the moves into `docs/`, and the `CLAUDE.md` trims into
  the confirmation summary (Apply step 4); write only after confirmation (or under an
  explicit opt-out). An `info.md`/`docs/` layout already matching the conventions is
  left untouched.

**Install (every run):**
- Ensure `info.md` exists (create if absent). Ensure a `docs/` directory exists when
  there is reference material to hold.
- Ensure `CLAUDE.md` exists, then insert-or-replace the **CLAUDE.md block** below —
  which both imports `info.md` so it auto-loads and records where documentation goes.

### CLAUDE.md block
Insert or replace as a managed block:

```markdown
<!-- LLMbootstrap:module=docs v=1 START — managed block, edit the source not here -->
## Documentation
- Project documentation (how the code works, architecture, design, internal usage)
  lives in `info.md`, auto-loaded via the import below. Bulky reference material
  (API references, manuals, specs) lives under `docs/`, linked from `info.md`.
- Keep `CLAUDE.md` to rules and pointers — put documentation in `info.md` (or
  `docs/`), never inline here. Update `info.md` when the code's behavior or design
  changes.
@info.md
<!-- LLMbootstrap:module=docs END -->
```

### How it works
`CLAUDE.md` is the agent's always-loaded context, so it must stay small and be
*rules*, not a manual. Claude Code's `@import` lets one auto-loaded file pull in
another, so `info.md` loads automatically alongside `CLAUDE.md` without being pasted
into it — documentation gets a dedicated home that is still always in context.
Reference material too bulky to keep in context every session (API refs, manuals)
goes under `docs/`, linked from `info.md` and read on demand. This is the internal
counterpart to Module 2's `README.md`: the README is the short outward-facing
explanation derived from the goals, while `info.md`/`docs/` hold the detailed
internal documentation for whoever — human or agent — is working in the code.

### Template — `info.md`
```markdown
# <Project> — Internal documentation

Auto-loaded with `CLAUDE.md`. How this project works, for anyone editing it.
For requirements/status see `reqs.md`; for the outward overview see `README.md`.

## Architecture
<The major pieces and how they fit together.>

## How it works
<Key flows / mechanisms, at the level a new contributor needs.>

## Conventions
<Project-specific patterns worth knowing before editing.>

## Reference
<Links into `docs/` for API references, manuals, and specs.>
```

---

<!-- ## Module N — <name>
**Id:** `<id>` (block version lives in its marker)
### Purpose
### Setup actions
### CLAUDE.md block
### How it works
### Templates -->
