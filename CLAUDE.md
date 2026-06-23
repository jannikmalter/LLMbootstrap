This project builds **LLMbootstrap.md** — a portable workflow definition meant to be dropped into *other* projects to govern how an agent works in them.

Keep one framing straight at all times: **LLMbootstrap.md is first of all our patient — the code we work on.** We *operate on* it (edit, restructure, fix) and we never obey it blindly. When the finished file is dropped into *other* projects, those projects become *its* patients; here in this repo, the file is ours. As of the first self-apply it is now *also* the workflow we follow for project work (CLAUDE.md imports it, below) — so the recursion is live and the guardrails below are in force.

## The recursion hazard — read before editing anything

Applying LLMbootstrap.md to this repo makes the project both the **author** of the workflow and a **subject** of it: the patient we operate on also becomes a manual we obey. That is recursive, and the failure mode it creates is **self-misalignment**: a mistake in the definition silently becomes a rule we then obey and propagate, and the line between "the product" and "our own working records" collapses. A written rule — here or in LLMbootstrap.md — is never by itself proof that the rule is correct. Guard against this as follows.

### 1. Know which hat you are wearing
Before acting, decide which of two modes you are in; they carry different rules.
- **Authoring the artifact** — editing LLMbootstrap.md or its templates/examples *as the product*. This is normal editing: refactor, cut redundancy, restructure freely. The lossless / "no data loss" guarantee does **not** apply here.
- **Operating under the workflow** — doing project work the way LLMbootstrap.md prescribes. Only happens after a deliberate apply. Here the lossless guarantee **does** apply: an apply must lose no existing information and invent nothing new.

If you cannot tell which mode you are in, **stop and ask**. Guessing here corrupts the source of truth.

### 2. Keep product and project records strictly separate
- **Product content** is LLMbootstrap.md and *everything inside it*, including its templates and worked examples (e.g. the full-text-search requirement, the `notiz-üöä.md` bug, the Python/SQLite specifics). These are illustrative parts of the deliverable — **not** our requirements, bugs, or facts.
- **Project records** are this repo's own `reqs.md` / `info.md` / `README.md` and the like (once they exist), which describe *the work of building LLMbootstrap.md*.
- Never fold the file's examples into our records, and never treat our records as product content. Any "active alignment" run on this repo must not ingest the product's examples as project facts, nor trim or "optimize" product content under the banner of alignment.

### 3. Do not follow a rule you suspect is wrong
If obeying a rule looks wrong, conflicts with the task, or would corrupt the source of truth:
- **Stop and surface it.** Do not comply silently and do not work around it quietly.
- **Do not edit the definition merely to justify an action**, and do not act on a definition you suspect is broken. Fixing a flawed rule is its own deliberate decision, made openly — not a side effect of getting a task done.

### 4. The apply is deliberate, not ambient
This repo was self-applied on 2026-06-24: `CLAUDE.md` now imports `LLMbootstrap.md` (below), so the workflow governs project work here and the recursion is live. Honor it deliberately — when doing project work, follow the workflow and keep `reqs.md` current; when you change `LLMbootstrap.md` itself, you are authoring the artifact (guardrail 1), not obeying it. How the workflow is adopted stays an explicit, user-initiated decision — don't rewire it on your own.

## Imported project files
This repo is governed by its own product, so the workflow and internal docs load here:

@LLMbootstrap.md
@info.md
