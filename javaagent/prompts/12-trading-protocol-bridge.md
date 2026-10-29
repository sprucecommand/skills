# Session 12 — Map canonical trading events into actual HTTP/FIX/SBE/Diffusion contracts

**Execute only this phase in one new Gemini CLI session.** Do not proceed to the next prompt, even if this session finishes early.

## Required startup

1. Read `GEMINI.md`, `GATE_POLICY.md`, `docs/GATE_LOG.md`, `docs/HANDOFF.md`, `docs/DECISIONS.md`, `docs/COVERAGE_MATRIX.md`, and this numbered prompt. Inspect only source and dependencies relevant to this phase. Do not load prior Gemini transcripts.
2. **Prerequisite check:** Read Phase 11 decision and evidence; it must be GO or a user-approved, relevant noncritical BYPASSED entry. If the prerequisite is missing, NO_GO, or supported only by an insufficiently scoped bypass, **stop without implementing this phase** and explain the blocker. Never grant yourself a bypass.
3. Inspect current Git status and preserve unrelated changes. Use real target code, actual dependency versions and observed interfaces. Never invent success evidence.
4. Do not alter target application business logic or implement target UI action recording/playback. The user manually operates the target Swing UI; the separate Swing simulator console is allowed.

## Objective

Make original Swing application process simulated business events through its existing communication handlers.

**Inputs to use:** Source-derived order/event protocol map; completed adapters; mini OMS/market engine.

## Implement in this session

1. Implement inbound outbound protocol mapping: captured order command -> canonical order; canonical OrderAccepted/Fill/Cancel/Reject/PositionUpdated -> actual discovered HTTP response, FIX ExecutionReport, SBE template, Diffusion topic update or binary message.
2. Enforce correlation across ClientOrderId, OrderId, ExecId, account, instrument and protocol-specific session headers; maintain ACK-before-fill and protocol-aware ordering.
3. Use existing generated codecs/dictionaries and business API DTO definitions; do not guess FIX version, SBE template IDs, topic names or proprietary return shapes.
4. When OMS/Executions/Positions services are in different routes, enforce consistency groups and simulate snapshots/queries so all affected screens agree.
5. Support missing or unsupported event mapping as a first-class NO_GO; permit user-defined mapping fixtures only with explicit provenance and tests.
6. Demonstrate a manually submitted order in fixture UI generates HTTP/FIX/SBE/Diffusion output as applicable, using test-only state and no live backend.

## Evidence and required deliverables

- protocol translation registry
- contract fixtures
- order-to-fill integration tests
- cross-service consistency report
- `docs/evidence/12.md`: source revision, commands executed, observed pass/fail output, coverage, test limitations, critical hazards, incomplete items, and proposed go/no-go decision.
- Update concise `docs/HANDOFF.md` (less than 1,500 words), `docs/DECISIONS.md` and `docs/COVERAGE_MATRIX.md`.

## Phase 12 gate: GO / NO_GO

Mark **GO only if every required assertion below was actually verified**; otherwise mark **NO_GO**, including when the target or runtime is unavailable. Use fixtures for unit tests and separate real-target integration claims.

- [ ] At least one actual target order->ACK->fill workflow mapped end-to-end or marked NO_GO.
- [ ] Order/Exec/Position identity and quantities remain consistent across channels.
- [ ] No fabricated unknown protocol tags/templates/topics.
- [ ] Correct asynchronous ordering and snapshot behavior.
- [ ] No unexpected external order route during simulated submit.

### Test execution

- Determine the project's actual build/test invocation; run clean compile and focused tests covering the above assertions.
- Include at least one positive test, one negative/fail-closed test and a network/secret safety check. Where a test is inapplicable, document proof of inapplicability rather than silently omitting it.
- Note external/untested behavior as BLOCKED, not PASS.
- Capture enough reproducible output in `docs/evidence/12.md` that the human can independently verify the gate.

### Decision and user override — interactive Gemini CLI only

1. Write the full measured results to `docs/evidence/12.md`, including tested revision, commands, pass/fail/blockers, safety concerns, remaining coverage, and specific acceptance assertions.
2. Update `docs/GATE_LOG.md` with **GO** only if every required assertion above has been verified; otherwise **NO_GO**. Record a timestamp, evidence path, and a short reason in the append-only decision history. Mark critical hazards explicitly.
3. Display a concise **PHASE 12 GATE: GO or NO_GO** summary in this interactive conversation. **Never start the next phase in this session.** Even after GO, the user starts the next numbered prompt in a fresh Gemini CLI session.
4. If NO_GO and **no critical safety failure exists**, ask the user here to choose: **FIX**, **BYPASS 12** with explicit scope and reason, or **STOP**. An acceptable response is `BYPASS 12 — scope: <specific omitted requirement>; reason: <rationale>; I accept the limitation.` Do not interpret `continue`, silence, or a general approval as a bypass.
5. Only after receiving that explicit reply in the active conversation, change the gate to **BYPASSED** and record the user's exact approval, date, scope, reason and unresolved tests in `docs/GATE_LOG.md` and `docs/evidence/12.md`. A bypass is **not a PASS**, does not expand tested coverage, and cannot certify an offline release. If the reply requests FIX, remediate/test this phase and present the gate again.
6. For unauthorized live trading, missing egress containment when claiming offline, credentials leakage, unsafe proxy/TLS bypass, fail-open routing, or live/synthetic order-state contamination: mark **CRITICAL NO_GO** and **do not offer a bypass**.

**Phase-specific bypass boundaries:** Can scope to one order protocol and mark other flows unavailable. No bypass on fake "passes" for unmapped fills or mixing synthetic and live positions.

**Stop here** after the interactive gate decision. In final output include changed files, test evidence, observed gate status, remaining gaps and the exact next prompt filename. Do not implement that next phase.
