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
- an **active-alignment (apply)** spec — how to bring an existing project into the
  layout losslessly,
- the **`reqs.md` tracking** spec, with templates for `reqs.md`, `reqs/<ID>.md`,
  and `todo.md`,
- the **`info.md` / `docs`** spec.

## Key design decisions
- **Files are memory, the chat is scratch** — durable state lives in `reqs.md` so a
  context reset loses nothing (G2).
- **Two-layer requirements** — a single overview/status table in `reqs.md`, with
  `reqs/<ID>.md` detail files only where an item needs one (G3).
- **Lightweight RE** — stable IDs, "shall", goal traceability; no heavyweight
  tooling, sized for a solo project (G3).
- **Lossless apply** — the alignment operation reorganizes, never discards or
  invents; requirements are inferred from code when records are sparse (G1, G2).
- **Self-application with guardrails** — the repo is governed by its own product;
  the author/subject recursion is handled by the guardrails in `CLAUDE.md` (G4, R7).

## This repo is self-applied
As of the first apply, `CLAUDE.md` imports `LLMbootstrap.md`, so the workflow now
governs this repo. The file is therefore both our patient (we edit it) and, for
project work, the manual we follow. See `CLAUDE.md` for how to hold both roles
without self-misalignment.
