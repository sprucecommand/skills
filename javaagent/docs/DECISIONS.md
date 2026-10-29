# Architecture decisions

- ADR-001: Agent intercepts data boundaries; application constructs its own business and Swing objects.
- ADR-002: Separate Swing console, authenticated local IPC, no target Swing action automation.
- ADR-003: Per-service modes LIVE_TEST, RECORD, REPLAY, SIMULATE, BLOCK; unmatched offline routes BLOCK.
- ADR-004: Record raw and decoded SBE/FIX payloads using real codecs/engine where discovered.
- ADR-005: Simulation engine is authoritative for synthetic order/execution/position state.
- ADR-006: External egress must be denied independently of Java agent in strict offline mode.
- ADR-007: Every phase reports GO or NO_GO; only interactive human grants noncritical BYPASSED.
