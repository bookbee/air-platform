# 0002 — Tasks

**Spec:** [`spec.md`](spec.md) · **Plan:** [`plan.md`](plan.md) · **Updated:** 2026-09-12
**Status:** Not started — blocked on spec Q1 and Q2

## Order

`T0` gates everything: the spec's open questions change `REQ-SESS-007` and `REQ-SESS-008`.

`T1` (the test double) gates every other test, so it comes before the store. `T2` (codec)
and `T3` (store) are separable. `T6` should land last — turning the backend on before the
outage path exists would ship the silent-history-loss bug in a new form.

## Tasks

### T0 — Resolve spec Q1 and Q2, move the spec to `Accepted`

- **Advances:** `REQ-SESS-007`, `REQ-SESS-008`
- **Touches:** `docs/specs/0002-durable-sessions/spec.md`, `docs/00-plan.md` §4
- **Adds test:** none — this is a decision, not code
- **Done when:** Q1 and Q2 have recorded answers, the decisions are in Plan §4, and the spec
  status is `Accepted`

### T1 — A Redis test double (or adopt `fakeredis`)

- **Advances:** every requirement, by making them testable
- **Touches:** `tests/conftest.py`, possibly `pyproject.toml`
- **Adds test:** `tests/unit/test_redis_double.py::test_getdel_is_atomic_under_concurrency`
- **Done when:** `GET`/`SET`/`GETDEL`/`EXPIRE`/`DEL` behave as documented, the suite stays
  hermetic, and the choice between a double and `fakeredis` is recorded in Plan §6

### T2 — Versioned session codec

- **Advances:** `REQ-SESS-010`
- **Touches:** `src/air_orchestrator_service/memory/codec.py` (new)
- **Adds test:** `tests/unit/test_session_codec.py::test_round_trip_preserves_every_field`,
  `::test_an_unknown_version_is_rejected`
- **Done when:** every `Session.__slots__` field round-trips and an unknown version raises

### T3 — `RedisSessionStore`

- **Advances:** `REQ-SESS-001` · `002` · `003` · `004` · `005`
- **Touches:** `src/air_orchestrator_service/memory/session.py`
- **Adds test:** `tests/unit/test_session_store_redis.py` — survival, two-instance equivalence,
  key shape, indistinguishable absence, TTL refresh
- **Done when:** the store satisfies `SessionStore` under mypy --strict with **no change to the
  protocol**, and `engine/turn.py` is untouched

### T4 — Atomic proposal consumption

- **Advances:** `REQ-SESS-006`, and strengthens `REQ-MUT-006` from [0001](../0001-mutation-gate/spec.md)
- **Touches:** `memory/session.py`, `schemas/session.py`
- **Adds test:** `::test_concurrent_confirmations_consume_one_proposal_once`
- **Done when:** two concurrent confirmations produce exactly one execution, and
  [0001 Q2](../0001-mutation-gate/spec.md) is answered and closed

### T5 — Brokered credentials

- **Advances:** `REQ-SESS-009`, `REQ-SESS-011`
- **Touches:** `clients/infra.py`, `main.py`
- **Adds test:** `tests/unit/test_infra_client.py::test_redis_credentials_come_from_the_broker`,
  `test_session_store_redis.py::test_no_credential_reaches_a_log_line`
- **Done when:** credentials come from air-infra when configured, and a `structlog` capture
  proves none is rendered

### T6 — Wire it up and remove the fallback

- **Advances:** `REQ-SESS-007`, `REQ-SESS-008`, `REQ-SESS-012`
- **Touches:** `memory/session.py` (`build_session_store`), `main.py`, `api/deps.py`,
  `api/v1/system.py`, `config.py`
- **Adds test:** `test_config.py::test_selecting_redis_never_yields_memory`,
  `test_turns.py::test_a_session_backend_outage_degrades_rather_than_fails`,
  `test_system_routes.py::test_ready_reports_the_session_backend_without_gating_on_it`
- **Done when:** the warn-and-fall-back branch is **deleted**, an outage degrades rather than
  fails, and `/v1/ready` reports the backend without gating on it

### T7 — Close out

- **Advances:** all
- **Touches:** `docs/02-lld.md` §15, `docs/00-plan.md` §5, `docs/specs/0002-durable-sessions/spec.md`, `docs/openapi.json`
- **Adds test:** none
- **Done when:** `make check` and `make trace` pass, Phase 2b reads **Shipped**, the spec is
  `Implemented`, and `make openapi` has been run

## Definition of done

- [ ] Every requirement in `spec.md` §5 traces to a passing test, or is a declared `GAP`
- [ ] `make check` passes (ruff · mypy --strict · pytest)
- [ ] `make trace` passes
- [ ] `make openapi` run and `docs/openapi.json` committed
- [ ] Spec status moved to `Implemented`
- [ ] `engine/turn.py` unchanged — if it changed, say why in the PR
- [ ] Exit criterion from Plan §5 Phase 2b met: *two replicas serve alternating turns of one
      conversation indistinguishably*
