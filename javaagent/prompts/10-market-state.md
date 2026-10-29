# Session 10 — Instrument reference, market-state and synthetic feed engine

**Execute only this phase in one new Gemini CLI session.** Do not proceed to the next prompt, even if this session finishes early.

## Required startup

1. Read `GEMINI.md`, `GATE_POLICY.md`, `docs/GATE_LOG.md`, `docs/HANDOFF.md`, `docs/DECISIONS.md`, `docs/COVERAGE_MATRIX.md`, and this numbered prompt. Inspect only source and dependencies relevant to this phase. Do not load prior Gemini transcripts.
2. **Prerequisite check:** Read Phase 09 decision and evidence; it must be GO or a user-approved, relevant noncritical BYPASSED entry. If the prerequisite is missing, NO_GO, or supported only by an insufficiently scoped bypass, **stop without implementing this phase** and explain the blocker. Never grant yourself a bypass.
3. Inspect current Git status and preserve unrelated changes. Use real target code, actual dependency versions and observed interfaces. Never invent success evidence.
4. Do not alter target application business logic or implement target UI action recording/playback. The user manually operates the target Swing UI; the separate Swing simulator console is allowed.

## Objective

Build deterministic current-instrument state from recorded or generated market/reference feeds.

**Inputs to use:** Real discovered instrument reference schemas, market data topics/templates, store and replay clock.

## Implement in this session

1. Define instrument identity and authoritative provenance: symbol, venue, currency, type, tick, lot, status, sessions, price bands, feature flags and account entitlements when supplied.
2. Build replayable snapshot plus incremental market state with bid/ask/depth, timestamps, snapshot epoch, stale timeout, feed gaps and invariants.
3. Support three feed sources: recorded stream, deterministic seeded synthetic book and manual console market controls. Real test market input allowed only under explicit HYBRID route.
4. Define synthetic price model, bounded spread, depth generation, tick alignment, volatility regimes, time controls, auction/halt/disconnect cases and replenishment rules.
5. Prevent undefined reference attributes from silently defaulting to fake tick/lot; report missing mandatory fields and refuse order validation where needed.
6. Produce market snapshots and canonical MarketChanged events consumable by mini matching engine; prohibit simulator from asserting real exchange liquidity from top-of-book quotes alone.

## Evidence and required deliverables

- instrument and market store
- synthetic feed generator
- `snapshot/stream fixture tests`
- market simulation policy docs
- `docs/evidence/10.md`: source revision, commands executed, observed pass/fail output, coverage, test limitations, critical hazards, incomplete items, and proposed go/no-go decision.
- Update concise `docs/HANDOFF.md` (less than 1,500 words), `docs/DECISIONS.md` and `docs/COVERAGE_MATRIX.md`.

## Phase 10 gate: GO / NO_GO

Mark **GO only if every required assertion below was actually verified**; otherwise mark **NO_GO**, including when the target or runtime is unavailable. Use fixtures for unit tests and separate real-target integration claims.

- [ ] Every required instrument field has provenance and missing-data policy.
- [ ] Seeded market simulation reproduces same book and timestamps.
- [ ] Tick/lot and ordinary noncrossed book constraints validated.
- [ ] Market replay resynchronizes correctly on snapshot/gap/disconnect.
- [ ] Top-of-book-only data never silently fabricated as exchange depth.

### Test execution

- Determine the project's actual build/test invocation; run clean compile and focused tests covering the above assertions.
- Include at least one positive test, one negative/fail-closed test and a network/secret safety check. Where a test is inapplicable, document proof of inapplicability rather than silently omitting it.
- Note external/untested behavior as BLOCKED, not PASS.
- Capture enough reproducible output in `docs/evidence/10.md` that the human can independently verify the gate.

### Decision and user override — interactive Gemini CLI only

1. Write the full measured results to `docs/evidence/10.md`, including tested revision, commands, pass/fail/blockers, safety concerns, remaining coverage, and specific acceptance assertions.
2. Update `docs/GATE_LOG.md` with **GO** only if every required assertion above has been verified; otherwise **NO_GO**. Record a timestamp, evidence path, and a short reason in the append-only decision history. Mark critical hazards explicitly.
3. Display a concise **PHASE 10 GATE: GO or NO_GO** summary in this interactive conversation. **Never start the next phase in this session.** Even after GO, the user starts the next numbered prompt in a fresh Gemini CLI session.
4. If NO_GO and **no critical safety failure exists**, ask the user here to choose: **FIX**, **BYPASS 10** with explicit scope and reason, or **STOP**. An acceptable response is `BYPASS 10 — scope: <specific omitted requirement>; reason: <rationale>; I accept the limitation.` Do not interpret `continue`, silence, or a general approval as a bypass.
5. Only after receiving that explicit reply in the active conversation, change the gate to **BYPASSED** and record the user's exact approval, date, scope, reason and unresolved tests in `docs/GATE_LOG.md` and `docs/evidence/10.md`. A bypass is **not a PASS**, does not expand tested coverage, and cannot certify an offline release. If the reply requests FIX, remediate/test this phase and present the gate again.
6. For unauthorized live trading, missing egress containment when claiming offline, credentials leakage, unsafe proxy/TLS bypass, fail-open routing, or live/synthetic order-state contamination: mark **CRITICAL NO_GO** and **do not offer a bypass**.

**Phase-specific bypass boundaries:** Can defer advanced market regimes not used by target client; cannot waive ambiguous tick size/status for accepted orders.

**Stop here** after the interactive gate decision. In final output include changed files, test evidence, observed gate status, remaining gaps and the exact next prompt filename. Do not implement that next phase.
