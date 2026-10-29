# Session 04 — Recording store, baseline matching and deterministic scheduler

**Execute only this phase in one new Gemini CLI session.** Do not proceed to the next prompt, even if this session finishes early.

## Required startup

1. Read `GEMINI.md`, `GATE_POLICY.md`, `docs/GATE_LOG.md`, `docs/HANDOFF.md`, `docs/DECISIONS.md`, `docs/COVERAGE_MATRIX.md`, and this numbered prompt. Inspect only source and dependencies relevant to this phase. Do not load prior Gemini transcripts.
2. **Prerequisite check:** Read Phase 03 decision and evidence; it must be GO or a user-approved, relevant noncritical BYPASSED entry. If the prerequisite is missing, NO_GO, or supported only by an insufficiently scoped bypass, **stop without implementing this phase** and explain the blocker. Never grant yourself a bypass.
3. Inspect current Git status and preserve unrelated changes. Use real target code, actual dependency versions and observed interfaces. Never invent success evidence.
4. Do not alter target application business logic or implement target UI action recording/playback. The user manually operates the target Swing UI; the separate Swing simulator console is allowed.

## Objective

Store and replay protocol events with fidelity, ordering and inspectability.

**Inputs to use:** Shared contracts and IPC and service policies.

## Implement in this session

1. Create append-only event journal, SQLite or equivalent metadata indexes, content-addressed binary payload blobs, recording manifests, schema fingerprints and redaction rules.
2. Capture monotonic and UTC time, per-connection ordering, request IDs, retries, topic selectors, fixture provenance; support snapshot before event sequence for topics and market state.
3. Build exact request matching plus auditable normalization/ignore rules (IDs, dates, selected headers) without silently accepting mismatches.
4. Build deterministic replay scheduler with logical clock, pause/resume, speed control, bounded async callback queues and recovery/failure semantics.
5. Add encrypted-at-rest or OS-protected fixture storage option, sanitation of auth/secrets/PII, file permissions and retention settings.
6. Design replay outputs: strict NO_FIXTURE, duplicate, EOF, disconnected, mismatched payload; expose reports to console/CLI.
7. Exercise binary payload integrity, mutable-buffer safety, crashes during writing, and replay with multiple connections/topic streams.

## Evidence and required deliverables

- recording store
- replay core
- sample synthetic fixtures
- storage and replay tests
- `docs/evidence/04.md`: source revision, commands executed, observed pass/fail output, coverage, test limitations, critical hazards, incomplete items, and proposed go/no-go decision.
- Update concise `docs/HANDOFF.md` (less than 1,500 words), `docs/DECISIONS.md` and `docs/COVERAGE_MATRIX.md`.

## Phase 04 gate: GO / NO_GO

Mark **GO only if every required assertion below was actually verified**; otherwise mark **NO_GO**, including when the target or runtime is unavailable. Use fixtures for unit tests and separate real-target integration claims.

- [ ] Repeated deterministic replay yields same events, IDs and ordering for seeded fixture.
- [ ] Binary payload hashes and decoded snapshots round-trip.
- [ ] Missing fixtures and malformed event streams fail explicitly.
- [ ] Sensitive tokens are absent from ordinary recordings and console metadata.
- [ ] Storage/replay does not block Swing EDT or hot market-data callbacks.

### Test execution

- Determine the project's actual build/test invocation; run clean compile and focused tests covering the above assertions.
- Include at least one positive test, one negative/fail-closed test and a network/secret safety check. Where a test is inapplicable, document proof of inapplicability rather than silently omitting it.
- Note external/untested behavior as BLOCKED, not PASS.
- Capture enough reproducible output in `docs/evidence/04.md` that the human can independently verify the gate.

### Decision and user override — interactive Gemini CLI only

1. Write the full measured results to `docs/evidence/04.md`, including tested revision, commands, pass/fail/blockers, safety concerns, remaining coverage, and specific acceptance assertions.
2. Update `docs/GATE_LOG.md` with **GO** only if every required assertion above has been verified; otherwise **NO_GO**. Record a timestamp, evidence path, and a short reason in the append-only decision history. Mark critical hazards explicitly.
3. Display a concise **PHASE 04 GATE: GO or NO_GO** summary in this interactive conversation. **Never start the next phase in this session.** Even after GO, the user starts the next numbered prompt in a fresh Gemini CLI session.
4. If NO_GO and **no critical safety failure exists**, ask the user here to choose: **FIX**, **BYPASS 04** with explicit scope and reason, or **STOP**. An acceptable response is `BYPASS 04 — scope: <specific omitted requirement>; reason: <rationale>; I accept the limitation.` Do not interpret `continue`, silence, or a general approval as a bypass.
5. Only after receiving that explicit reply in the active conversation, change the gate to **BYPASSED** and record the user's exact approval, date, scope, reason and unresolved tests in `docs/GATE_LOG.md` and `docs/evidence/04.md`. A bypass is **not a PASS**, does not expand tested coverage, and cannot certify an offline release. If the reply requests FIX, remediate/test this phase and present the gate again.
6. For unauthorized live trading, missing egress containment when claiming offline, credentials leakage, unsafe proxy/TLS bypass, fail-open routing, or live/synthetic order-state contamination: mark **CRITICAL NO_GO** and **do not offer a bypass**.

**Phase-specific bypass boundaries:** Advanced event compression/performance goals may be deferred if bounded and safe. Cannot waive corruption, secret leakage or silently unmatched requests.

**Stop here** after the interactive gate decision. In final output include changed files, test evidence, observed gate status, remaining gaps and the exact next prompt filename. Do not implement that next phase.
