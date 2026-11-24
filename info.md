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

- a working-principles preamble (`reqs.md` as durable memory, reconcile before
  changing, don't invent facts),
- a **"where things live"** map — one home per kind of information,
- an **active-alignment (apply)** spec — a seven-step procedure (inventory →
  classify → confirm → reconstruct → relocate → account → record) that brings an
  existing project into the layout losslessly,
- the **`reqs.md` tracking** spec, with templates for `reqs.md` and `reqs/<ID>.md`,
- the **`todo.md`** spec — the only home for todos,
- the **`changelog.md`** spec — the on-demand change record,
- the **`info.md` / `docs`** spec.

## Key design decisions
- **Files are memory, the chat is scratch** — durable state lives in `reqs.md` so a
  context reset loses nothing (G2).
- **One home for todos, two for decisions** — todos live only in `todo.md`, read on
  demand when planning or resuming work. Decisions split by kind: what the project
  shall do in `reqs.md`, how it is built in `info.md`; both load every session
  (B7, B8).
- **Two-layer requirements** — a single overview/status table in `reqs.md`, with
  `reqs/<ID>.md` detail files only where an item needs one (G3).
- **Lightweight RE** — stable IDs, "shall", goal traceability; no heavyweight
  tooling, sized for a solo project (G3).
- **Lossless apply** — the alignment operation reorganizes, never discards or
  invents; requirements are inferred from code when records are sparse (G1, G2).
  Specified as an ordered procedure whose first step (inventory) and sixth step
  (accounting) bracket the work, making losslessness checkable rather than
  aspirational: nothing moves before it is listed, nothing is deleted before it
  is accounted for (R10).
- **Apply safeguards** — confirm before writing, skip dependency/build/VCS/generated
  files, code read-only, delete only what the plan lists (R19–R22). v1 had
  the first three; the 2026-06-24 rewrite dropped them without a recorded reason.
  They matter because an agent applying the file may work in a project with no clean
  git state to fall back on. The inventory and plan live in a temporary
  `alignment.md`, so both the plan and the losslessness check survive a context
  reset (B9).
- **`reqs.md` is imported, not just mentioned** — relying on the agent to remember to
  read it left sessions without it in context (B6). `CLAUDE.md` imports it; the "if
  it is not in your context, read it" rule covers agents without import support and
  projects set up before the import was prescribed (R23).
- **Changelog outside the loaded context** — `reqs.md` holds current state and is
  always in context; `changelog.md` holds the trajectory, may grow without bound,
  and is read on demand (G2, R8). Motivated by an agent in another project
  spontaneously growing a changelog inside `reqs.md` — unspecified kinds of
  information get improvised homes, so the spec should assign one.
- **Open questions inside `reqs.md`** — the opposite placement from the
  changelog, deliberately: open questions are pending decisions that must stay in
  context until answered, and the list is bounded because answered questions are
  removed (their resolution goes to the changelog). Closed a gap where the
  workflow said "raise an open question" but chat was the only place to raise
  it — and the chat is scratch (G2, R9).
- **Self-application with guardrails** — the repo is governed by its own product;
  the author/subject recursion is handled by the guardrails in `CLAUDE.md` (G4, R7).

## How the file is used in practice
The user's other projects do not copy `LLMbootstrap.md` — they reference this
repo's copy directly from their `CLAUDE.md`. Advantage: always the latest
version, no stale copies. Cost: reading a file outside the working directory
needs manual approval, so the agent must be prompted to read it each session
(accepted; can be avoided by granting the path read permission in the other
project's `.claude/settings.json`). Field observation from this usage: an agent
in one project spontaneously grew a changelog inside `reqs.md` — the trigger
for R8.

## This repo is self-applied
As of the first apply, `CLAUDE.md` imports `LLMbootstrap.md`, so the workflow now
governs this repo. The file is therefore both our patient (we edit it) and, for
project work, the manual we follow. See `CLAUDE.md` for how to hold both roles
without self-misalignment.
