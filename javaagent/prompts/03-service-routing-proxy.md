# Session 03 — Service registry, selective routing and proxy-aware discovery

**Execute only this phase in one new Gemini CLI session.** Do not proceed to the next prompt, even if this session finishes early.

## Required startup

1. Read `GEMINI.md`, `GATE_POLICY.md`, `docs/GATE_LOG.md`, `docs/HANDOFF.md`, `docs/DECISIONS.md`, `docs/COVERAGE_MATRIX.md`, and this numbered prompt. Inspect only source and dependencies relevant to this phase. Do not load prior Gemini transcripts.
2. **Prerequisite check:** Read Phase 02 decision and evidence; it must be GO or a user-approved, relevant noncritical BYPASSED entry. If the prerequisite is missing, NO_GO, or supported only by an insufficiently scoped bypass, **stop without implementing this phase** and explain the blocker. Never grant yourself a bypass.
3. Inspect current Git status and preserve unrelated changes. Use real target code, actual dependency versions and observed interfaces. Never invent success evidence.
4. Do not alter target application business logic or implement target UI action recording/playback. The user manually operates the target Swing UI; the separate Swing simulator console is allowed.

## Objective

Make routing decisions at logical service boundaries while preserving approved live test connectivity and blocking unapproved paths.

**Inputs to use:** Phase 00 proxy inventory and service map; Phase 02 IPC.

## Implement in this session

1. Implement service catalog persisted in config with stable logical service IDs, path/topic/template matchers, provenance and consistency groups. Support LIVE_TEST, RECORD, REPLAY, SIMULATE and BLOCK.
2. Define match precedence and conflict checks; reject overlapping ambiguous rules and enforce BLOCK as strict-mode default. Dynamic changes require quiescent/reset point when session-level transports exist.
3. Observe original logical target, selected proxy and real socket separately. Support ProxySelector, system JVM properties, discovered library overrides, CONNECT/SOCKS and Netty only where actual code requires.
4. Distinguish global OFFLINE (independent external egress deny) from HYBRID (only explicit nonproduction allowlist); never call HYBRID offline.
5. Provide routing decision audit and service connection explorer data; detect unsupported physical-socket-only paths and mark them uncovered rather than intercepting blindly.
6. Build rules to prohibit write-capable trading endpoints from LIVE_TEST/RECORD unless explicitly approved, verifiably nonproduction and isolated; prohibit fail-open fallback.
7. Test proxy-decorated traffic: order requests through proxy must match original orders destination; simulated orders must never open tunnel or proxy connection.

## Evidence and required deliverables

- service-router registry implementation
- proxy observability hooks
- routing config and docs
- policy tests
- `docs/evidence/03.md`: source revision, commands executed, observed pass/fail output, coverage, test limitations, critical hazards, incomplete items, and proposed go/no-go decision.
- Update concise `docs/HANDOFF.md` (less than 1,500 words), `docs/DECISIONS.md` and `docs/COVERAGE_MATRIX.md`.

## Phase 03 gate: GO / NO_GO

Mark **GO only if every required assertion below was actually verified**; otherwise mark **NO_GO**, including when the target or runtime is unavailable. Use fixtures for unit tests and separate real-target integration claims.

- [ ] Original destination correctly distinguished from proxy transport destination.
- [ ] Specific service can be simulated while unrelated approved test service stays live.
- [ ] Unmatched, ambiguous and simulator-down calls fail closed.
- [ ] Write-capable services cannot silently route to a live/production endpoint.
- [ ] Strict OFFLINE mode enforces blocker contract independently of Java interceptor claims.

### Test execution

- Determine the project's actual build/test invocation; run clean compile and focused tests covering the above assertions.
- Include at least one positive test, one negative/fail-closed test and a network/secret safety check. Where a test is inapplicable, document proof of inapplicability rather than silently omitting it.
- Note external/untested behavior as BLOCKED, not PASS.
- Capture enough reproducible output in `docs/evidence/03.md` that the human can independently verify the gate.

### Decision and user override — interactive Gemini CLI only

1. Write the full measured results to `docs/evidence/03.md`, including tested revision, commands, pass/fail/blockers, safety concerns, remaining coverage, and specific acceptance assertions.
2. Update `docs/GATE_LOG.md` with **GO** only if every required assertion above has been verified; otherwise **NO_GO**. Record a timestamp, evidence path, and a short reason in the append-only decision history. Mark critical hazards explicitly.
3. Display a concise **PHASE 03 GATE: GO or NO_GO** summary in this interactive conversation. **Never start the next phase in this session.** Even after GO, the user starts the next numbered prompt in a fresh Gemini CLI session.
4. If NO_GO and **no critical safety failure exists**, ask the user here to choose: **FIX**, **BYPASS 03** with explicit scope and reason, or **STOP**. An acceptable response is `BYPASS 03 — scope: <specific omitted requirement>; reason: <rationale>; I accept the limitation.` Do not interpret `continue`, silence, or a general approval as a bypass.
5. Only after receiving that explicit reply in the active conversation, change the gate to **BYPASSED** and record the user's exact approval, date, scope, reason and unresolved tests in `docs/GATE_LOG.md` and `docs/evidence/03.md`. A bypass is **not a PASS**, does not expand tested coverage, and cannot certify an offline release. If the reply requests FIX, remediate/test this phase and present the gate again.
6. For unauthorized live trading, missing egress containment when claiming offline, credentials leakage, unsafe proxy/TLS bypass, fail-open routing, or live/synthetic order-state contamination: mark **CRITICAL NO_GO** and **do not offer a bypass**.

**Phase-specific bypass boundaries:** An optional exotic proxy implementation may be bypassed for a target not using it. No bypass if any live write or fail-open risk remains; report as critical.

**Stop here** after the interactive gate decision. In final output include changed files, test evidence, observed gate status, remaining gaps and the exact next prompt filename. Do not implement that next phase.
