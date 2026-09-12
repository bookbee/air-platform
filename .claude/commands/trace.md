---
description: Run the requirement-to-test traceability check and explain any failures
argument-hint: "[spec number, optional]"
---

Run `make trace` and interpret the result$ARGUMENTS.

The check enforces constitution Article X — *a claim is proved by a test, not by assertion*.

**Errors** are broken links and fail the build:
- a requirement naming a test that does not exist — almost always a test renamed without the
  spec being updated. Fix by finding where the test went; if the behaviour is genuinely no
  longer covered, the honest move is to mark it `GAP` and say so, not to delete the requirement.
- a requirement declared in §5 but missing from §10, or traced but never declared.

**Declared gaps** do not fail the build. They are tracked debt. Report them, and for each one
say what it would take to close it — a gap that no one can act on is just a confession.

If any spec has gaps, end by recommending which to close first. Weight by risk, not by count:
an unproved `MUST NOT` on a safety article (I, II or IV) outranks a missing metrics assertion.
