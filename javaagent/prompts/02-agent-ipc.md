# Session 02 — Agent bootstrap and authenticated localhost control channel

**Execute only this phase in one new Gemini CLI session.** Do not proceed to the next prompt, even if this session finishes early.

## Required startup

1. Read `GEMINI.md`, `GATE_POLICY.md`, `docs/GATE_LOG.md`, `docs/HANDOFF.md`, `docs/DECISIONS.md`, `docs/COVERAGE_MATRIX.md`, and this numbered prompt. Inspect only source and dependencies relevant to this phase. Do not load prior Gemini transcripts.
2. **Prerequisite check:** Read Phase 01 decision and evidence; it must be GO or a user-approved, relevant noncritical BYPASSED entry. If the prerequisite is missing, NO_GO, or supported only by an insufficiently scoped bypass, **stop without implementing this phase** and explain the blocker. Never grant yourself a bypass.
3. Inspect current Git status and preserve unrelated changes. Use real target code, actual dependency versions and observed interfaces. Never invent success evidence.
4. Do not alter target application business logic or implement target UI action recording/playback. The user manually operates the target Swing UI; the separate Swing simulator console is allowed.

## Objective

Load a low-overhead premain agent and communicate safely with a standalone control client.

**Inputs to use:** Actual target startup mechanism/classloaders, Phase 01 schema and gate tests.

## Implement in this session

1. Implement -javaagent premain, manifest, narrow Byte Buddy transformers, configurable adapter activation and no-op DISCOVER mode. Record classloader instrumentation capability without broad bootstrap transformation.
2. Build versioned localhost IPC transport between agent JVM and separate console JVM; authentication (ephemeral token stored with restricted filesystem permissions), capabilities handshake, heartbeats, reconnect, bounded event queue and backpressure.
3. Ensure agent behavior is independent of Swing console presence. If simulator backend unavailable for a SIMULATE route, reject, do not forward.
4. Keep IPC/IO off Swing EDT and socket/market hot paths. Add reentrancy guards and avoid transforming IPC library calls into recursive agent operations.
5. Provide a minimal Swing control application as connectivity proof only, with agent health status, mode, process info and connect/disconnect; full UI comes later.
6. Demonstrate startup/shutdown and attach behavior on a synthetic Swing app and (if authorized) the actual target client.

## Evidence and required deliverables

- Agent JAR with premain
- `local IPC implementation/tests`
- minimal simulator console
- launch scripts
- `docs/evidence/02.md`: source revision, commands executed, observed pass/fail output, coverage, test limitations, critical hazards, incomplete items, and proposed go/no-go decision.
- Update concise `docs/HANDOFF.md` (less than 1,500 words), `docs/DECISIONS.md` and `docs/COVERAGE_MATRIX.md`.

## Phase 02 gate: GO / NO_GO

Mark **GO only if every required assertion below was actually verified**; otherwise mark **NO_GO**, including when the target or runtime is unavailable. Use fixtures for unit tests and separate real-target integration claims.

- [ ] Java agent starts without changing target binaries and reports version + capabilities.
- [ ] Console may disconnect/reconnect with no crash or unintended network egress.
- [ ] Unauthenticated/remote IPC attempts are rejected.
- [ ] EDT responsiveness and bounded queue behavior verified.
- [ ] Replay agent failure never silently reconnects to an unapproved endpoint.

### Test execution

- Determine the project's actual build/test invocation; run clean compile and focused tests covering the above assertions.
- Include at least one positive test, one negative/fail-closed test and a network/secret safety check. Where a test is inapplicable, document proof of inapplicability rather than silently omitting it.
- Note external/untested behavior as BLOCKED, not PASS.
- Capture enough reproducible output in `docs/evidence/02.md` that the human can independently verify the gate.

### Decision and user override — interactive Gemini CLI only

1. Write the full measured results to `docs/evidence/02.md`, including tested revision, commands, pass/fail/blockers, safety concerns, remaining coverage, and specific acceptance assertions.
2. Update `docs/GATE_LOG.md` with **GO** only if every required assertion above has been verified; otherwise **NO_GO**. Record a timestamp, evidence path, and a short reason in the append-only decision history. Mark critical hazards explicitly.
3. Display a concise **PHASE 02 GATE: GO or NO_GO** summary in this interactive conversation. **Never start the next phase in this session.** Even after GO, the user starts the next numbered prompt in a fresh Gemini CLI session.
4. If NO_GO and **no critical safety failure exists**, ask the user here to choose: **FIX**, **BYPASS 02** with explicit scope and reason, or **STOP**. An acceptable response is `BYPASS 02 — scope: <specific omitted requirement>; reason: <rationale>; I accept the limitation.` Do not interpret `continue`, silence, or a general approval as a bypass.
5. Only after receiving that explicit reply in the active conversation, change the gate to **BYPASSED** and record the user's exact approval, date, scope, reason and unresolved tests in `docs/GATE_LOG.md` and `docs/evidence/02.md`. A bypass is **not a PASS**, does not expand tested coverage, and cannot certify an offline release. If the reply requests FIX, remediate/test this phase and present the gate again.
6. For unauthorized live trading, missing egress containment when claiming offline, credentials leakage, unsafe proxy/TLS bypass, fail-open routing, or live/synthetic order-state contamination: mark **CRITICAL NO_GO** and **do not offer a bypass**.

**Phase-specific bypass boundaries:** If actual client startup is unavailable, a fixture-JVM GO is insufficient; report NO_GO and allow human-limited bypass solely for independent adapter development.

**Stop here** after the interactive gate decision. In final output include changed files, test evidence, observed gate status, remaining gaps and the exact next prompt filename. Do not implement that next phase.
