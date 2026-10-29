# Session 07 — FIX engine capture and session-aware replay

**Execute only this phase in one new Gemini CLI session.** Do not proceed to the next prompt, even if this session finishes early.

## Required startup

1. Read `GEMINI.md`, `GATE_POLICY.md`, `docs/GATE_LOG.md`, `docs/HANDOFF.md`, `docs/DECISIONS.md`, `docs/COVERAGE_MATRIX.md`, and this numbered prompt. Inspect only source and dependencies relevant to this phase. Do not load prior Gemini transcripts.
2. **Prerequisite check:** Read Phase 06 decision and evidence; it must be GO or a user-approved, relevant noncritical BYPASSED entry. If the prerequisite is missing, NO_GO, or supported only by an insufficiently scoped bypass, **stop without implementing this phase** and explain the blocker. Never grant yourself a bypass.
3. Inspect current Git status and preserve unrelated changes. Use real target code, actual dependency versions and observed interfaces. Never invent success evidence.
4. Do not alter target application business logic or implement target UI action recording/playback. The user manually operates the target Swing UI; the separate Swing simulator console is allowed.

## Objective

Support FIX application messages and transport/session lifecycle if FIX is present in the target stack.

**Inputs to use:** Discovered FIX engine, dictionary/version, initiator/acceptor mode, session config, proxy and order/exec mapping.

## Implement in this session

1. Instrument actual engine application/session callbacks (`fromApp`/`toApp`/admin equivalents), preserving raw SOH bytes and parsed tag map where allowed.
2. Identify session IDs, Logon/Logout, sequence numbers, Heartbeat/TestRequest, ResendRequest, reset and persistence policy; build synthetic local session adapter or supported engine facade. If FIX payloads are carried over HTTPS rather than a native FIX session, consume bytes from the HTTP adapter and do not invent a second network connection or session lifecycle.
3. Build session-aware recorded inbound execution/ack/reject injection; outgoing trading requests route to stateful simulator only if actual mapping and environment allow.
4. Sanitize SenderCompID/TargetCompID credentials and password tags; never export auth secrets in FIX recordings.
5. Exercise duplicate/out-of-order sequence handling, session disconnect, heartbeat timeout and application message assertions with a local fixture counterparty.
6. If no FIX dependency/use is found, explicitly label feature NOT_APPLICABLE and provide a standalone fixture module for future onboarding rather than injecting dormant instrumentation.

## Evidence and required deliverables

- adapter-fix
- FIX session fixture
- `parser/session tests`
- mapping report
- `docs/evidence/07.md`: source revision, commands executed, observed pass/fail output, coverage, test limitations, critical hazards, incomplete items, and proposed go/no-go decision.
- Update concise `docs/HANDOFF.md` (less than 1,500 words), `docs/DECISIONS.md` and `docs/COVERAGE_MATRIX.md`.

## Phase 07 gate: GO / NO_GO

Mark **GO only if every required assertion below was actually verified**; otherwise mark **NO_GO**, including when the target or runtime is unavailable. Use fixtures for unit tests and separate real-target integration claims.

- [ ] FIX transport or engine events round-trip with valid session behavior in fixture.
- [ ] An application-level ExecutionReport is not delivered without required session initialization.
- [ ] Unapproved real FIX endpoints are never contacted.
- [ ] Version/session field mapping is derived from code/dictionaries.
- [ ] Target-app FIX coverage measured; NOT_APPLICABLE explicitly stated if unused.

### Test execution

- Determine the project's actual build/test invocation; run clean compile and focused tests covering the above assertions.
- Include at least one positive test, one negative/fail-closed test and a network/secret safety check. Where a test is inapplicable, document proof of inapplicability rather than silently omitting it.
- Note external/untested behavior as BLOCKED, not PASS.
- Capture enough reproducible output in `docs/evidence/07.md` that the human can independently verify the gate.

### Decision and user override — interactive Gemini CLI only

1. Write the full measured results to `docs/evidence/07.md`, including tested revision, commands, pass/fail/blockers, safety concerns, remaining coverage, and specific acceptance assertions.
2. Update `docs/GATE_LOG.md` with **GO** only if every required assertion above has been verified; otherwise **NO_GO**. Record a timestamp, evidence path, and a short reason in the append-only decision history. Mark critical hazards explicitly.
3. Display a concise **PHASE 07 GATE: GO or NO_GO** summary in this interactive conversation. **Never start the next phase in this session.** Even after GO, the user starts the next numbered prompt in a fresh Gemini CLI session.
4. If NO_GO and **no critical safety failure exists**, ask the user here to choose: **FIX**, **BYPASS 07** with explicit scope and reason, or **STOP**. An acceptable response is `BYPASS 07 — scope: <specific omitted requirement>; reason: <rationale>; I accept the limitation.` Do not interpret `continue`, silence, or a general approval as a bypass.
5. Only after receiving that explicit reply in the active conversation, change the gate to **BYPASSED** and record the user's exact approval, date, scope, reason and unresolved tests in `docs/GATE_LOG.md` and `docs/evidence/07.md`. A bypass is **not a PASS**, does not expand tested coverage, and cannot certify an offline release. If the reply requests FIX, remediate/test this phase and present the gate again.
6. For unauthorized live trading, missing egress containment when claiming offline, credentials leakage, unsafe proxy/TLS bypass, fail-open routing, or live/synthetic order-state contamination: mark **CRITICAL NO_GO** and **do not offer a bypass**.

**Phase-specific bypass boundaries:** If no FIX use in target app, a limited bypass may allow next phase while keeping FIX not certified; session safety and no live order guarantees cannot be bypassed.

**Stop here** after the interactive gate decision. In final output include changed files, test evidence, observed gate status, remaining gaps and the exact next prompt filename. Do not implement that next phase.
