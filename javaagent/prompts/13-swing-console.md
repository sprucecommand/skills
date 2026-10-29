# Session 13 — Full separate Swing simulator console and operator workflows

**Execute only this phase in one new Gemini CLI session.** Do not proceed to the next prompt, even if this session finishes early.

## Required startup

1. Read `GEMINI.md`, `GATE_POLICY.md`, `docs/GATE_LOG.md`, `docs/HANDOFF.md`, `docs/DECISIONS.md`, `docs/COVERAGE_MATRIX.md`, and this numbered prompt. Inspect only source and dependencies relevant to this phase. Do not load prior Gemini transcripts.
2. **Prerequisite check:** Read Phase 12 decision and evidence; it must be GO or a user-approved, relevant noncritical BYPASSED entry. If the prerequisite is missing, NO_GO, or supported only by an insufficiently scoped bypass, **stop without implementing this phase** and explain the blocker. Never grant yourself a bypass.
3. Inspect current Git status and preserve unrelated changes. Use real target code, actual dependency versions and observed interfaces. Never invent success evidence.
4. Do not alter target application business logic or implement target UI action recording/playback. The user manually operates the target Swing UI; the separate Swing simulator console is allowed.

## Objective

Make all routing, recording, decoded data, market scenarios and gate results accessible without altering target UI.

**Inputs to use:** IPC versioned protocol, adapters/coverage, service routing, replay APIs and business engine.

## Implement in this session

1. Build accessible separate Swing app with tabs: Agents, Service Routing, Connection/Proxy Explorer, Timeline, Payload Inspector, Recordings, Replay, Instruments/Market Book, Orders/Fills/Positions, Faults, Coverage/Gates, Audit.
2. Allow per-service LIVE_TEST/RECORD/REPLAY/SIMULATE/BLOCK selection with route provenance, consistency dependency warnings and explicit apply/restart requirements.
3. Add capture start/stop, safe capture profile changes, recording selection, strict replay request matching results, virtual clock pause/speed, filtering/search and export.
4. For FIX/SBE show parsed fields and raw/hex view with redaction; for HTTP compare request/response. Display actual proxy and logical target separately.
5. Implement simulator-user controls for quote/book updates, accept/reject/fill/cancel outcomes and fault injection; these change simulator backend state, never create target Swing input events.
6. Use SwingWorker or nonblocking model update paths to keep console EDT responsive; keyboard accessibility and large-volume virtualized tables required.

## Evidence and required deliverables

- standalone console JAR
- console walkthrough
- UI integration tests
- screenshots optional
- `docs/evidence/13.md`: source revision, commands executed, observed pass/fail output, coverage, test limitations, critical hazards, incomplete items, and proposed go/no-go decision.
- Update concise `docs/HANDOFF.md` (less than 1,500 words), `docs/DECISIONS.md` and `docs/COVERAGE_MATRIX.md`.

## Phase 13 gate: GO / NO_GO

Mark **GO only if every required assertion below was actually verified**; otherwise mark **NO_GO**, including when the target or runtime is unavailable. Use fixtures for unit tests and separate real-target integration claims.

- [ ] Two JVMs connect/disconnect and reconnect securely without loss of safe control state.
- [ ] Routes show pending/apply/restart and prevent unsafe hot-switching.
- [ ] No target Swing action recording or driving present.
- [ ] Console remains responsive with large event fixture.
- [ ] Operator can manually force fill and inspect correlated order/market state.

### Test execution

- Determine the project's actual build/test invocation; run clean compile and focused tests covering the above assertions.
- Include at least one positive test, one negative/fail-closed test and a network/secret safety check. Where a test is inapplicable, document proof of inapplicability rather than silently omitting it.
- Note external/untested behavior as BLOCKED, not PASS.
- Capture enough reproducible output in `docs/evidence/13.md` that the human can independently verify the gate.

### Decision and user override — interactive Gemini CLI only

1. Write the full measured results to `docs/evidence/13.md`, including tested revision, commands, pass/fail/blockers, safety concerns, remaining coverage, and specific acceptance assertions.
2. Update `docs/GATE_LOG.md` with **GO** only if every required assertion above has been verified; otherwise **NO_GO**. Record a timestamp, evidence path, and a short reason in the append-only decision history. Mark critical hazards explicitly.
3. Display a concise **PHASE 13 GATE: GO or NO_GO** summary in this interactive conversation. **Never start the next phase in this session.** Even after GO, the user starts the next numbered prompt in a fresh Gemini CLI session.
4. If NO_GO and **no critical safety failure exists**, ask the user here to choose: **FIX**, **BYPASS 13** with explicit scope and reason, or **STOP**. An acceptable response is `BYPASS 13 — scope: <specific omitted requirement>; reason: <rationale>; I accept the limitation.` Do not interpret `continue`, silence, or a general approval as a bypass.
5. Only after receiving that explicit reply in the active conversation, change the gate to **BYPASSED** and record the user's exact approval, date, scope, reason and unresolved tests in `docs/GATE_LOG.md` and `docs/evidence/13.md`. A bypass is **not a PASS**, does not expand tested coverage, and cannot certify an offline release. If the reply requests FIX, remediate/test this phase and present the gate again.
6. For unauthorized live trading, missing egress containment when claiming offline, credentials leakage, unsafe proxy/TLS bypass, fail-open routing, or live/synthetic order-state contamination: mark **CRITICAL NO_GO** and **do not offer a bypass**.

**Phase-specific bypass boundaries:** Optional charts/themes can be deferred. Routing controls, warnings and source/recording provenance are required.

**Stop here** after the interactive gate decision. In final output include changed files, test evidence, observed gate status, remaining gaps and the exact next prompt filename. Do not implement that next phase.
