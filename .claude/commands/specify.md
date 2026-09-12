---
description: Draft a new spec from the template, checked against the constitution
argument-hint: <what the feature must do>
---

Draft a new specification for: **$ARGUMENTS**

Follow this order and do not skip ahead to implementation.

1. **Read the rules first.** `docs/specs/constitution.md`, then `docs/specs/README.md` for the
   conventions. Then skim `docs/00-plan.md` §2 (goals G1–G13) and §5 (phasing) to find where
   this work belongs.

2. **Find the ground truth.** Read the code and tests this spec would govern *before* writing
   a word of it. A spec written from assumptions about the codebase is worse than no spec. If
   this reverse-engineers shipped behaviour, the spec must describe what the code actually
   does, including where that differs from what its docstrings claim.

3. **Scaffold it.** `make spec NAME=<slug>` — it allocates the next number and copies the
   template. Never renumber an existing spec.

4. **Write the spec**, filling every section of the template:
   - §5 requirements must be *independently falsifiable*. If you cannot describe the test that
     would fail, it is not a requirement — it is a wish. Prefer observable behaviour over
     mechanism, and state negative requirements (`MUST NOT`) explicitly, since those are the
     ones that get refactored away by accident.
   - §4 must check every constitution article the spec touches. An article you cannot satisfy
     means either the spec is wrong or the constitution needs amending — say which, do not
     quietly proceed.
   - §10 must trace every `REQ-` id to a real pytest node id, or to `GAP`/`DEFERRED` with a
     reason. For a forward spec, name the test you *intend* to write: deciding test names
     before code is the practice, not an afterthought.
   - §9 open questions get an owner and the decision each one blocks. If a question would
     change what gets built, say so — the spec cannot be `Accepted` until it is resolved.

5. **Verify.** Run `make trace`. It fails on a requirement that names a test which does not
   exist, and on one that is untraced.

6. **Report** the requirement count, the gaps, and anything you found where the implementation
   and its own documentation disagree. Those findings are the most valuable output of the
   exercise — surface them prominently rather than burying them in the table.

Leave the status as `Draft`. Do not write implementation code; that is `/plan` then `/tasks`.
