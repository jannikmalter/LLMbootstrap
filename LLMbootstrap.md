# LLMbootstrap.md — the workflow definition

This file defines the structure, workflow, and rules an LLM agent follows in a project,
so that the agent builds what the user decided, nothing important depends on the chat,
and the process costs little effort and context.

Each project's `CLAUDE.md` points to this file — usually to one shared copy — and imports
the project's own `@reqs.md` and `@info.md`. Outside its own repository, never change
this file: other projects share it. Don't repeat its rules in project files.

## Working principles

- **The records are the memory; the chat is scratch.** If `reqs.md` or `info.md` is not
  in your context, read it before doing anything else. Keep the records current as you
  go, so that a new session loses nothing. When work stays unfinished, note in `todo.md`
  where it stands and what comes next.
- **Reconcile before changing anything.** Check each request against `reqs.md`: if it
  fits, proceed; if it is new, add the requirement or bug first (or a todo, if it is only
  a work item); if it changes something, edit that first. Implement only what the records
  state and the user has accepted. After changing code, update the affected records.
- **Don't invent.** What the project shall do is decided in `reqs.md` / `reqs/`, how it is
  built in `info.md`, what to work on in `todo.md`; what isn't written there isn't
  decided. Record only what is established. Anything ambiguous, conflicting, or
  underspecified becomes an open question in `reqs.md` — asking in chat is not enough. It
  blocks guessing, not work: continue with whatever is unaffected. Keep project facts out
  of your built-in memory; they belong in these records.
- **Heal what you come across.** When the structure or records contradict each other or
  this file, are stale, misplaced, or broken, fix them without being asked:
  - *Unambiguous* — exactly one fix follows from this file, the records, or the code, and
    it changes no decision: fix it, add a changelog line, tell the user briefly.
  - *In doubt*, including doubt about which case it is — add an open question, finish what
    is unaffected, ask at the end, and fix it once the user has confirmed.
  - Always in doubt, unless this file prescribes it: accepting or dropping anything,
    changing what a goal or requirement means, choosing between conflicting statements,
    deleting a file.
  - Move text verbatim: write it to its new home first, then remove it from the old one.
  - Never heal this file or the code; code that deviates from a requirement becomes a bug.
    If the user asked only to look or explain, report instead of fixing.

## Where things live

- **`reqs.md`** (+ `reqs/<ID>.md`) — goals, scope, requirements, bugs, open questions: what
  the project shall do, and what is undecided. Loaded every session; detail files are
  read on demand.
- **`info.md`** — how it is built: architecture, design decisions and their reasons,
  internal how-to. Loaded every session.
- **`todo.md`** — the work plan and the only home for todos. Read it when planning,
  choosing, or resuming work.
- **`changelog.md`** — all history. May grow freely; read it only when history matters.
- **`docs/`** — reference material too bulky to load every session, linked from `info.md`.
- **`README.md`** — the short outward overview: what the project is and how to use it.
- **`CLAUDE.md`** — the pointers above and minimal LLM-specific notes, nothing else.
- **`alignment.md`** — working file that exists only during an alignment.

## Starting a project

In a new project, ask the user for its purpose, goals, and what is out of scope, then
create `CLAUDE.md`, `reqs.md`, `todo.md`, `changelog.md`, and `info.md` from what they
say. Don't fill gaps yourself. In an existing project, run an alignment.

## Active alignment

Alignment is the full sweep that brings a whole project into line with this file — when
it is first applied to an existing project, or when new information has been dumped into
the project folder. It is **lossless and accurate**: lose no information that still holds,
invent nothing. What is outdated may be dropped if the plan lists it and the changelog
says what went and why. Code files are read-only sources: a TODO comment is copied into
the records and stays in the code. For a small re-alignment, inventory only what is new or
changed. If `alignment.md` already exists, resume that alignment.

1. **Inventory.** Write `alignment.md` — always, even if the user waives confirmation. List
   every file and fragment that carries project information: code, docs, READMEs, TODO
   comments, scratch notes. Read rather than sample, but skip dependency, build, VCS, and
   generated files. Text that will move or be dropped is listed sentence by sentence.
2. **Classify.** Next to each item, note its kind (goal, requirement, bug, todo, open
   question, history, design, reference, overview, LLM note) and its planned home. List
   every file to be deleted and all content to be dropped.
3. **Confirm.** Up to here, write nothing but `alignment.md`. Show the user a short summary:
   what will be created, moved, dropped, and deleted, which requirements are taken over,
   and the open questions. Wait for their OK; skip the wait only if they explicitly said
   not to ask. Departing from the plan later needs a new OK.
4. **Reconstruct.** Fill the records, and make `CLAUDE.md` import `reqs.md` and `info.md`.
   - Requirements stated in the project's documents keep their meaning, in "shall" form.
     The OK in step 3 accepts those the summary listed; without it they are `Proposed`.
   - Behavior evident only in code becomes `(as-built)` and `Proposed`.
   - Stated intentions become todos, known defects bugs, anything unclear an open question.
5. **Relocate, don't rewrite.** Preserve wording when unsure; merge only exact duplicates.
   What is too bulky for its home goes to `docs/`, never summarized away.
6. **Account.** Mark every item in `alignment.md` with where it now is, why it stayed, that
   it was dropped as planned, or which open question covers it. Open the new home and find
   the sentence there before marking it as moved. Only then delete files, and only those
   in the plan.
7. **Record.** Add a changelog entry, naming any source that is not a file (such as a
   chat), and report open questions and `Proposed` items. Leave
   `alignment.md` in place until the user has accepted the result, then delete it. Accepting
   the result does not accept the `Proposed` items.

## reqs.md — requirements & tracking

Standard requirements engineering, kept simple: `reqs.md` is the overview; a detail file
`reqs/<ID>.md` exists only for items that need one.

- **Current state only** — no history in rows, prose, or footnotes, and no prose between
  the tables. Test: would this sentence be needed if the project had always been in its
  current state? If not, it belongs in `changelog.md`. A reason still needed for the work
  goes in `reqs/<ID>.md`, or `info.md` for a design decision.
- **What is over leaves.** When the user drops a requirement, or a bug's fix has been
  observed to work (or the user won't have it fixed), delete its row and detail file and
  add a changelog line saying what went and why.
- **IDs:** `G#` goals, `R#` requirements (Type F=function / Q=quality / C=constraint), `B#`
  bugs. Don't renumber; take new ones from the `Next IDs` line, so none is reused.
- **Requirements** use "shall", are singular and verifiable, and trace to a goal. Their
  priority is `?` until the user states one.
- **Status** lives only in the tables; checkboxes in a detail file are working notes.
  - `Proposed` — written by the agent, not confirmed by the user.
  - `Accepted` — the user wants it. Only the user accepts or drops; an explicit request
    counts, the agent's inference does not.
  - `Implemented` — built. Known deviations are listed as bugs.
  - `Verified` — *observed* to meet the requirement, by use or test, never from reading
    code alone; note how in the detail file or changelog.
  - Bugs: `Open`, then `Fixed` until the fix has been observed to work.
- **Changing what an accepted requirement means** needs acceptance again and returns it to
  `Accepted` until built. Rewording does not.
- **As-built:** existing behavior may be recorded with its source, tagged `(as-built)`. It
  is `Proposed`, since code proves what happens, not that it is wanted; once accepted, it
  is `Implemented`.
- **Open questions:** those about a single item may stay in its detail file. Once answered,
  move the answer to its proper record, note it in the changelog, remove the question.
- **Every change** updates the affected record and adds a changelog line. Reference IDs in
  commits, e.g. `fix(B1): ...`.
- **Older formats** are converted only in an alignment; until then read `Done` ☑ as
  `Implemented`, ☐ as `Accepted` (bugs: `Fixed`, `Open`). Converted requirements are taken
  over as in step 4, as-built ones as `Proposed`. Dropped items, finished todos, and
  history move to the changelog; a missing `Next IDs` line is added.

### Template — `reqs.md`

```markdown
# <PROJECT> — Requirements

## Goals
Why this exists. Everything below traces to one of these.

Purpose: one sentence.

- **G1** — One-line goal.
- **G2** — One-line goal.

## Out of scope
What this deliberately will *not* do (stops scope creep).

- Excluded thing.

## Requirements
One row each. Use "shall". `Type`: F=function, Q=quality, C=constraint.

| ID | Type | Requirement                                                            | Pri | Goal | Status      |
|----|------|------------------------------------------------------------------------|-----|------|-------------|
| R1 | F    | The system shall store each note as a Markdown file.                   | M   | G1   | Verified    |
| R2 | F    | The system shall preserve a note's content when saving it.             | M   | G1   | Implemented |
| R3 | F    | The system shall return every note that contains the search term.      | S   | G2   | Accepted    |
| R4 | Q    | The system shall return search results within 1 s for 10k notes (p95). | S   | G2   | Accepted    |
| R5 | C    | The system shall run on Python 3.11 or later.                          | M   | G1   | Verified    |
| R6 | F    | The system shall delete a note on request. (as-built: `notes.delete`)  | ?   | G1   | Proposed    |

## Bugs
Deviations from a requirement. `Ref` = the requirement broken.

| ID | Bug                                  | Ref | Sev | Status |
|----|--------------------------------------|-----|-----|--------|
| B1 | Non-ASCII filename corrupts on save. | R2  | Hi  | Open   |

## Open questions
Ambiguities awaiting the user's decision — recorded here instead of guessed at.
Remove once answered, folding the answer into the records above.

- Keep deleted notes in a trash folder, or delete permanently? (affects R6)

---
*R status:* Proposed → Accepted → Implemented → Verified. *B status:* Open → Fixed.
*Pri:* M/S/C (must/should/could), ? = not set by the user. *Sev:* Hi/Md/Lo.
*Next IDs:* G3, R8, B2 — removed IDs are not reused.
*Detail files: `reqs/<ID>.md` (e.g. `reqs/R3.md`, `reqs/B1.md`).*
```

### Template — `reqs/<ID>.md` (requirement / feature)

```markdown
# R3 — Full-text search

**Requirement.** The system shall return every note that contains the search term.

## Rationale
Browsing by filename does not scale past a few hundred notes. Traces to **G2**.

## Acceptance criteria
Verifiable conditions; all must hold before R3 is set to `Verified`.

- [ ] Searching `apple` returns a note containing `Apple` and one containing `apple`,
      and no note without the word.
- [ ] A note that was added, edited, or deleted is reflected in the next search.
- [ ] An empty query returns no results (not all notes).

## Design notes
- Candidate: SQLite FTS5 vs. in-memory inverted index.
- Index rebuild trigger: file-watch vs. on-demand.

## Open questions
- Stem/normalize terms, or exact substring only?

## Links
Blocked by: R1  ·  Related: R4 (speed)  ·  Related bugs: —
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

## todo.md — the work plan

Lines without IDs of their own. Unfinished work only: delete an item once it is done and
in the changelog.

```markdown
# Todos

Work items. Reference the ID they advance, if any.

- [ ] Implement search backend. (R3) — index works; next: update it on delete.
- [ ] Write deploy notes.
```

## changelog.md — the change record

One line per notable change, with its reason if known, newest first: a todo completed, a
bug fixed, a goal, requirement, or design decision added, changed, or dropped. Never load
it by default, never treat it as status.

```markdown
# Changelog

Newest first. One line per notable change; reference IDs.

- 2025-06-30 — Dropped R7 ("find notes by filename"): covered by full-text search. (R3)
- 2025-06-29 — Accepted full-text search. (R3)
- 2025-06-28 — Found B1: saving a note with a non-ASCII filename corrupts it.
```
