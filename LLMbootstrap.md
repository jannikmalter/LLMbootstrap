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
  Record only what is actually established; anything ambiguous, out of scope, or
  conflicting goes into the **Open questions** section of `reqs.md` instead of
  being guessed at — asking in chat is not enough, since the chat is scratch.
- **Follow standard requirements-engineering principles** — kept simple and
  concise, since this is a solo project.

## Where things live

- **`reqs.md`** (+ `reqs/<ID>.md`) — goals, requirements, bugs, todos, open
  questions, and their status; the single source of truth for what's decided —
  and for what's still undecided.
- **`todo.md`** — the development plan; each item references the ID it advances.
- **`changelog.md`** — the chronological record of notable changes, newest first.
  Deliberately a separate file: `reqs.md` stays small enough to keep in context
  every session, while the changelog may grow freely and is read on demand — when
  you need to know how or why the project reached its current state.
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

Sometimes the whole project must be actively brought into line with this file —
when it is first added to an existing project, or when new information has been
dumped into the project folder since the last alignment. The operation is
**lossless and accurate**: lose no information, invent nothing. Work through
these steps in order; for a small re-alignment, scope the inventory to what is
new or changed.

1. **Inventory.** List every file and fragment that carries project
   information — code, docs, READMEs, TODO/FIXME comments, scratch notes,
   half-finished lists. Nothing is moved before it is on the list; the list is
   what losslessness is checked against in step 5.
2. **Classify.** Decide each item's kind: goal, requirement, bug, todo, open
   question, change history, internal design, bulky reference, outward overview,
   or LLM note. The kind determines the home ("Where things live" above).
3. **Reconstruct the records.** Fill `reqs.md` and its companions:
   - Recorded requirements and plans move over with their meaning intact —
     rephrase to "shall" form, but change no content.
   - Behavior evident in the code but recorded nowhere becomes an `R#` row
     tagged `(as-built)`, marked Done — the code is the evidence.
   - Stated intentions become todos; known defects become bugs; anything
     ambiguous or conflicting becomes an open question — never a guess.
4. **Relocate, don't rewrite.** Move content to its home preserving meaning;
   merge only exact duplicates and keep original wording when unsure. Detail
   too bulky for its home goes to `docs/` — it is never summarized away.
5. **Account for everything.** Walk the inventory: every item is now relocated
   (note where), deliberately left in place (note why), or raised as an open
   question. Only a fully absorbed source file may be deleted.
6. **Record the apply.** Add a `changelog.md` entry, update the `reqs.md`
   date, and report the open questions to the user.



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
- Open questions live in the `reqs.md` **Open questions** section: record one the
  moment something is ambiguous, conflicting, or underspecified — it blocks
  guessing, not all work, so continue with whatever is unaffected. Questions tied
  to a single item stay in that item's `reqs/<ID>.md`. When the user answers,
  fold the answer into the proper record (requirement, goal, detail file) and
  remove the question; the resolution is noted in `changelog.md`, not kept in
  the list.
- On any add / complete / fix, update `reqs.md` and append a line to
  `changelog.md`. Reference IDs in commits, e.g. `fix(B1): ...`.

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

## Open questions
Ambiguities awaiting the user's decision — recorded here instead of guessed at.
Remove once answered, folding the answer into the records above.

- Keep deleted notes in a trash folder, or delete permanently? (affects R2)

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

## changelog.md — the change record

`reqs.md` shows only the *current* state; `changelog.md` preserves the
*trajectory* — what changed and why, in order. Append one line per notable
change: a todo completed, a bug fixed, a requirement or goal added or changed, a
design decision that alters behavior. Newest first, referencing IDs.

The changelog lives in its own file, **not** inside `reqs.md`: it is the one
record that grows without bound, so it must not weigh down the always-loaded
overview. Do not load it by default — read it on demand when history matters,
and never treat it as tracked status (`reqs.md` remains authoritative).

### Template — `changelog.md`

```markdown
# Changelog

Newest first. One line per notable change; reference IDs.

- 2025-07-02 — Implemented full-text search backend. (R3)
- 2025-06-30 — Fixed non-ASCII filename corruption. (B1)
- 2025-06-28 — Widened R3 to include tag search; added B1.
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

