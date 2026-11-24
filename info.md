# info.md — how this project is built

Internal documentation for whoever works in this repo. The outward overview is in
`README.md`; the deliverable itself is `LLMbootstrap.md`; the recursion guardrails
are in `CLAUDE.md`.

## What the "code" is
The product of this repo is a single file, **`LLMbootstrap.md`** — a portable
workflow definition an agent reads to run a project. There is no build step and no
runtime: the file *is* the deliverable, and editing it is the development work.

## Structure of LLMbootstrap.md
The file is organized as:

- working principles (records as memory, reconcile before changing, don't invent,
  heal what you come across),
- a **"where things live"** map — one home per kind of information,
- **starting a project**,
- an **active-alignment (apply)** spec — a seven-step procedure (inventory →
  classify → confirm → reconstruct → relocate → account → record) that brings an
  existing project into the layout losslessly,
- the **`reqs.md` tracking** spec, with templates for `reqs.md` and `reqs/<ID>.md`,
- the **`todo.md`** spec — the only home for todos,
- the **`changelog.md`** spec — the on-demand change record.

## Key design decisions
- **Files are memory, the chat is scratch** — durable state lives in `reqs.md` so a
  context reset loses nothing (G2).
- **One home for todos, two for decisions** — todos live only in `todo.md`, read on
  demand when planning or resuming work. Decisions split by kind: what the project
  shall do in `reqs.md`, how it is built in `info.md`; both load every session.
- **Two-layer requirements** — a single overview/status table in `reqs.md`, with
  `reqs/<ID>.md` detail files only where an item needs one (G3).
- **Status instead of Done** — a single Done box hid who wanted a requirement and
  whether it was ever checked. The agent both writes and implements requirements, so
  accepting one is reserved to the user, and as-built items start as Proposed —
  otherwise a bug in the code becomes a completed requirement. `Implemented` and
  `Verified` are separate (R24–R27). Deliberately not adopted from professional RE,
  as too heavy for G3: stakeholder analysis, baselines (git is enough), traceability
  matrices, and per-requirement verification methods.
- **Current state only, and what is over leaves** — `reqs.md` and `todo.md` hold what
  is true and pending now; everything past goes to `changelog.md` (R28, R30, R31).
  Giving history a home was not enough: reasons had no slot in the tables, nothing
  was ever removed, and agents extend whatever text is already there, so the spec
  states a boundary and a one-sentence test. IDs need not be permanent rows: a
  `Next IDs` line keeps changelog and commit references unambiguous.
- **Templates obey the rules** — agents copy examples closely, so an example that
  breaks a rule teaches the violation (R29).
- **Self-healing, bounded** — alignment is a rare, deliberate sweep; healing is its
  always-on counterpart for what the agent comes across anyway (R32, R33). The line is
  whether exactly one fix follows and no decision changes; everything else becomes an
  open question, so healing cannot bypass the user's acceptance. This file is exempt:
  in reference-mode, healing it from one project would change the rules of all. Code is
  exempt too, since changing it is implementing. Verbatim write-then-remove guards
  against the loss seen when agents clean up casually (B15).
- **Lightweight RE** — stable IDs, "shall", goal traceability; no heavyweight
  tooling, sized for a solo project (G3).
- **Lossless apply** — the alignment operation loses nothing that still holds and
  invents nothing; outdated content may go, but only as planned and logged (R4).
  Requirements are inferred from code when records are sparse (G1, G2).
  Specified as an ordered procedure whose first step (inventory) and sixth step
  (accounting) bracket the work, making losslessness checkable rather than
  aspirational: nothing moves before it is listed, nothing is deleted before it
  is accounted for (R10). Moved text is listed sentence by sentence and looked up in
  its new home, because agents otherwise report text as moved that was lost.
- **Apply safeguards** — confirm before writing, skip dependency/build/VCS/generated
  files, code read-only, delete only what the plan lists (R19–R22). They matter
  because an agent applying the file may work in a project with no clean
  git state to fall back on. The inventory and plan live in a temporary
  `alignment.md`, so both the plan and the losslessness check survive a context
  reset.
- **`reqs.md` is imported, not just mentioned** — relying on the agent to remember to
  read it leaves sessions without it in context. `CLAUDE.md` imports it; the "if
  it is not in your context, read it" rule covers projects whose `CLAUDE.md` lacks the
  import (R23).
- **Changelog outside the loaded context** — `reqs.md` holds current state and is
  always in context; `changelog.md` holds the trajectory, may grow without bound,
  and is read on demand (G2, R8). Unspecified kinds of information get improvised
  homes, so the spec should assign one.
- **Open questions inside `reqs.md`** — the opposite placement from the
  changelog, deliberately: open questions are pending decisions that must stay in
  context until answered, and the list is bounded because answered questions are
  removed (their resolution goes to the changelog). Without this home, chat would be
  the only place to raise one — and the chat is scratch (G5, R9).
- **Taken-over requirements are accepted by the plan's OK** — an alignment may find dozens;
  one OK on a summary that lists them keeps acceptance with the user without asking for
  each (R25, G3). As-built behavior is excluded, since nobody ever stated it.
- **Files over agent memory** — Claude Code's own memory lies outside the repo and competes
  with the records, so project facts stay out of it (R39).
- **Built for one user with Claude Code** — no support for other agents or teams; that
  would cost words in every session (R2, G3).
- **Self-application with guardrails** — the repo is governed by its own product;
  the author/subject recursion is handled by the guardrails in `CLAUDE.md` (G4, R7).

## How the file is used in practice
The user's other projects do not copy `LLMbootstrap.md` — they reference this
repo's copy directly from their `CLAUDE.md`. Advantage: always the latest
version, no stale copies. Cost: reading a file outside the working directory
needs manual approval, so the agent must be prompted to read it each session
(accepted; can be avoided by granting the path read permission in the other
project's `.claude/settings.json`).

## This repo is self-applied
`CLAUDE.md` imports `LLMbootstrap.md`, so the workflow governs this repo. The file is therefore both our patient (we edit it) and, for
project work, the manual we follow. See `CLAUDE.md` for how to hold both roles
without self-misalignment.
