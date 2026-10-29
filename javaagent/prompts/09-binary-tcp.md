# Session 09 — Custom framed binary TCP/TLS and native boundary audit

**Execute only this phase in one new Gemini CLI session.** Do not proceed to the next prompt, even if this session finishes early.

## Required startup

1. Read `GEMINI.md`, `GATE_POLICY.md`, `docs/GATE_LOG.md`, `docs/HANDOFF.md`, `docs/DECISIONS.md`, `docs/COVERAGE_MATRIX.md`, and this numbered prompt. Inspect only source and dependencies relevant to this phase. Do not load prior Gemini transcripts.
2. **Prerequisite check:** Read Phase 08 decision and evidence; it must be GO or a user-approved, relevant noncritical BYPASSED entry. If the prerequisite is missing, NO_GO, or supported only by an insufficiently scoped bypass, **stop without implementing this phase** and explain the blocker. Never grant yourself a bypass.
3. Inspect current Git status and preserve unrelated changes. Use real target code, actual dependency versions and observed interfaces. Never invent success evidence.
4. Do not alter target application business logic or implement target UI action recording/playback. The user manually operates the target Swing UI; the separate Swing simulator console is allowed.

## Objective

Capture/replay uncovered non-HTTP raw socket protocols without inventing packet boundaries.

**Inputs to use:** Known TCP/NIO/Netty/JNI connection inventory, framing/codec code, session heartbeat semantics.

## Implement in this session

1. Identify each actual custom binary protocol, socket lifecycle, framing rules, compression, encryption/TLS, push/pull semantics and framing decoder callback.
2. Implement adapters at framed-message/dispatcher boundaries, not generic read(byte[]) calls. Record connection identity, message type, decoded payload where possible, raw bytes and direction.
3. Where app requires successful socket connect/read, implement isolated loopback fixture server or supported channel facade, preserving handshake, timeout and heartbeat semantics.
4. Handle connections multiplexing different service IDs: route only if decoded dispatcher permits; otherwise full connection virtualization or NO_GO.
5. Test packet fragmentation, coalescing, short writes, connection close, reconnect, keepalive, endian, version mismatch and backpressure.
6. Detect any JNI/native socket call that escapes Java instrumentation and report external enforcement strategy rather than claiming all calls can be intercepted.

## Evidence and required deliverables

- adapter-binary
- frame codec tests
- unsupported native path audit
- loopback fixture if necessary
- `docs/evidence/09.md`: source revision, commands executed, observed pass/fail output, coverage, test limitations, critical hazards, incomplete items, and proposed go/no-go decision.
- Update concise `docs/HANDOFF.md` (less than 1,500 words), `docs/DECISIONS.md` and `docs/COVERAGE_MATRIX.md`.

## Phase 09 gate: GO / NO_GO

Mark **GO only if every required assertion below was actually verified**; otherwise mark **NO_GO**, including when the target or runtime is unavailable. Use fixtures for unit tests and separate real-target integration claims.

- [ ] Frames reconstructed correctly under fragmentation/coalescing.
- [ ] Mixed-service connection routing is proven or forbidden.
- [ ] Native/unobserved sockets appear in coverage with NO_GO.
- [ ] Offline test prevents real remote connect with independent egress controls.
- [ ] Disconnect/timeout errors delivered according to application contract.

### Test execution

- Determine the project's actual build/test invocation; run clean compile and focused tests covering the above assertions.
- Include at least one positive test, one negative/fail-closed test and a network/secret safety check. Where a test is inapplicable, document proof of inapplicability rather than silently omitting it.
- Note external/untested behavior as BLOCKED, not PASS.
- Capture enough reproducible output in `docs/evidence/09.md` that the human can independently verify the gate.

### Decision and user override — interactive Gemini CLI only

1. Write the full measured results to `docs/evidence/09.md`, including tested revision, commands, pass/fail/blockers, safety concerns, remaining coverage, and specific acceptance assertions.
2. Update `docs/GATE_LOG.md` with **GO** only if every required assertion above has been verified; otherwise **NO_GO**. Record a timestamp, evidence path, and a short reason in the append-only decision history. Mark critical hazards explicitly.
3. Display a concise **PHASE 09 GATE: GO or NO_GO** summary in this interactive conversation. **Never start the next phase in this session.** Even after GO, the user starts the next numbered prompt in a fresh Gemini CLI session.
4. If NO_GO and **no critical safety failure exists**, ask the user here to choose: **FIX**, **BYPASS 09** with explicit scope and reason, or **STOP**. An acceptable response is `BYPASS 09 — scope: <specific omitted requirement>; reason: <rationale>; I accept the limitation.` Do not interpret `continue`, silence, or a general approval as a bypass.
5. Only after receiving that explicit reply in the active conversation, change the gate to **BYPASSED** and record the user's exact approval, date, scope, reason and unresolved tests in `docs/GATE_LOG.md` and `docs/evidence/09.md`. A bypass is **not a PASS**, does not expand tested coverage, and cannot certify an offline release. If the reply requests FIX, remediate/test this phase and present the gate again.
6. For unauthorized live trading, missing egress containment when claiming offline, credentials leakage, unsafe proxy/TLS bypass, fail-open routing, or live/synthetic order-state contamination: mark **CRITICAL NO_GO** and **do not offer a bypass**.

**Phase-specific bypass boundaries:** Only unused or out-of-scope binary services can be waived; an unidentified order/execution socket is a release blocker.

**Stop here** after the interactive gate decision. In final output include changed files, test evidence, observed gate status, remaining gaps and the exact next prompt filename. Do not implement that next phase.
