# Session 06 — Diffusion SDK subscription capture and simulated delivery

**Execute only this phase in one new Gemini CLI session.** Do not proceed to the next prompt, even if this session finishes early.

## Required startup

1. Read `GEMINI.md`, `GATE_POLICY.md`, `docs/GATE_LOG.md`, `docs/HANDOFF.md`, `docs/DECISIONS.md`, `docs/COVERAGE_MATRIX.md`, and this numbered prompt. Inspect only source and dependencies relevant to this phase. Do not load prior Gemini transcripts.
2. **Prerequisite check:** Read Phase 05 decision and evidence; it must be GO or a user-approved, relevant noncritical BYPASSED entry. If the prerequisite is missing, NO_GO, or supported only by an insufficiently scoped bypass, **stop without implementing this phase** and explain the blocker. Never grant yourself a bypass.
3. Inspect current Git status and preserve unrelated changes. Use real target code, actual dependency versions and observed interfaces. Never invent success evidence.
4. Do not alter target application business logic or implement target UI action recording/playback. The user manually operates the target Swing UI; the separate Swing simulator console is allowed.

## Objective

Capture real subscribed market/reference updates and inject them into actual SDK callback paths without a live Diffusion server.

**Inputs to use:** Exact Diffusion SDK version, subscription registration and listener classes discovered in Phase 00.

## Implement in this session

1. Instrument SDK session/subscription registration, listener attachment, topic selectors, initial image/update/removal, session/reconnect states and callback ordering. Avoid guessed API method names.
2. Capture decoded values and delta-reconstructed full values at stable callback boundary, with topic path/types, schema information, timestamps and listener correlations.
3. During REPLAY/SIMULATE, substitute compatible SDK-level session lifecycle or local transport as required so app believes its subscriptions are active; do not merely call a listener before subscription.
4. Handle topic-specific LIVE_TEST/REPLAY/SIMULATE routes where SDK allows safe multiplexing; if not, virtualize whole session and bridge eligible live test updates explicitly.
5. Test listener ordering within a topic, initial snapshot after subscription, remove/unsubscribe, disconnect/reconnect, duplicate notification and rate pressure.
6. Provide safe fake Diffusion integration fixture or SDK mock harness in absence of test server; do not claim successful original-client integration until verified.

## Evidence and required deliverables

- adapter-diffusion
- `topic recording/replay sample`
- Diffusion callback and lifecycle tests
- `docs/evidence/06.md`: source revision, commands executed, observed pass/fail output, coverage, test limitations, critical hazards, incomplete items, and proposed go/no-go decision.
- Update concise `docs/HANDOFF.md` (less than 1,500 words), `docs/DECISIONS.md` and `docs/COVERAGE_MATRIX.md`.

## Phase 06 gate: GO / NO_GO

Mark **GO only if every required assertion below was actually verified**; otherwise mark **NO_GO**, including when the target or runtime is unavailable. Use fixtures for unit tests and separate real-target integration claims.

- [ ] Subscription initialization succeeds offline in test fixture/app path.
- [ ] Decoded topic values and initial snapshots are delivered in correct sequence.
- [ ] No real Diffusion socket or configured proxy used in offline replay.
- [ ] Unsubscribe/reconnect/failure behavior tested.
- [ ] SDK version-specific undocumented gaps explicitly reported.

### Test execution

- Determine the project's actual build/test invocation; run clean compile and focused tests covering the above assertions.
- Include at least one positive test, one negative/fail-closed test and a network/secret safety check. Where a test is inapplicable, document proof of inapplicability rather than silently omitting it.
- Note external/untested behavior as BLOCKED, not PASS.
- Capture enough reproducible output in `docs/evidence/06.md` that the human can independently verify the gate.

### Decision and user override — interactive Gemini CLI only

1. Write the full measured results to `docs/evidence/06.md`, including tested revision, commands, pass/fail/blockers, safety concerns, remaining coverage, and specific acceptance assertions.
2. Update `docs/GATE_LOG.md` with **GO** only if every required assertion above has been verified; otherwise **NO_GO**. Record a timestamp, evidence path, and a short reason in the append-only decision history. Mark critical hazards explicitly.
3. Display a concise **PHASE 06 GATE: GO or NO_GO** summary in this interactive conversation. **Never start the next phase in this session.** Even after GO, the user starts the next numbered prompt in a fresh Gemini CLI session.
4. If NO_GO and **no critical safety failure exists**, ask the user here to choose: **FIX**, **BYPASS 06** with explicit scope and reason, or **STOP**. An acceptable response is `BYPASS 06 — scope: <specific omitted requirement>; reason: <rationale>; I accept the limitation.` Do not interpret `continue`, silence, or a general approval as a bypass.
5. Only after receiving that explicit reply in the active conversation, change the gate to **BYPASSED** and record the user's exact approval, date, scope, reason and unresolved tests in `docs/GATE_LOG.md` and `docs/evidence/06.md`. A bypass is **not a PASS**, does not expand tested coverage, and cannot certify an offline release. If the reply requests FIX, remediate/test this phase and present the gate again.
6. For unauthorized live trading, missing egress containment when claiming offline, credentials leakage, unsafe proxy/TLS bypass, fail-open routing, or live/synthetic order-state contamination: mark **CRITICAL NO_GO** and **do not offer a bypass**.

**Phase-specific bypass boundaries:** Optional topic families can be excluded. Not acceptable to mark all Diffusion-ready if startup requires unmocked session semantics.

**Stop here** after the interactive gate decision. In final output include changed files, test evidence, observed gate status, remaining gaps and the exact next prompt filename. Do not implement that next phase.
