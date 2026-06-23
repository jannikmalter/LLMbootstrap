# LLMbootstrap

**`LLMbootstrap.md`** is a single, portable workflow definition you drop into a
project so an LLM coding agent works to a consistent, durable, file-based
structure — without losing state when its context is cleared.

## What it gives you
- **One home per kind of information** — requirements and status in `reqs.md`
  (+ `reqs/<ID>.md`), the development plan in `todo.md`, internal design in
  `info.md`, bulky reference in `docs/`, the outward overview in `README.md`, and
  minimal agent notes in `CLAUDE.md`.
- **Durable memory** — goals, requirements, decisions, and tasks live in files,
  not the chat, so clearing context loses nothing.
- **Lightweight requirements engineering** — stable IDs, "shall" requirements, and
  goal traceability; markdown only, sized for solo work.
- **Lossless adoption** — an "active alignment" step reorganizes an existing
  project into the structure without discarding or inventing anything.

## Using it
1. Copy `LLMbootstrap.md` into your project.
2. Have `CLAUDE.md` import it (`@LLMbootstrap.md`).
3. Ask your agent: *"Bring this project into line with `LLMbootstrap.md`."*

The agent then creates or reorganizes the project's files to match the structure,
relocating existing content rather than discarding it and asking when something is
unclear.

## This repository
This repo builds `LLMbootstrap.md` and is itself governed by it. See `info.md` for
the internal design and `CLAUDE.md` for how the project handles being governed by
its own product.
