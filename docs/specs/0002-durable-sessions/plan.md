# 0002 — Implementation plan

**Spec:** [`spec.md`](spec.md) · **Status:** Draft — **blocked on spec Q1 and Q2** · **Updated:** 2026-09-12

> Provisional. The spec is still `Draft` with two open questions that change what gets built,
> so this plan records the shape and marks where each answer forks it. It should not be
> executed until the spec is `Accepted`.
>
> Writing a plan against an unresolved spec is allowed and often useful — it is how you
> discover that a question is load-bearing. Writing *code* against one is not.

## 1. Approach

A `RedisSessionStore` implementing the existing `SessionStore` protocol, selected by
`build_session_store`. The protocol does not change, which is the test of whether the Phase 1
abstraction was drawn correctly — if this plan needs a new method, that abstraction was wrong.

The one genuine design decision is **where the pending proposal lives**. Holding it inside the
serialised session makes `REQ-SESS-006` (atomic consumption) a read-modify-write across the
whole payload; splitting it into its own key makes consumption a single `GETDEL`. See §5.

## 2. Modules touched

| Path | Change | Notes |
| --- | --- | --- |
| `memory/session.py` | modified | Add `RedisSessionStore`; `build_session_store` returns it and stops falling back |
| `memory/codec.py` | new | Versioned serialisation of `Session` (`REQ-SESS-010`) — kept separate so the wire format is reviewable on its own |
| `clients/infra.py` | modified | Fetch Redis credentials from the broker (`REQ-SESS-009`) |
| `config.py` | modified | Possibly empty the `redis_url` default — see spec Q2 |
| `api/deps.py` | modified | `AppState.sessions` becomes the protocol type, not the concrete class |
| `main.py` | modified | Construct the client; close the pool on lifespan shutdown |
| `api/v1/system.py` | modified | Report the backend on `/v1/ready` without gating (`REQ-SESS-012`) |
| `engine/turn.py` | **unchanged** | It depends on the protocol and must not learn that a backend exists. If this file changes, the abstraction leaked |

## 3. Contract impact

- [x] **Additive only** — `/v1/ready` gains a dependency entry. `readiness` already carries a
      list, so this is a new element, not a new field.
- [ ] No change to `constants.py`, `schemas/events.py` or the error shape.

`make openapi` required: the readiness response gains an entry.

## 4. Configuration

| Setting | Default | Why it is configurable |
| --- | --- | --- |
| `session.backend` | `memory` | Exists; a fresh checkout must not need Redis |
| `session.redis_url` | `redis://localhost:6379/1` → **possibly `""`** | Spec Q2: the current default makes `_guard_redis_backends` unreachable |
| `session.redis_timeout_s` | `0.5` | New. Must be well inside the turn deadline so a slow Redis degrades rather than consuming the turn's budget |
| `session.fail_open` | `true` | New. Encodes spec Q1's answer — `true` degrades, `false` refuses. Configurable because the right answer may differ per channel |

## 5. Alternatives rejected

| Alternative | Why not |
| --- | --- |
| Keep the proposal inside the session payload | Makes `REQ-SESS-006` a read-modify-write; two concurrent confirmations can both read a proposal before either writes back. Would need `WATCH`/`MULTI` or Lua — more machinery than a second key |
| Separate key per proposal, consumed with `GETDEL` | **Provisionally chosen.** Atomic in one command. Cost: two keys to expire and a possible orphan if the session is deleted first — acceptable, since an orphaned proposal is unreachable without its session |
| Write-through cache (memory in front of Redis) | Reintroduces the exact bug being fixed: a replica would serve stale history and look correct |
| A new `SessionStore.take_pending_atomic` method | The protocol should not grow a method whose name admits the other one is not atomic. Fix the semantics, not the name |
| `redis-py` async vs a new dependency | `redis>=5.2` is already an extra in `pyproject.toml` with `types-redis` in dev. No new dependency |

## 6. Test strategy

Hermetic, per the repo convention — **no test may require a running Redis.** `fakeredis` is the
obvious candidate but is a new dependency; the alternative is a small in-process double that
implements the handful of commands used (`GET`, `SET`, `GETDEL`, `EXPIRE`, `DEL`).

> Decide this in tasks T1. A double is more work but keeps the dependency list honest; the
> commands used are few enough that it is plausible.

| Requirement | Test | Approach |
| --- | --- | --- |
| `REQ-SESS-001` · `002` | `test_session_store_redis.py` | Two store instances over one backing double |
| `REQ-SESS-006` | `test_concurrent_confirmations_consume_one_proposal_once` | `asyncio.gather` of two confirmations |
| `REQ-SESS-007` | `test_turns.py` | A double that raises `ConnectionError`, asserting 200 + `degraded` |
| `REQ-SESS-008` | `test_config.py` | Assert `build_session_store` raises rather than warns |
| `REQ-SESS-011` | `test_session_store_redis.py` | `structlog` capture, asserting the credential never appears |

## 7. Rollout

`backend` stays `memory` by default, so the deploy is inert until a deployment opts in.
No migration: in-memory sessions are already lost on restart.

Reverting is a config change back to `memory` — which is exactly why `REQ-SESS-008` matters.
The escape hatch must be deliberate, not accidental.

## 8. Risks

| Risk | Likelihood | Mitigation |
| --- | --- | --- |
| The in-process double diverges from real Redis semantics, especially `GETDEL` atomicity | Medium | Keep the double tiny; one integration test against real Redis in `make up`, outside the hermetic suite |
| Spec Q1 answered "fail closed" after implementation | Low | `session.fail_open` makes it a setting, so the answer changes config rather than code |
| Serialisation drifts from the `Session` slots | Medium | `codec.py` round-trip test over every field; `Session.__slots__` makes an omission detectable |
| Turn deadline consumed by a slow Redis | Medium | `redis_timeout_s` bounded well inside the deadline; treat a timeout as unreachable |
