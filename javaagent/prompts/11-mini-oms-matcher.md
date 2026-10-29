# Session 11 — Stateful mini OMS, matching, executions and positions

**Execute only this phase in one new Gemini CLI session.** Do not proceed to the next prompt, even if this session finishes early.

## Required startup

1. Read `GEMINI.md`, `GATE_POLICY.md`, `docs/GATE_LOG.md`, `docs/HANDOFF.md`, `docs/DECISIONS.md`, `docs/COVERAGE_MATRIX.md`, and this numbered prompt. Inspect only source and dependencies relevant to this phase. Do not load prior Gemini transcripts.
2. **Prerequisite check:** Read Phase 10 decision and evidence; it must be GO or a user-approved, relevant noncritical BYPASSED entry. If the prerequisite is missing, NO_GO, or supported only by an insufficiently scoped bypass, **stop without implementing this phase** and explain the blocker. Never grant yourself a bypass.
3. Inspect current Git status and preserve unrelated changes. Use real target code, actual dependency versions and observed interfaces. Never invent success evidence.
4. Do not alter target application business logic or implement target UI action recording/playback. The user manually operates the target Swing UI; the separate Swing simulator console is allowed.

## Objective

Accept new, previously unseen orders initiated manually through the original Swing app and simulate realistic outcomes.

**Inputs to use:** Actual outbound order contract and inbound ack/fill/position mapping, market state engine and consistency groups.

## Implement in this session

1. Implement order command validation: instrument tradability, price/quantity tick and lot, account permissions/limits available in fixtures; refuse unsupported types/time-in-force rather than guessing.
2. Maintain canonical event-sourced states: pending, accepted, rejected, working, partially filled, filled, cancel pending/canceled, replace pending/replaced, expired; require coherent and legal transitions.
3. Implement deterministic mini matcher: market/marketable limit crossing at eligible price levels, price-time priority for simulated resting orders, partial fills, conservation/depletion of synthetic displayed liquidity and explicit replenishment policy.
4. Implement manual user-driven controls in *simulator console* to force a fill, partial, reject, cancel, replace response, timeout or disconnect; no target Swing driving.
5. Update synthetic executions and positions using consistent identifiers, decimal precision and timestamps; implement query/snapshot API for blotter refresh.
6. Inject configurable latency and errors using seeded clock; journal all outgoing simulated business events for protocol adapters. No live trade routing.

## Evidence and required deliverables

- sim-orders mini engine
- order state transition tests
- execution and position tests
- fill policy documentation
- `docs/evidence/11.md`: source revision, commands executed, observed pass/fail output, coverage, test limitations, critical hazards, incomplete items, and proposed go/no-go decision.
- Update concise `docs/HANDOFF.md` (less than 1,500 words), `docs/DECISIONS.md` and `docs/COVERAGE_MATRIX.md`.

## Phase 11 gate: GO / NO_GO

Mark **GO only if every required assertion below was actually verified**; otherwise mark **NO_GO**, including when the target or runtime is unavailable. Use fixtures for unit tests and separate real-target integration claims.

- [ ] New order not present in recordings produces deterministic ACK and permitted fills.
- [ ] Quantity conservation: cumQty + leavesQty = acceptedQty; never overfill.
- [ ] Partial fill, reject, cancel, replace, duplicate IDs and market not-crossing cases validated.
- [ ] Simulated execution/position data stays isolated from live services.
- [ ] All write-capable endpoints remain blocked from unapproved network paths.

### Test execution

- Determine the project's actual build/test invocation; run clean compile and focused tests covering the above assertions.
- Include at least one positive test, one negative/fail-closed test and a network/secret safety check. Where a test is inapplicable, document proof of inapplicability rather than silently omitting it.
- Note external/untested behavior as BLOCKED, not PASS.
- Capture enough reproducible output in `docs/evidence/11.md` that the human can independently verify the gate.

### Decision and user override — interactive Gemini CLI only

1. Write the full measured results to `docs/evidence/11.md`, including tested revision, commands, pass/fail/blockers, safety concerns, remaining coverage, and specific acceptance assertions.
2. Update `docs/GATE_LOG.md` with **GO** only if every required assertion above has been verified; otherwise **NO_GO**. Record a timestamp, evidence path, and a short reason in the append-only decision history. Mark critical hazards explicitly.
3. Display a concise **PHASE 11 GATE: GO or NO_GO** summary in this interactive conversation. **Never start the next phase in this session.** Even after GO, the user starts the next numbered prompt in a fresh Gemini CLI session.
4. If NO_GO and **no critical safety failure exists**, ask the user here to choose: **FIX**, **BYPASS 11** with explicit scope and reason, or **STOP**. An acceptable response is `BYPASS 11 — scope: <specific omitted requirement>; reason: <rationale>; I accept the limitation.` Do not interpret `continue`, silence, or a general approval as a bypass.
5. Only after receiving that explicit reply in the active conversation, change the gate to **BYPASSED** and record the user's exact approval, date, scope, reason and unresolved tests in `docs/GATE_LOG.md` and `docs/evidence/11.md`. A bypass is **not a PASS**, does not expand tested coverage, and cannot certify an offline release. If the reply requests FIX, remediate/test this phase and present the gate again.
6. For unauthorized live trading, missing egress containment when claiming offline, credentials leakage, unsafe proxy/TLS bypass, fail-open routing, or live/synthetic order-state contamination: mark **CRITICAL NO_GO** and **do not offer a bypass**.

**Phase-specific bypass boundaries:** May limit v1 order types to explicitly supported market and limit. No bypass of order conservation, real-trade isolation or identity consistency.

**Stop here** after the interactive gate decision. In final output include changed files, test evidence, observed gate status, remaining gaps and the exact next prompt filename. Do not implement that next phase.
