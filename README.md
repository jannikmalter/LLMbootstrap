# LLMbootstrap

A single Markdown file that configures an AI coding agent to follow a consistent,
opinionated workflow in any project — new or existing.

## Overview
`LLMbootstrap.md` is a portable *source* file you drop into a project and tell your
AI coding agent to apply. It compiles a set of modules into the project: it creates
working files (such as `reqs.md`) and installs short, persistent rules into the
project's `CLAUDE.md` so the agent keeps following the workflow in every future
session.

Applying it to an existing, messy project gathers information that's scattered
across README, docs, `TODO`/`FIXME` comments and the issue tracker, restructures it
into the conventions, and then keeps it tidy. The operation is idempotent: a single
"apply" converges a project to the desired state — creating what's missing,
refreshing what's outdated, leaving compliant parts untouched — so re-running never
duplicates. It can even be applied recursively to the repository that contains it.

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

Both run the same idempotent procedure. The agent reads the file, structures any
existing information into the conventions (confirming first unless you've told it to
proceed), installs the managed blocks into `CLAUDE.md`, and prints a per-module
report of what it created, updated, or skipped.

## How it works
`LLMbootstrap.md` is the source; `CLAUDE.md` is the compiled result. Each module
does up to two jobs: **Structure** (gather and reorganize existing project
information) and **Install** (write short persistent rules into `CLAUDE.md`, wrapped
in versioned `<!-- LLMbootstrap:module=... -->` markers). The markers let a re-apply
replace an outdated block instead of duplicating it; anything outside the markers is
yours and is preserved.

Current modules:

- **Core conventions** — rules that protect the workflow and govern day-to-day work.
- **Requirements & tracking** — `reqs.md` plus `reqs/<ID>.md` detail files for
  goals, requirements, bugs, and todos.
- **GitHub README** — this document, derived from the project goals.

## License
TBD
