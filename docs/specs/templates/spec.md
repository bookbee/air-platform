# NNNN — <Title>

**Status:** Draft · **Area:** `<AREA>` · **Phase:** <n> · **Updated:** <YYYY-MM-DD>
**Derives from:** <HLD §n, LLD §n, Plan §n>
**Supersedes:** —

> Delete every instruction line (`>`) as you fill this in.
> A spec states **what must be true and why**. It contains no implementation detail —
> module names, function signatures and data structures belong in `plan.md`.

## 1. Problem

> What is wrong or missing today, in two or three sentences. Concrete, not aspirational.
> If this is reverse-engineered from shipped code, say so and say what the code already does.

## 2. Outcome

> One paragraph: what is true when this is done that is not true now. Write it so a reviewer
> who reads only this section knows whether the spec succeeded.

## 3. Scope

**In scope**

-

**Out of scope**

-

> Out-of-scope items prevent the review from re-litigating decisions already taken. Name the
> tempting adjacent thing you are deliberately not doing, and why.

## 4. Constitution check

> Every article this spec touches, and how it complies. An article you cannot satisfy is
> either a spec that needs rethinking or an amendment that needs proposing — say which.

| Article | Relevance | Compliance |
| --- | --- | --- |
| <I — The channel comes from the credential> | <how it applies> | <how this complies> |

## 5. Requirements

> One row per requirement. Each is independently falsifiable: if you cannot describe the test
> that would fail, it is not a requirement yet. Prefer observable behaviour over mechanism.
> Ids are permanent — never renumber, never reuse.

| Id | Level | Requirement |
| --- | --- | --- |
| `REQ-<AREA>-001` | MUST | |
| `REQ-<AREA>-002` | MUST NOT | |
| `REQ-<AREA>-003` | SHOULD | |

## 6. Acceptance criteria

> Given/When/Then for the requirements that are not self-evident, especially negative and
> adversarial cases. These become test names, so write them as behaviour.

**AC-1 — <name>** (`REQ-<AREA>-001`)
- **Given** …
- **When** …
- **Then** …

## 7. Failure and degradation

> What happens when a dependency is absent, slow, or wrong. Article III requires an answer
> that narrows rather than a refusal — state which stage degrades and what the client sees.

| Condition | Behaviour | Turn status |
| --- | --- | --- |
| | | |

## 8. Observability

> What must be measurable for this to be operable: metrics, log fields, stage events.
> A requirement that cannot be observed in production cannot be verified in production.

## 9. Open questions

> Numbered, each with an owner and the decision it blocks. Resolve before `Accepted`; move
> the answer into Plan §4 "Decisions taken" when settled.

| # | Question | Blocks | Owner |
| --- | --- | --- | --- |

## 10. Traceability

> Every `REQ-` id from §5, exactly once. Either a real pytest node id, or `GAP` / `DEFERRED`
> with a reason. `make trace` fails on a missing id, a stale test reference, or an
> undeclared gap.

| Id | Proof |
| --- | --- |
| `REQ-<AREA>-001` | `tests/unit/test_<file>.py::test_<name>` |
| `REQ-<AREA>-002` | GAP — <why not tested yet, and what would test it> |
| `REQ-<AREA>-003` | DEFERRED — <which phase, and why it cannot be tested before then> |
