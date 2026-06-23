# LLMbootstrap

A single Markdown file that configures an AI coding agent to follow a consistent,
opinionated workflow in any project — new or existing.

## Overview
`LLMbootstrap.md` is a portable *source* file you drop into a project and tell your
AI coding agent to apply. It compiles a set of modules into the project: it creates
working files (such as `reqs.md`) and installs short, persistent rules into the
project's `CLAUDE.md` so the agent keeps following the workflow in every future
session — no need to re-read the source file.

The point is to make project documentation the project's **durable memory**. Goals,
requirements, bugs, and decisions live in tracked files, not in the chat, so an AI
coding session becomes disposable: clearing the context window or starting a new
chat loses nothing.

Applying is a **maximum-effort** operation. It scans the whole project — every
Markdown file and every code file (for `TODO`/`FIXME` comments and docstrings) —
and aligns both the *structure* and the *content* of the project's tracking files
to the conventions. It is **lossless**: existing information is relocated and
reformatted, never deleted, and nothing is invented. The operation is idempotent
and convergent — a single "apply" creates what's missing, restructures what drifted,
and leaves compliant parts untouched — so re-running never duplicates. It can even
be applied recursively to the repository that contains it.

## Requirements
- An AI coding agent that can read files and edit the workspace (e.g. Claude Code).
- Nothing else — the mechanism is plain Markdown the agent reads; no extra tooling,
  services, or dependencies.

## Installation
Copy `LLMbootstrap.md` into the root of your project.

```sh
cp LLMbootstrap.md /path/to/your/project/
```

## Usage
In a new project, tell the agent:

```
execute LLMbootstrap.md
```

In an existing project, or to roll out an updated workflow, tell the agent:

```
apply LLMbootstrap.md
```

Both run the same idempotent procedure. The agent reads the file, scans the whole
project, structures and losslessly aligns existing information into the conventions
(confirming first unless you've told it to proceed), installs the managed blocks
into `CLAUDE.md`, and prints a per-module report of what it created, aligned,
updated, or left unchanged.

## How it works
`LLMbootstrap.md` is the source; `CLAUDE.md` and the tracking files are the compiled
result. Each module does up to two jobs: **Structure** (scan the project and
reorganize existing information into the module's format, losslessly) and **Install**
(write short persistent rules into `CLAUDE.md`, wrapped in versioned
`<!-- LLMbootstrap:module=... -->` markers). The markers let a re-apply replace an
outdated block instead of duplicating it; code files are read-only sources and are
never edited.

Current modules:

- **Core conventions** — rules that protect the workflow and govern day-to-day work.
- **Requirements & tracking** — `reqs.md` plus `reqs/<ID>.md` detail files for
  goals, requirements, bugs, and todos.
- **GitHub README** — this document, derived from the project goals.
- **Working loop** — reconcile every change with `reqs.md` before implementing it,
  work from the tracked files rather than memory, and keep them current so no state
  lives only in the chat.

## License
TBD
