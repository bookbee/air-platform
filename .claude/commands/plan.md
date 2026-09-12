---
description: Write the implementation plan for an accepted spec
argument-hint: <spec number, e.g. 0002>
---

Write `plan.md` for spec **$ARGUMENTS**.

1. Read `docs/specs/constitution.md`, then the spec at `docs/specs/$ARGUMENTS-*/spec.md`.

2. **Check the spec's status.** If it is still `Draft` with open questions in §9 that would
   change what gets built, say so plainly. You may still write a provisional plan that records
   the shape and marks where each answer forks it — that is often how you discover a question
   is load-bearing — but mark the plan blocked and do not proceed to `/tasks`.

3. Read the modules the plan will touch. Real signatures, real types, real call sites.

4. Write `plan.md` from `docs/specs/templates/plan.md`:
   - §2 should name files you expect *not* to change but a reviewer might assume would, and
     why. That is usually where the design insight is — a change that must not reach
     `engine/turn.py` is making a claim about an abstraction holding.
   - §3 must classify contract impact honestly. `constants.py` and `schemas/events.py` are
     closed sets (Article VI): additive is cheap, renaming is breaking. Any route or schema
     change means `make openapi`.
   - §5 records alternatives rejected, including the ones that feel obviously wrong. The next
     person will have the same idea.
   - §6 must keep the suite hermetic. No test may require a running service; a new probe needs
     stubs in *both* directions, per `tests/conftest.py`.

5. Report anything the plan revealed that the spec got wrong, and amend the spec first if so.

Do not write implementation code.
