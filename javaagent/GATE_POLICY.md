# Interactive GO / NO-GO / User BYPASS Gate Policy

**No helper scripts or checkpoint CLI.** Every phase 00–16 has its own acceptance checklist in `prompts/NN-*.md`. Gemini records evidence and presents the decision directly inside its **interactive CLI session**. Only the human can authorize bypassing a noncritical failed phase.

## Phase statuses

| Status | Meaning | Can next phase begin? |
|---|---|---|
| PENDING | Not yet executed or evaluated | No |
| GO | Every required acceptance assertion verified and passed | Yes, in a new Gemini session |
| NO_GO | One or more assertions failed, are unverified or blocked | No; fix first, unless noncritical bypass approved |
| BYPASSED | User explicitly accepts a scoped noncritical gap; it remains unsupported | Only stages compatible with the scope; no full-offline certification by implication |

## At every stage boundary

1. Gemini runs relevant positive, negative and fail-closed checks and writes `docs/evidence/NN.md`. Every assertion is marked PASS, FAIL or BLOCKED with actual evidence; missing test infrastructure is BLOCKED, never PASS.
2. Gemini writes GO or NO_GO in `docs/GATE_LOG.md` and appends a dated decision-history entry. It displays the gate result in the conversation.
3. For GO, Gemini reports the next prompt filename and **stops**. The user initiates the next fresh session; no implied automatic continuation.
4. For a **noncritical NO_GO**, Gemini asks for one of the following in the **same interactive conversation**:

   - `FIX` — investigate/remediate and re-run tests in this same phase.
   - `BYPASS NN — scope: <exact omitted capability>; reason: <justification>; I accept the limitation.` — explicitly accept only the named gap.
   - `STOP` — leave the stage NO_GO, do nothing else.

5. Gemini must not record BYPASSED on its own. Only upon receiving the precise explicit user approval does it append the user message, scope, rationale, date, outstanding failures and downstream limitations to `docs/GATE_LOG.md` and `docs/evidence/NN.md`, then stop. A generic “continue”, affirmative response to another question, or silence is not a bypass.
6. The next session checks the prior entry and evidence. If the bypass scope does not cover the intended next step, it stops and asks for clarification or remediation. Later failures may reveal a bypassed assumption invalid; mark NO_GO and stop.

## Example dialog (illustrative)

**Gemini:** `PHASE 07 — NO_GO. FIX encoder/decoder tests passed, but the application does not use QuickFIX/J in the selected workflow. This optional FIX adapter is unverified. Choose FIX, BYPASS 07 with scope/reason, or STOP.`

**User:** `BYPASS 07 — scope: FIX adapter not used by this target workflow; reason: no FIX connection in the runtime inventory; I accept the limitation.`

**Gemini:** Records the exact approval, marks Phase 07 BYPASSED for the explicitly scoped workflow, and stops. Phase 16 still must not claim FIX coverage or complete certification of workflows requiring FIX.

## Critical failures: never bypass

No user bypass for unauthorized live/production trading, failed default-deny network egress in strict offline mode, fallback to a real trading endpoint, exposed secrets, unsafe bypass of TLS/authentication/proxy security, or synthetic orders/executions/positions contaminating live state. These are CRITICAL NO_GO until remedied and retested. This policy is a documented human-review workflow, **not an access-control or authentication mechanism**; independent system safeguards remain required.

## Required audit files (all Markdown)

- `docs/GATE_LOG.md` — current phase statuses plus append-only decision history containing user approvals.
- `docs/evidence/NN.md` — detailed, reproducible stage results and limitations.
- `docs/HANDOFF.md` — short, bounded next-session context.
- `docs/COVERAGE_MATRIX.md` — per-service interception/replay/simulation readiness.
- `docs/DECISIONS.md` — architectural decisions and their rationale.

A phase-level bypass is **not** permission to release a partially protected simulator or to report unsupported protocol paths as working. Phase 16 certifies only named, actually tested offline workflows.
