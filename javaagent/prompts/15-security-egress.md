# Session 15 — Hardening, network containment, performance and privacy audit

**Execute only this phase in one new Gemini CLI session.** Do not proceed to the next prompt, even if this session finishes early.

## Required startup

1. Read `GEMINI.md`, `GATE_POLICY.md`, `docs/GATE_LOG.md`, `docs/HANDOFF.md`, `docs/DECISIONS.md`, `docs/COVERAGE_MATRIX.md`, and this numbered prompt. Inspect only source and dependencies relevant to this phase. Do not load prior Gemini transcripts.
2. **Prerequisite check:** Read Phase 14 decision and evidence; it must be GO or a user-approved, relevant noncritical BYPASSED entry. If the prerequisite is missing, NO_GO, or supported only by an insufficiently scoped bypass, **stop without implementing this phase** and explain the blocker. Never grant yourself a bypass.
3. Inspect current Git status and preserve unrelated changes. Use real target code, actual dependency versions and observed interfaces. Never invent success evidence.
4. Do not alter target application business logic or implement target UI action recording/playback. The user manually operates the target Swing UI; the separate Swing simulator console is allowed.

## Objective

Establish safety assurance that a missed Java hook cannot send real trading activity or leak credentials.

**Inputs to use:** All adapters, policy engine, proxy audit, recording store, user console and launcher.

## Implement in this session

1. Provide documented OS/container/VM external egress deny configuration for target runtime; confirm loopback IPC remains available and DNS/external TCP/UDP are blocked in strict OFFLINE profile.
2. Add external connection watcher/verification for Java and native child processes; audit proxy CONNECT/SOCKS leakage. Do not rely on `ProxySelector` alone.
3. Create simulator-down/unmatched/malformed path tests showing hard failures without fallback to original production endpoints.
4. Complete credential masking, PII redaction, consent/authorization notes, encryption/permission checks, supply-chain and signed dependency inventory.
5. Benchmark agent overhead, queue capacity, dropped-event reporting, backpressure, Swing console EDT safety, GC/memory/CPU under active market-data streams.
6. Check startup failure handling, replay version/schema mismatch, truststore/mTLS constraints without disabling TLS validation, and independent runtime mode attestations.

## Evidence and required deliverables

- `docs/SECURITY_REVIEW.md`
- `docs/EGRESS_TESTS.md`
- performance benchmark results
- negative safety tests
- `docs/evidence/15.md`: source revision, commands executed, observed pass/fail output, coverage, test limitations, critical hazards, incomplete items, and proposed go/no-go decision.
- Update concise `docs/HANDOFF.md` (less than 1,500 words), `docs/DECISIONS.md` and `docs/COVERAGE_MATRIX.md`.

## Phase 15 gate: GO / NO_GO

Mark **GO only if every required assertion below was actually verified**; otherwise mark **NO_GO**, including when the target or runtime is unavailable. Use fixtures for unit tests and separate real-target integration claims.

- [ ] In strict OFFLINE launch, independent egress controls deny all external endpoints even with agent disabled.
- [ ] Attempted real trading writes or proxy/tunnel escapes are blocked and reported.
- [ ] No secret credentials in standard logs, recordings or console exports.
- [ ] Agent failure cannot fail open into live operation.
- [ ] Known performance limits documented and tested under representative load.

### Test execution

- Determine the project's actual build/test invocation; run clean compile and focused tests covering the above assertions.
- Include at least one positive test, one negative/fail-closed test and a network/secret safety check. Where a test is inapplicable, document proof of inapplicability rather than silently omitting it.
- Note external/untested behavior as BLOCKED, not PASS.
- Capture enough reproducible output in `docs/evidence/15.md` that the human can independently verify the gate.

### Decision and user override — interactive Gemini CLI only

1. Write the full measured results to `docs/evidence/15.md`, including tested revision, commands, pass/fail/blockers, safety concerns, remaining coverage, and specific acceptance assertions.
2. Update `docs/GATE_LOG.md` with **GO** only if every required assertion above has been verified; otherwise **NO_GO**. Record a timestamp, evidence path, and a short reason in the append-only decision history. Mark critical hazards explicitly.
3. Display a concise **PHASE 15 GATE: GO or NO_GO** summary in this interactive conversation. **Never start the next phase in this session.** Even after GO, the user starts the next numbered prompt in a fresh Gemini CLI session.
4. If NO_GO and **no critical safety failure exists**, ask the user here to choose: **FIX**, **BYPASS 15** with explicit scope and reason, or **STOP**. An acceptable response is `BYPASS 15 — scope: <specific omitted requirement>; reason: <rationale>; I accept the limitation.` Do not interpret `continue`, silence, or a general approval as a bypass.
5. Only after receiving that explicit reply in the active conversation, change the gate to **BYPASSED** and record the user's exact approval, date, scope, reason and unresolved tests in `docs/GATE_LOG.md` and `docs/evidence/15.md`. A bypass is **not a PASS**, does not expand tested coverage, and cannot certify an offline release. If the reply requests FIX, remediate/test this phase and present the gate again.
6. For unauthorized live trading, missing egress containment when claiming offline, credentials leakage, unsafe proxy/TLS bypass, fail-open routing, or live/synthetic order-state contamination: mark **CRITICAL NO_GO** and **do not offer a bypass**.

**Phase-specific bypass boundaries:** Critical isolation/credential/order-write violations cannot be bypassed. A human may accept noncritical performance optimizations with explicit limited scope.

**Stop here** after the interactive gate decision. In final output include changed files, test evidence, observed gate status, remaining gaps and the exact next prompt filename. Do not implement that next phase.
