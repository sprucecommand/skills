# Session 14 — Hybrid scenarios, consistency enforcement, failure injection and reports

**Execute only this phase in one new Gemini CLI session.** Do not proceed to the next prompt, even if this session finishes early.

## Required startup

1. Read `GEMINI.md`, `GATE_POLICY.md`, `docs/GATE_LOG.md`, `docs/HANDOFF.md`, `docs/DECISIONS.md`, `docs/COVERAGE_MATRIX.md`, and this numbered prompt. Inspect only source and dependencies relevant to this phase. Do not load prior Gemini transcripts.
2. **Prerequisite check:** Read Phase 13 decision and evidence; it must be GO or a user-approved, relevant noncritical BYPASSED entry. If the prerequisite is missing, NO_GO, or supported only by an insufficiently scoped bypass, **stop without implementing this phase** and explain the blocker. Never grant yourself a bypass.
3. Inspect current Git status and preserve unrelated changes. Use real target code, actual dependency versions and observed interfaces. Never invent success evidence.
4. Do not alter target application business logic or implement target UI action recording/playback. The user manually operates the target Swing UI; the separate Swing simulator console is allowed.

## Objective

Prove selective service mixing behaves coherently and produces usable validation evidence.

**Inputs to use:** Router, market, OMS, protocol bridge, console and journal.

## Implement in this session

1. Implement declared consistency groups (market/reference, trading/orders/executions, account/positions). Resolve illegal mixes by block or explicit synthetic overlay policy, never silently mix.
2. Define scenario DSL with service routes, fixture IDs, seeds, initial instrument state, expected outputs, timeout, overrides and negative tests. No automated Swing stimulus syntax.
3. Implement simulator fault injection: HTTP error, Diffusion drop/reconnect/stale, FIX disconnect/resend, binary framing failure, SBE invalid template and delayed fills.
4. Produce assertions for outgoing requests, resultant simulator state, displayed-data source checkpoints where observable without driving UI, network isolation attempts, and missing mappings.
5. Emit JUnit XML/JSON/HTML or similar reproducible reports with bypassed gates and unsupported services prominently listed.
6. Test service A live-approved read-only while B simulated, plus all-offline. Verify route provenance and expected behavior changes.

## Evidence and required deliverables

- scenario DSL
- consistency validator
- fault injection tests
- reporters
- `docs/evidence/14.md`: source revision, commands executed, observed pass/fail output, coverage, test limitations, critical hazards, incomplete items, and proposed go/no-go decision.
- Update concise `docs/HANDOFF.md` (less than 1,500 words), `docs/DECISIONS.md` and `docs/COVERAGE_MATRIX.md`.

## Phase 14 gate: GO / NO_GO

Mark **GO only if every required assertion below was actually verified**; otherwise mark **NO_GO**, including when the target or runtime is unavailable. Use fixtures for unit tests and separate real-target integration claims.

- [ ] Hybrid read-only reference plus synthetic order/exec/positions stays coherent.
- [ ] Unsafe live-position/synthetic-execution mix detected and blocked or transparently overlaid.
- [ ] Fault scenarios deterministic with seeded clock.
- [ ] Reports distinguish pass, fail, blocked, bypassed and unsupported.
- [ ] No target UI action automation added.

### Test execution

- Determine the project's actual build/test invocation; run clean compile and focused tests covering the above assertions.
- Include at least one positive test, one negative/fail-closed test and a network/secret safety check. Where a test is inapplicable, document proof of inapplicability rather than silently omitting it.
- Note external/untested behavior as BLOCKED, not PASS.
- Capture enough reproducible output in `docs/evidence/14.md` that the human can independently verify the gate.

### Decision and user override — interactive Gemini CLI only

1. Write the full measured results to `docs/evidence/14.md`, including tested revision, commands, pass/fail/blockers, safety concerns, remaining coverage, and specific acceptance assertions.
2. Update `docs/GATE_LOG.md` with **GO** only if every required assertion above has been verified; otherwise **NO_GO**. Record a timestamp, evidence path, and a short reason in the append-only decision history. Mark critical hazards explicitly.
3. Display a concise **PHASE 14 GATE: GO or NO_GO** summary in this interactive conversation. **Never start the next phase in this session.** Even after GO, the user starts the next numbered prompt in a fresh Gemini CLI session.
4. If NO_GO and **no critical safety failure exists**, ask the user here to choose: **FIX**, **BYPASS 14** with explicit scope and reason, or **STOP**. An acceptable response is `BYPASS 14 — scope: <specific omitted requirement>; reason: <rationale>; I accept the limitation.` Do not interpret `continue`, silence, or a general approval as a bypass.
5. Only after receiving that explicit reply in the active conversation, change the gate to **BYPASSED** and record the user's exact approval, date, scope, reason and unresolved tests in `docs/GATE_LOG.md` and `docs/evidence/14.md`. A bypass is **not a PASS**, does not expand tested coverage, and cannot certify an offline release. If the reply requests FIX, remediate/test this phase and present the gate again.
6. For unauthorized live trading, missing egress containment when claiming offline, credentials leakage, unsafe proxy/TLS bypass, fail-open routing, or live/synthetic order-state contamination: mark **CRITICAL NO_GO** and **do not offer a bypass**.

**Phase-specific bypass boundaries:** May waive advanced fault cases, not cross-service identity or financial-state inconsistency.

**Stop here** after the interactive gate decision. In final output include changed files, test evidence, observed gate status, remaining gaps and the exact next prompt filename. Do not implement that next phase.
