# Session 01 — Multi-module foundation, contracts and Markdown gate records

**Execute only this phase in one new Gemini CLI session.** Do not proceed to the next prompt, even if this session finishes early.

## Required startup

1. Read `GEMINI.md`, `GATE_POLICY.md`, `docs/GATE_LOG.md`, `docs/HANDOFF.md`, `docs/DECISIONS.md`, `docs/COVERAGE_MATRIX.md`, and this numbered prompt. Inspect only source and dependencies relevant to this phase. Do not load prior Gemini transcripts.
2. **Prerequisite check:** Read Phase 00 decision and evidence; it must be GO or a user-approved, relevant noncritical BYPASSED entry. If the prerequisite is missing, NO_GO, or supported only by an insufficiently scoped bypass, **stop without implementing this phase** and explain the blocker. Never grant yourself a bypass.
3. Inspect current Git status and preserve unrelated changes. Use real target code, actual dependency versions and observed interfaces. Never invent success evidence.
4. Do not alter target application business logic or implement target UI action recording/playback. The user manually operates the target Swing UI; the separate Swing simulator console is allowed.

## Objective

Produce compileable Java modules and immutable core models that all later adapters share.

**Inputs to use:** Phase 00 inventory and design decisions; Java toolchain/build system actually present.

## Implement in this session

1. Create Gradle/Maven multi-module skeleton: agent-bootstrap, agent-runtime, adapter-spi, protocol adapters, service-router, event-store, replay-core, sim-market, sim-orders, console-swing, sim-cli, ipc-contract and integration-fixtures. Omit unused modules only with explanation.
2. Define versioned ProtocolAdapter SPI: detection/coverage, capture, capability advertisement, stateful replay hook. Do not assume universal injectMessage(byte[]) semantics.
3. Define typed immutable event envelope (event/session/connection/service/correlation IDs, timestamp, direction, protocol, logical endpoint, proxy info, payload hashes, redaction, decode meta). Use binary-safe storage references.
4. Provide typed service policy and state/event schemas; validate configuration strictly; include schema version migration strategy and deterministic clock/ID interfaces.
5. Establish the **Markdown-only** gate workflow (`docs/GATE_LOG.md`, stage evidence and interactive user decisions). Define and test a review convention: a missing/NO_GO preceding decision blocks the next phase; noncritical BYPASSED entries keep scope, rationale and missing coverage visible; critical failures never qualify for a bypass. Do not create a gate-management CLI or scripts.
6. Implement integration test fixtures with no actual trading endpoints and a CI command that builds from clean checkout.

## Evidence and required deliverables

- `pom.xml or settings.gradle`
- adapter-spi and contract modules
- schema and gate-policy evidence/examples (Markdown only for gate tracking)
- `docs/BUILD.md`
- `docs/evidence/01.md`: source revision, commands executed, observed pass/fail output, coverage, test limitations, critical hazards, incomplete items, and proposed go/no-go decision.
- Update concise `docs/HANDOFF.md` (less than 1,500 words), `docs/DECISIONS.md` and `docs/COVERAGE_MATRIX.md`.

## Phase 01 gate: GO / NO_GO

Mark **GO only if every required assertion below was actually verified**; otherwise mark **NO_GO**, including when the target or runtime is unavailable. Use fixtures for unit tests and separate real-target integration claims.

- [ ] Clean build and unit tests pass on target-compatible Java version.
- [ ] No target application changes required for base build.
- [ ] The documented Gemini session startup check blocks downstream phases when the prior gate is PENDING/NO_GO, absent an explicitly approved relevant noncritical bypass.
- [ ] Versioned schema round-trip including binary payload refs and unknown fields passes.
- [ ] No hardcoded credentials or production endpoints.

### Test execution

- Determine the project's actual build/test invocation; run clean compile and focused tests covering the above assertions.
- Include at least one positive test, one negative/fail-closed test and a network/secret safety check. Where a test is inapplicable, document proof of inapplicability rather than silently omitting it.
- Note external/untested behavior as BLOCKED, not PASS.
- Capture enough reproducible output in `docs/evidence/01.md` that the human can independently verify the gate.

### Decision and user override — interactive Gemini CLI only

1. Write the full measured results to `docs/evidence/01.md`, including tested revision, commands, pass/fail/blockers, safety concerns, remaining coverage, and specific acceptance assertions.
2. Update `docs/GATE_LOG.md` with **GO** only if every required assertion above has been verified; otherwise **NO_GO**. Record a timestamp, evidence path, and a short reason in the append-only decision history. Mark critical hazards explicitly.
3. Display a concise **PHASE 01 GATE: GO or NO_GO** summary in this interactive conversation. **Never start the next phase in this session.** Even after GO, the user starts the next numbered prompt in a fresh Gemini CLI session.
4. If NO_GO and **no critical safety failure exists**, ask the user here to choose: **FIX**, **BYPASS 01** with explicit scope and reason, or **STOP**. An acceptable response is `BYPASS 01 — scope: <specific omitted requirement>; reason: <rationale>; I accept the limitation.` Do not interpret `continue`, silence, or a general approval as a bypass.
5. Only after receiving that explicit reply in the active conversation, change the gate to **BYPASSED** and record the user's exact approval, date, scope, reason and unresolved tests in `docs/GATE_LOG.md` and `docs/evidence/01.md`. A bypass is **not a PASS**, does not expand tested coverage, and cannot certify an offline release. If the reply requests FIX, remediate/test this phase and present the gate again.
6. For unauthorized live trading, missing egress containment when claiming offline, credentials leakage, unsafe proxy/TLS bypass, fail-open routing, or live/synthetic order-state contamination: mark **CRITICAL NO_GO** and **do not offer a bypass**.

**Phase-specific bypass boundaries:** May bypass optional build variants or adapters not used in the discovered app, but not broken core contracts or bypass authorization tests.

**Stop here** after the interactive gate decision. In final output include changed files, test evidence, observed gate status, remaining gaps and the exact next prompt filename. Do not implement that next phase.
