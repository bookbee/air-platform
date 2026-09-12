# NNNN — Tasks

**Spec:** [`spec.md`](spec.md) · **Plan:** [`plan.md`](plan.md) · **Updated:** <YYYY-MM-DD>

> Each task is independently reviewable and leaves the tree green — `make check` passes at
> every checkpoint. A task that cannot be reviewed alone is two tasks.
> Every task names the requirement it advances, so a reviewer can ask "does this prove it?"

## Order

> Note any task that must precede another, and why. Unlisted tasks may proceed in parallel.

## Tasks

### T1 — <title>

- **Advances:** `REQ-<AREA>-001`
- **Touches:** `src/…`
- **Adds test:** `tests/unit/test_<file>.py::test_<name>`
- **Done when:** <observable condition, not "code written">

### T2 — <title>

- **Advances:** `REQ-<AREA>-002`
- **Touches:**
- **Adds test:**
- **Done when:**

## Definition of done

- [ ] Every requirement in `spec.md` §5 traces to a passing test, or is a declared `GAP`
- [ ] `make check` passes (ruff · mypy --strict · pytest)
- [ ] `make trace` passes
- [ ] `make openapi` run and `docs/openapi.json` committed, if routes or schemas changed
- [ ] Spec status moved to `Implemented` (or `Partial`, with gaps listed)
- [ ] Module docstrings explain the *why*, per the repo convention
- [ ] `docs/02-lld.md` §15 build status updated if a phase moved
