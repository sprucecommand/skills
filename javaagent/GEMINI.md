# Gemini CLI standing instructions — Java Agent simulator

Read this file at the beginning of **each fresh numbered session**. Execute one prompt file per session; stop after the stage's gate outcome and do not autonomously advance. No Python scripts, checkpoint utilities, or external gate CLI are needed.

## Scope

Build a Java agent with protocol-specific interception, independent Swing simulator console, selective service routing, proxy-aware discovery, recording/replay, and stateful market/order simulation. Support actual discovered HTTP(S), Diffusion, FIX, SBE and custom binary TCP/TLS paths. The user **manually operates the original Swing UI**. Do not implement a target UI action recorder, synthetic mouse/keyboard events, automated form entry, arbitrary Swing controller invocation, or app-under-test UI action playback. Controls in the *separate simulator console* are in scope.

## At the start of this session

1. Read `GATE_POLICY.md`, `docs/GATE_LOG.md`, `docs/HANDOFF.md`, `docs/DECISIONS.md`, `docs/COVERAGE_MATRIX.md`, and the current `prompts/NN-*.md`. Consult architecture/roadmap selectively. Avoid loading earlier conversations.
2. Verify prior phase is GO or a **user-approved relevant noncritical BYPASSED** state. If PENDING, NO_GO or unverified, stop without writing implementation code. Phase 00 has no predecessor.
3. Check Git status and build environment before edits; never overwrite unrelated changes. Inspect the actual Java source/JARs and exact SDK versions; do not invent method signatures or successful runs.

## Implementation standards

- Only authorized nonproduction/synthetic test data. Do not contact production trading endpoints, bypass authentication or TLS, or change the target application's business logic without explicit approval.
- For OFFLINE mode, unknown paths fail closed. Independently deny external egress at OS/VM/container layer; allow localhost IPC. Do not rely on the Java agent as the sole network barrier.
- Capture data at the highest reliable decoded library boundary, retain raw binary safely when needed, redact secrets, propagate correlation, preserve required session state, and avoid blocking network threads or the Swing EDT.
- Test positive and negative cases using controlled fixtures. If real target runtime is missing, report BLOCKED instead of PASS. Service-by-service coverage is mandatory.

## End of each phase: interactive GO/NO_GO

1. Produce `docs/evidence/NN.md` with revision, commands and observed results, check-by-check PASS/FAIL/BLOCKED, safety observations, scope and limitations.
2. Update `docs/HANDOFF.md` (max 1,500 words; headings Completed, Decisions, Evidence, Open gaps, Next step), `docs/DECISIONS.md` and `docs/COVERAGE_MATRIX.md` where applicable.
3. Set the status GO only when required assertions passed; otherwise NO_GO. Write result, evidence link and short rationale in `docs/GATE_LOG.md`, preserving previous decisions in its history.
4. **In this interactive Gemini CLI conversation**, show the gate and, only for noncritical NO_GO, ask the **user** to choose FIX, BYPASS NN (with explicit limited scope, reason and risk acceptance), or STOP. Never infer permission from a vague instruction. Record BYPASSED only *after* this explicit user reply.
5. A critical risk—unauthorized trading, uncontrolled external egress in offline claims, secrets leakage, unsafe live/synthetic state mixing, fail-open routing, or unauthorized proxy/TLS subversion—cannot be bypassed. Explain how to remediate.
6. End the session with file changes, evidence, GO/NO_GO/BYPASSED, outstanding issues and next prompt filename. **Do not run the next phase.** A bypass does not certify missing behavior.
