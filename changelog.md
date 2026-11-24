# Changelog

Newest first. One line per notable change; reference IDs.

- 2026-11-24 — Closed B11 without renumbering: changing IDs again would break today's references, so `reqs.md` documents the pre-rewrite range and numbering resumes after it. (B11)
- 2026-11-24 — Closed B12: corrected the first-self-apply entry; of the two open v1 todos dropped in the rewrite, non-file sources is an open question again and "define further workflow modules" is obsolete (modules no longer exist). (B12)
- 2026-11-24 — Fixed B10 (exclusive framing in `reqs/R7.md`) and B13 (stale text in R1, R6, `CLAUDE.md`). (B10, B13)
- 2026-11-24 — Todos live only in `todo.md`; decisions split by kind: what the project shall do in `reqs.md`, how it is built in `info.md`. R3 reworded to match. (B7, B8, R1, R3)
- 2026-11-24 — Fixed B6: the workflow prescribes importing `reqs.md` in `CLAUDE.md` and reading it if not loaded; this repo's `CLAUDE.md` now imports it (verified in a fresh session: not loaded before, loaded after). Added R23. (B6, R23)
- 2026-11-24 — Restored apply safeguards on user decision (open question resolved): confirm step, scan exclusions, read-only code, plan-bound deletion; inventory now persisted in `alignment.md`. R10 widened to seven steps. (R10, R19–R22, B9)
- 2026-11-24 — Investigated the whole project: recorded B6–B13 and four new open questions, extended two existing ones; deleted the untracked `AGENTS.md` (a name-swapped copy of `CLAUDE.md`) on user decision. (B6–B13)
- 2026-07-02 — Ran active alignment on this repo (first run of the R10 procedure): all files conform; relocated three chat-only facts into records (usage mode → info.md, two undecided items → open questions). (R10, R9)
- 2026-07-02 — Added R10: active alignment rewritten as a six-step procedure with a losslessness accounting step. (R10)
- 2026-07-02 — Added R9: open questions get a dedicated `reqs.md` section, recorded instead of guessed at. (R9)
- 2026-07-02 — Added R8 and the `changelog.md` spec (separate on-demand file); created this changelog. (R8)
- 2026-06-24 — Fixed "which hat" guardrail to name the authoring/operating overlap. (B1)
- 2026-06-24 — First self-apply of the rewritten, imported workflow (an earlier managed-block version had been applied on 2026-06-23; corrected 2026-11-24, B12): created `reqs.md`, `reqs/`, `todo.md`, `info.md`, `README.md`, `docs/`; CLAUDE.md now imports the workflow. (R1, R7)
