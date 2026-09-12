---
description: Cut an implementation plan into independently reviewable tasks
argument-hint: <spec number, e.g. 0002>
---

Write `tasks.md` for spec **$ARGUMENTS**.

1. Read the spec and `plan.md` in `docs/specs/$ARGUMENTS-*/`. If the plan is marked blocked on
   an open question, the first task is resolving it — not coding around it.

2. Write `tasks.md` from `docs/specs/templates/tasks.md`. Each task must:
   - **name the requirement it advances** (`REQ-…`), so a reviewer can ask whether it proves it;
   - **name the test it adds**, matching what §10 of the spec already committed to;
   - **leave the tree green** — `make check` passes at every checkpoint. A task that cannot be
     reviewed on its own is two tasks;
   - state "done when" as an *observable condition*, never "code written".

3. Note ordering constraints and why. Anything unlisted may proceed in parallel.

4. The last task is always close-out: update `docs/02-lld.md` §15 build status, run
   `make openapi` if contracts changed, move the spec to `Implemented` (or `Partial` with the
   gaps listed), and confirm `make check` and `make trace` pass.

Do not write implementation code — that is the tasks' job, executed one at a time.
