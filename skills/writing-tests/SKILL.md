---
name: writing-tests
description: Plan and write tests (unit, smoke, integration) for user-specified code. Use when asked to write, add, plan, or expand tests—especially for critical or risky code (async, DB, APIs, permissions, side effects). Runs a gated two-phase flow: PLAN first (ask questions, get approval), then IMPLEMENT one step at a time with a Testing Report and human verification after each turn.
---

# Writing Tests

Act as a test architect first, code generator second. Build a reliable safety
net, not just many tests.

This skill runs in **two gated phases**. Do not write test code during Phase 1.
Do not skip ahead.

## Phase 0: Read the code

Read the exact code the user pointed at, plus what it directly touches:
callers, dependencies, data shapes, existing tests, and the test setup/config
already in the repo (framework, runner, fixtures, mock style).

- If the user named files/functions, read those first.
- If scope is vague, ask what to cover before reading half the repo.
- Reuse the repo's existing test conventions. Do not introduce a new framework
  or mocking style when one already exists.

## Phase 1: Plan (no test code yet)

Analyze behavior, then produce a plan of **small, individually executable
steps**. Stay focused: unit testing goes module by module, function by
function. Do not bundle unrelated units into one step.

Produce this, then STOP and wait for approval:

```text
## Behavior map
- Contract: <what it promises>
- Inputs / Outputs:
- Side effects:
- Dependencies (to mock vs. use real):
- Decision points / branches:
- Failure modes:
- Invariants:
- Ambiguities:

## Decision table
| Condition | Behavior | Result | Side effects | Priority |
|-----------|----------|--------|--------------|----------|
Prioritize risks: data loss, silent failure, permission leak, bad state.

## Test plan (ordered, small steps)
Each step = one unit/module or one cohesive layer, independently implementable.
| # | Target (module/fn) | Layer | Cases to cover | Mocks | Priority |
|---|--------------------|-------|----------------|-------|----------|
Layers: unit → smoke → integration.

## Mocks
What gets mocked (external systems: API, DB, time, queues) and what stays real
(the code under test, core logic).

## Questions
Only genuine blockers (rollback? idempotency? partial success allowed?
expected error behavior?). If none, say "None."
```

Do not proceed to Phase 2 until the human approves the plan (and answers any
questions).

## Phase 2: Implement (one step per turn)

Implement **exactly one plan step per turn**, then hand back to the human to
verify before the next step.

Each turn:

1. Implement only the current step's tests.
2. Run them; fix until green (or report a real blocker).
3. Emit the **Testing Report** below.
4. Stop. Wait for the human to verify before starting the next step.

### Testing Report (required at the end of every implementation turn)

```markdown
## Testing Report — Step <#>: <target>

### Cases covered
- <case> — <why it matters>

### Tests → cases
| Test name | Case(s) covered |
|-----------|-----------------|
| test_missing_user_id_raises_validation_error | invalid input / no side effects |

### Result
- Ran: <command> — <pass/fail counts>

### What the human reviewer should look for
- <specific thing to eyeball: correct assertion target, realistic data,
  mock not hiding a real bug, missing edge case, invariant actually checked>

### Not yet covered (next steps)
- <deferred cases / remaining plan steps>
```

## Test generation rules

- Small batches, one behavior per test, descriptive names.
- Test public behavior, not implementation details.
- Realistic data; minimal mocking.
- Always include failure cases + a regression test when fixing a bug.

**Naming** — `test_missing_user_id_raises_validation_error`, not `test_1` /
`test_success`.

**Mock** external systems (API, DB, time, queues). **Never mock** the function
under test or core logic.

## Layers

- **Unit**: logic, validation, branches. Mock external deps only.
- **Smoke**: the thing runs end-to-end on the happy path without crashing —
  cheap, fast, wide.
- **Integration**: DB, APIs, transactions, serialization. Real dependencies
  where practical.
- **Property-based** (only if it earns its keep): parsers, merges, wide input
  spaces.

## Critical checks (for risky code)

DB state on success AND failure • rollback • events/jobs fired • permissions •
idempotency • no side effects on invalid input • retries bounded.

## Failure & regression

Cover invalid/missing input, dependency failure, DB errors, retries, partial
failures. Decide expected behavior (raise / retry / rollback) before asserting.

Regression test format: `Given <bug> / Before <wrong> / Expected <correct> /
Test <code>`.

## Characterization (large/unclear code)

Before refactoring unclear code, capture current behavior as tests first, then
refactor small pieces. Only refactor after tests exist.

## Language notes

- **Python**: pytest, AsyncMock, fixtures. No `sleep`, no huge inline data.
- **TypeScript**: describe/it, typed mocks. Avoid snapshots and internal
  assertions.
- **Rust**: `#[test]`, table tests, proptest. Avoid excessive `unwrap`.

## Anti-patterns

Mock-only tests • testing implementation details • brittle tests • giant
snapshots • unclear names • over-mocking • chasing coverage without value.

## Final principle

Simple code: a couple of examples may suffice. Critical code: test behavior,
risks, invariants, side effects, and failures. Plan first, gate on human
approval, implement one small step at a time, report after each.
