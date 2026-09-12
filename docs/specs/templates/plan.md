# NNNN — Implementation plan

**Spec:** [`spec.md`](spec.md) · **Status:** Draft · **Updated:** <YYYY-MM-DD>

> *How*, against this codebase specifically. Written after the spec is `Accepted`, before any
> code. If writing this changes your mind about the spec, change the spec first.

## 1. Approach

> The chosen shape in a paragraph or two, and the one sentence that explains why it beats the
> alternatives in §5.

## 2. Modules touched

| Path | Change | Notes |
| --- | --- | --- |
| `src/air_orchestrator_service/…` | new · modified · deleted | |

> Include files you expect *not* to change but a reviewer might assume would — and say why
> they don't. That is usually where the design insight is.

## 3. Contract impact

> Anything in `constants.py`, `schemas/events.py`, `api/errors.py` or the OpenAPI document.
> Article VI makes these closed sets: additive change is cheap, renaming is breaking.

- [ ] No change to public contracts
- [ ] Additive only (new enum member / new event name / new optional field)
- [ ] Breaking — requires a version discussion before implementing

> Any route or schema change means `make openapi` and a committed `docs/openapi.json`.

## 4. Configuration

| Setting | Default | Why it is configurable |
| --- | --- | --- |

> Per Article VIII, `create_app(settings)` never reads the environment. New settings go in
> `config.py` with validated bounds, and the default must be safe for a fresh checkout —
> optional downstreams default to `enabled=false`.

## 5. Alternatives rejected

| Alternative | Why not |
| --- | --- |

> Record these even when the answer feels obvious. The next person will have the same idea.

## 6. Test strategy

> How each requirement gets proved, and which fixtures do it. Per the repo convention, tests
> must be hermetic: no test may depend on what is running on the developer's machine, and any
> new probe needs stubs in *both* directions.

| Requirement | Test | Fixture / approach |
| --- | --- | --- |

## 7. Rollout

> Migration, backwards compatibility, feature flag, and how to turn it off. If the change
> makes a stubbed stage real, removing its `_STUBBED` entry is part of this (Article V).

## 8. Risks

| Risk | Likelihood | Mitigation |
| --- | --- | --- |
