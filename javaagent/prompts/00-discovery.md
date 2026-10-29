# Session 00 — Source + runtime discovery and architecture mapping

**Execute only this phase in one new Gemini CLI session.** Do not proceed to the next prompt, even if this session finishes early.

## Required startup

1. Read `GEMINI.md`, `GATE_POLICY.md`, `docs/GATE_LOG.md`, `docs/HANDOFF.md`, `docs/DECISIONS.md`, `docs/COVERAGE_MATRIX.md`, and this numbered prompt. Inspect only source and dependencies relevant to this phase. Do not load prior Gemini transcripts.
2. **Prerequisite check:** Phase 00 has no prior phase; verify that `docs/GATE_LOG.md` exists and begins with PENDING entries. If the prerequisite is missing, NO_GO, or supported only by an insufficiently scoped bypass, **stop without implementing this phase** and explain the blocker. Never grant yourself a bypass.
3. Inspect current Git status and preserve unrelated changes. Use real target code, actual dependency versions and observed interfaces. Never invent success evidence.
4. Do not alter target application business logic or implement target UI action recording/playback. The user manually operates the target Swing UI; the separate Swing simulator console is allowed.

## Objective

Establish actual communication topology and a verifiable implementation map before introducing Java instrumentation.

**Inputs to use:** Target application source, dependency/JAR inventory, launch scripts, configuration, approved test endpoint documentation, and optional synthetic/nonproduction runtime traces.

## Implement in this session

1. Inspect build system, JVM/OS versions, agent compatibility, launch paths, JPMS, classloaders, dependency versions, encrypted/native/JNI networking and initial application startup services.
2. Classify HTTP(S), Diffusion subscription paths, FIX engine/sessions, SBE generated codecs/schemas, other socket/TLS framing, message dispatchers, and business order/event protocols. Do not assume FIX or SBE exists merely because named in requirements.
3. Map logical services and their upstream/downstream dependencies; annotate instrument/reference, market, orders, executions, positions, authentication, licensing and audit endpoints.
4. Audit JVM/system/library proxy configuration (ProxySelector, PAC, HTTP CONNECT, SOCKS, Netty handlers, SDK/FIX settings), original logical destinations vs proxy endpoints, proxy authentication and TLS/mTLS.
5. Find static source evidence for exact order submission and fill receipt paths; document whether outbound orders are HTTP/FIX/SBE/other and inbound fills are Diffusion/FIX/SBE/HTTP/other.
6. Use controlled runtime tracing only if explicitly authorized nonproduction infrastructure is provided. Enumerate observed connections, open sockets, DNS and child processes. Label all unobserved paths as unknown.
7. Define interceptor candidates with method signatures, supporting source references and fallback strategies for unsupported boundaries; enumerate external egress risks.

## Evidence and required deliverables

- `docs/NETWORK_DISCOVERY.md`
- `docs/PROXY_AUDIT.md`
- `docs/SERVICE_DEPENDENCIES.md`
- `docs/INTERCEPTION_COVERAGE.md`
- `docs/ORDER_EVENT_MAP.md`
- `docs/OFFLINE_READINESS.md`
- `docs/evidence/00.md`: source revision, commands executed, observed pass/fail output, coverage, test limitations, critical hazards, incomplete items, and proposed go/no-go decision.
- Update concise `docs/HANDOFF.md` (less than 1,500 words), `docs/DECISIONS.md` and `docs/COVERAGE_MATRIX.md`.

## Phase 00 gate: GO / NO_GO

Mark **GO only if every required assertion below was actually verified**; otherwise mark **NO_GO**, including when the target or runtime is unavailable. Use fixtures for unit tests and separate real-target integration claims.

- [ ] No invented actual SDK version or interceptor signature; source locations support each claimed mapping.
- [ ] Proxy and endpoint audit differentiates original target, selected proxy and physical socket.
- [ ] Known vs unknown communication paths and startup dependencies explicitly listed.
- [ ] No production connections or outbound trading writes attempted.
- [ ] An actionable first prototype target and credible runtime verification plan exist.

### Test execution

- Determine the project's actual build/test invocation; run clean compile and focused tests covering the above assertions.
- Include at least one positive test, one negative/fail-closed test and a network/secret safety check. Where a test is inapplicable, document proof of inapplicability rather than silently omitting it.
- Note external/untested behavior as BLOCKED, not PASS.
- Capture enough reproducible output in `docs/evidence/00.md` that the human can independently verify the gate.

### Decision and user override — interactive Gemini CLI only

1. Write the full measured results to `docs/evidence/00.md`, including tested revision, commands, pass/fail/blockers, safety concerns, remaining coverage, and specific acceptance assertions.
2. Update `docs/GATE_LOG.md` with **GO** only if every required assertion above has been verified; otherwise **NO_GO**. Record a timestamp, evidence path, and a short reason in the append-only decision history. Mark critical hazards explicitly.
3. Display a concise **PHASE 00 GATE: GO or NO_GO** summary in this interactive conversation. **Never start the next phase in this session.** Even after GO, the user starts the next numbered prompt in a fresh Gemini CLI session.
4. If NO_GO and **no critical safety failure exists**, ask the user here to choose: **FIX**, **BYPASS 00** with explicit scope and reason, or **STOP**. An acceptable response is `BYPASS 00 — scope: <specific omitted requirement>; reason: <rationale>; I accept the limitation.` Do not interpret `continue`, silence, or a general approval as a bypass.
5. Only after receiving that explicit reply in the active conversation, change the gate to **BYPASSED** and record the user's exact approval, date, scope, reason and unresolved tests in `docs/GATE_LOG.md` and `docs/evidence/00.md`. A bypass is **not a PASS**, does not expand tested coverage, and cannot certify an offline release. If the reply requests FIX, remediate/test this phase and present the gate again.
6. For unauthorized live trading, missing egress containment when claiming offline, credentials leakage, unsafe proxy/TLS bypass, fail-open routing, or live/synthetic order-state contamination: mark **CRITICAL NO_GO** and **do not offer a bypass**.

**Phase-specific bypass boundaries:** If a protocol or runtime endpoint cannot be observed because test infrastructure is unavailable, mark that path UNKNOWN and NO_GO; human may scope a bypass for independent foundation work, never certify complete offline coverage.

**Stop here** after the interactive gate decision. In final output include changed files, test evidence, observed gate status, remaining gaps and the exact next prompt filename. Do not implement that next phase.
