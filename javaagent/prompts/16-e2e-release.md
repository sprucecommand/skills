# Session 16 — End-to-end manual UI qualification, packaging and release readiness

**Execute only this phase in one new Gemini CLI session.** Do not proceed to the next prompt, even if this session finishes early.

## Required startup

1. Read `GEMINI.md`, `GATE_POLICY.md`, `docs/GATE_LOG.md`, `docs/HANDOFF.md`, `docs/DECISIONS.md`, `docs/COVERAGE_MATRIX.md`, and this numbered prompt. Inspect only source and dependencies relevant to this phase. Do not load prior Gemini transcripts.
2. **Prerequisite check:** Read Phase 15 decision and evidence; it must be GO or a user-approved, relevant noncritical BYPASSED entry. If the prerequisite is missing, NO_GO, or supported only by an insufficiently scoped bypass, **stop without implementing this phase** and explain the blocker. Never grant yourself a bypass.
3. Inspect current Git status and preserve unrelated changes. Use real target code, actual dependency versions and observed interfaces. Never invent success evidence.
4. Do not alter target application business logic or implement target UI action recording/playback. The user manually operates the target Swing UI; the separate Swing simulator console is allowed.

## Objective

Certify a narrowly defined fully-offline workflow with honest coverage and explicit limitations.

**Inputs to use:** Completed adapters, engine, console, evidence reports, known actual Swing app, controlled offline runtime.

## Implement in this session

1. Bundle agent JAR, Swing console JAR, simulator backend and CLI, synthetic fixtures, launch/runbook templates, update scripts, installer policy and test reports.
2. Execute cold-start target app in strict OS/VM-denied external network environment. Manually operate original Swing UI (the user or engineer) to view reference and market data and submit a never-before-recorded synthetic order.
3. Validate ACK, one partial fill, final fill, cancel/replace/reject case, execution blotter and position views through actual application communication handlers; note any unsupported visual assertions as manual observation.
4. Change selective routing to simulate one unavailable service while keeping an approved nonproduction read-only service live. Confirm correct proxy selection, destination classification and consistency warnings.
5. Prove agent+backend crash/missing fixture/unclassified socket scenarios fail closed; collect firewall/sandbox egress logs, not merely agent logs.
6. Run regression suite, produce JUnit + coverage report mapping every required service to pass/partial/unsupported, list approved BYPASSED stages and remaining risks.
7. Prepare operator runbook including recovery, session reset, selective-service UI, fixture refresh, test-data cleanup, never-live-trading warning, and how to run one fresh Gemini CLI session for enhancements.

## Evidence and required deliverables

- packaged distributables
- `docs/RELEASE_REPORT.md`
- `docs/OPERATIONS.md`
- end-to-end evidence including egress logs
- CI smoke test
- `docs/evidence/16.md`: source revision, commands executed, observed pass/fail output, coverage, test limitations, critical hazards, incomplete items, and proposed go/no-go decision.
- Update concise `docs/HANDOFF.md` (less than 1,500 words), `docs/DECISIONS.md` and `docs/COVERAGE_MATRIX.md`.

## Phase 16 gate: GO / NO_GO

Mark **GO only if every required assertion below was actually verified**; otherwise mark **NO_GO**, including when the target or runtime is unavailable. Use fixtures for unit tests and separate real-target integration claims.

- [ ] Actual target app can start, display data, and process manually submitted new synthetic order offline.
- [ ] All required service endpoints have verified intercept or block behavior, and OS egress denies real endpoints.
- [ ] Order/ack/fill/position state matches; partial/cancel/reject cases pass.
- [ ] Hybrid mode uses only approved read-only test services and shows proxy routing accurately.
- [ ] Every bypass/unsupported workflow is prominent: release is NO_GO if an essential path is unproven.

### Test execution

- Determine the project's actual build/test invocation; run clean compile and focused tests covering the above assertions.
- Include at least one positive test, one negative/fail-closed test and a network/secret safety check. Where a test is inapplicable, document proof of inapplicability rather than silently omitting it.
- Note external/untested behavior as BLOCKED, not PASS.
- Capture enough reproducible output in `docs/evidence/16.md` that the human can independently verify the gate.

### Decision and user override — interactive Gemini CLI only

1. Write the full measured results to `docs/evidence/16.md`, including tested revision, commands, pass/fail/blockers, safety concerns, remaining coverage, and specific acceptance assertions.
2. Update `docs/GATE_LOG.md` with **GO** only if every required assertion above has been verified; otherwise **NO_GO**. Record a timestamp, evidence path, and a short reason in the append-only decision history. Mark critical hazards explicitly.
3. Display a concise **PHASE 16 GATE: GO or NO_GO** summary in this interactive conversation. **Never start the next phase in this session.** Even after GO, the user starts the next numbered prompt in a fresh Gemini CLI session.
4. If NO_GO and **no critical safety failure exists**, ask the user here to choose: **FIX**, **BYPASS 16** with explicit scope and reason, or **STOP**. An acceptable response is `BYPASS 16 — scope: <specific omitted requirement>; reason: <rationale>; I accept the limitation.` Do not interpret `continue`, silence, or a general approval as a bypass.
5. Only after receiving that explicit reply in the active conversation, change the gate to **BYPASSED** and record the user's exact approval, date, scope, reason and unresolved tests in `docs/GATE_LOG.md` and `docs/evidence/16.md`. A bypass is **not a PASS**, does not expand tested coverage, and cannot certify an offline release. If the reply requests FIX, remediate/test this phase and present the gate again.
6. For unauthorized live trading, missing egress containment when claiming offline, credentials leakage, unsafe proxy/TLS bypass, fail-open routing, or live/synthetic order-state contamination: mark **CRITICAL NO_GO** and **do not offer a bypass**.

**Phase-specific bypass boundaries:** No bypass can make an unsupported essential workflow qualify as fully offline-ready. Human may ship a labeled limited-scope preview only if all critical safety gates pass.

**Stop here** after the interactive gate decision. In final output include changed files, test evidence, observed gate status, remaining gaps and the exact next prompt filename. Do not implement that next phase.
