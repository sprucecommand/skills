# Java Agent Hybrid Offline Simulator — Gemini CLI Markdown Prompt Pack

**17 separate Markdown prompts, one fresh interactive Gemini CLI session per phase. No Python tools, gate scripts or automation utilities.**

## Scope

- Java agent for HTTP(S), Diffusion, FIX, SBE and discovered framed binary TCP/TLS communication.
- Per-service LIVE_TEST / RECORD / REPLAY / SIMULATE / BLOCK routing, with proxy-aware original-destination discovery.
- Persistent captures, decoded and binary payload inspectors, deterministic replay and failure injection.
- Separate Swing simulator console (its own JVM) controlling the agent over authenticated localhost IPC.
- Instrument/market-data simulation; mini OMS/matching engine for new manually entered orders, fills and positions.
- Strict offline mode with **independent** external-network deny controls and no fail-open routing.

**Not included:** target Swing user-action capture or replay, automated buttons/mouse/keyboard, arbitrary controller invocation. The original Swing UI is operated manually. “Offline UI testing” does not mean `-Djava.awt.headless=true`.

## Contents

- `ARCHITECTURE.md` — complete technical design
- `ROADMAP.md` — ordered session list and gates
- `GATE_POLICY.md` — GO / NO_GO / user-approved BYPASSED interactive policy
- `GEMINI.md` — standing rules read in each fresh session
- `prompts/00-discovery.md` through `prompts/16-e2e-release.md` — individual implementation prompts
- `docs/GATE_LOG.md`, `docs/HANDOFF.md`, `docs/COVERAGE_MATRIX.md`, `docs/DECISIONS.md` — small shared context and review records

## Run Session 00

Place the extracted directory at the project workspace root or a sibling directory where Gemini has approved access to the target application's source/JARs and fixtures. From the extracted project root, open **interactive Gemini CLI** and execute:

```bash
gemini --model gemini-3.8-flash -i "Read GEMINI.md and prompts/00-discovery.md. Execute this phase only; perform its interactive GO/NO_GO gate and stop."
```

Use the actual model available in your installed CLI if the specified identifier differs; `gemini --help` shows supported flags.

Review its evidence. When a stage passes, end that CLI process and start a fresh session with the next filename:

```bash
gemini --model gemini-3.8-flash -i "Read GEMINI.md and prompts/01-foundation.md. Execute this phase only; perform its interactive GO/NO_GO gate and stop."
```

If NO_GO, reply `FIX`, `STOP`, or an explicit noncritical approval in the **same interactive Gemini conversation**:

```text
BYPASS 01 — scope: <exact capability deferred>; reason: <why acceptable>; I accept the limitation.
```

No separate terminal, gate script or user-side Python commands. Gemini records an explicit BYPASSED decision into the Markdown gate log and stops. **Critical trading/network/credential safety failures are never bypassable.** The next session must evaluate whether a bypass actually permits its scope.

Keep each Gemini session bounded. The phase prompt names deliverables and evidence. Do not ask Gemini to read `ALL_PROMPTS.md` or full previous transcripts; that would recreate context bloat.

## Safety

Only connect to authorized nonproduction environments, use synthetic trading fixtures, and get approval before providing proprietary code or credentials to a cloud model. Do not run with permissive tool auto-approval when actions could touch real systems. Strict offline simulation requires OS/VM/container egress denial, not merely instrumenting Java HTTP clients.
