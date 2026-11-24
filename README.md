# LLMbootstrap

**`LLMbootstrap.md`** is a single, portable workflow definition you drop into a
project so an LLM coding agent works to a consistent, durable, file-based
structure — without losing state when its context is cleared.

## What it gives you
- **One home per kind of information** — requirements and status in `reqs.md`
  (+ `reqs/<ID>.md`), the development plan in `todo.md`, change history in
  `changelog.md` (read on demand), internal design in `info.md`, bulky reference
  in `docs/`, the outward overview in `README.md`, and minimal agent notes in
  `CLAUDE.md`.
- **Durable memory** — goals, requirements, decisions, and tasks live in files,
  not the chat, so clearing context loses nothing.
- **Lightweight requirements engineering** — stable IDs, "shall" requirements, and
  goal traceability; markdown only, sized for solo work.
- **Lossless adoption** — an "active alignment" step reorganizes an existing
  project into the structure, losing nothing that still holds and inventing nothing.

## Using it
Built for one user working with Claude Code.

1. Point the project's `CLAUDE.md` at the shared copy of `LLMbootstrap.md` in this
   repo's checkout, so every project follows the latest version. (Copying the file
   into the project and importing it with `@LLMbootstrap.md` also works.)
2. New project: ask the agent to start it; it asks you for purpose, goals, and scope.
   Existing project: *"Bring this project into line with `LLMbootstrap.md`."*

For an existing project, the agent first shows you a summary of what it would create, move, or delete and
waits for your OK. It then creates or reorganizes the project's files to match the
structure, relocating existing content rather than discarding it, and reports
anything unclear as an open question. Requirements it infers from code are only
proposals until you accept them. Code files are read but never edited.

In daily work, the agent also fixes unambiguous problems in the records as it comes
across them, and asks when in doubt.

## This repository
This repo builds `LLMbootstrap.md` and is itself governed by it. See `info.md` for
the internal design and `CLAUDE.md` for how the project handles being governed by
its own product.
