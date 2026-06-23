This is a project building a bootstrap file for project initialization and restructurization. Don't get confused, "LLMbootstrap.md" is our code, not our guide. We build "LLMbootstrap.md". We might apply the file to this project itself, but during normal operation, this is our patient.


<!-- LLMbootstrap:module=core v=1 START — managed block, edit the source not here -->
## Project conventions (managed by LLMbootstrap)
- This project's workflow is defined by `LLMbootstrap.md`. The blocks between
  `<!-- LLMbootstrap:module=... -->` markers are compiled from it — edit the
  source and re-apply, never hand-edit a managed block.
- Do not modify or restructure `LLMbootstrap.md` as a side effect of routine work.
  Change it only when altering the workflow itself is the explicit task.
- Record only facts explicitly present in the project; don't fabricate. When
  something is implied but ambiguous, raise it as an open question.
<!-- LLMbootstrap:module=core END -->

<!-- LLMbootstrap:module=requirements v=3 START — managed block, edit the source not here -->
## Requirements & tracking
- Goals, requirements, bugs, todos are tracked in `reqs.md` (overview + source of
  truth for status/priority/severity/trace) and `reqs/<ID>.md` detail files.
- IDs are stable and never reused: `G#` goals, `R#` requirements
  (Type F=function / Q=quality / C=constraint), `B#` bugs. Todos are checklist lines.
- Requirements use "shall", are singular and verifiable, and each traces to a goal.
- You may record requirements for already-built behavior (as-built); `Done` still
  means acceptance criteria verified against the code, not merely that code exists.
- An item's tracked status, priority, and severity live ONLY in the `reqs.md`
  tables — the `Done` flag there is authoritative. Detail files (headed
  `# <ID> — <name>`) hold description plus *working* checklists (acceptance
  criteria, fix steps); those checkboxes are progress notes, not the tracked status.
- Give an item its own `reqs/<ID>.md` file only when it needs detail (repro steps,
  acceptance criteria, design notes). Trivial items stay as table rows / checklist lines.
- On add/complete/fix: update `reqs.md`. When implementing a requirement, satisfy
  its acceptance criteria before flipping its `Done` flag. Reference IDs in commits,
  e.g. `fix(B1): ...`.
<!-- LLMbootstrap:module=requirements END -->

<!-- LLMbootstrap:module=readme v=1 START — managed block, edit the source not here -->
## README
- `README.md` is the GitHub-facing technical document: what the project does
  (from the goals in `reqs.md`) and how to use it. Keep it factual, not marketing.
- When the goals (`G#`) or the way the project is run change, update `README.md`.
- Don't document commands, options, or behavior that don't exist.
<!-- LLMbootstrap:module=readme END -->

<!-- LLMbootstrap:module=workflow v=1 START — managed block, edit the source not here -->
## Working loop (reqs-first)
- Before making any change, reconcile it with `reqs.md` FIRST: if it fits the
  existing goals/requirements, proceed; if it is new, add the requirement / todo
  (or goal) first; if it changes one, edit that requirement first. Implement only
  after `reqs.md` reflects the intended change.
- Never work from memory. Goals, requirements, decisions, alignments, and tasks
  live in `reqs.md` / `reqs/`. If it isn't written there, it isn't decided — write
  it down rather than carrying it in your head.
- Keep `reqs.md` current as you go, so clearing the context or starting a new chat
  loses nothing. At the start of a session, read `reqs.md` to recover state before
  acting.
- If a request is ambiguous, out of scope, or conflicts with a goal, surface that
  against `reqs.md` and resolve it there before doing the work.
<!-- LLMbootstrap:module=workflow END -->
