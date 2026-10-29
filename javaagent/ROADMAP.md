# Implementation roadmap — isolated Gemini CLI sessions

Every row is one **new** Gemini process; all rows end in a GO / NO_GO gate. Only the human may authorize a scoped noncritical NO_GO bypass through an explicit reply in the active Gemini CLI conversation. Do not implement any target Swing UI action recording or replay.

| Step | Prompt | Stage focus | Hard evidence |
|---|---|---|---|
| 00 | `prompts/00-discovery.md` | Source + runtime discovery and architecture mapping | No invented actual SDK version or interceptor signature; source locations support each claimed mapping. |
| 01 | `prompts/01-foundation.md` | Multi-module foundation, contracts and Markdown gate records | Clean build and unit tests pass on target-compatible Java version. |
| 02 | `prompts/02-agent-ipc.md` | Agent bootstrap and authenticated localhost control channel | Java agent starts without changing target binaries and reports version + capabilities. |
| 03 | `prompts/03-service-routing-proxy.md` | Service registry, selective routing and proxy-aware discovery | Original destination correctly distinguished from proxy transport destination. |
| 04 | `prompts/04-store-replay.md` | Recording store, baseline matching and deterministic scheduler | Repeated deterministic replay yields same events, IDs and ordering for seeded fixture. |
| 05 | `prompts/05-http.md` | HTTP / HTTPS capture, replay and selective virtualization | Offline request returns exact recorded response without establishing target/proxy TCP connection. |
| 06 | `prompts/06-diffusion.md` | Diffusion SDK subscription capture and simulated delivery | Subscription initialization succeeds offline in test fixture/app path. |
| 07 | `prompts/07-fix.md` | FIX engine capture and session-aware replay | FIX transport or engine events round-trip with valid session behavior in fixture. |
| 08 | `prompts/08-sbe.md` | SBE schema discovery, binary capture and codec replay | One or more actual target SBE types decode and re-encode/dispatch faithfully, or target marked not applicable. |
| 09 | `prompts/09-binary-tcp.md` | Custom framed binary TCP/TLS and native boundary audit | Frames reconstructed correctly under fragmentation/coalescing. |
| 10 | `prompts/10-market-state.md` | Instrument reference, market-state and synthetic feed engine | Every required instrument field has provenance and missing-data policy. |
| 11 | `prompts/11-mini-oms-matcher.md` | Stateful mini OMS, matching, executions and positions | New order not present in recordings produces deterministic ACK and permitted fills. |
| 12 | `prompts/12-trading-protocol-bridge.md` | Map canonical trading events into actual HTTP/FIX/SBE/Diffusion contracts | At least one actual target order->ACK->fill workflow mapped end-to-end or marked NO_GO. |
| 13 | `prompts/13-swing-console.md` | Full separate Swing simulator console and operator workflows | Two JVMs connect/disconnect and reconnect securely without loss of safe control state. |
| 14 | `prompts/14-hybrid-validation.md` | Hybrid scenarios, consistency enforcement, failure injection and reports | Hybrid read-only reference plus synthetic order/exec/positions stays coherent. |
| 15 | `prompts/15-security-egress.md` | Hardening, network containment, performance and privacy audit | In strict OFFLINE launch, independent egress controls deny all external endpoints even with agent disabled. |
| 16 | `prompts/16-e2e-release.md` | End-to-end manual UI qualification, packaging and release readiness | Actual target app can start, display data, and process manually submitted new synthetic order offline. |

## Release sequencing

After each stage: (1) Gemini writes evidence, (2) Gemini reports GO/NO_GO and records it in `docs/GATE_LOG.md`, (3) on a noncritical NO_GO the user replies FIX, BYPASS NN with scope/reason, or STOP **within the same interactive Gemini session**, (4) Gemini records any explicit approval, stops, and (5) the user starts the next numbered prompt in a fresh Gemini session only after its prerequisite is GO or appropriately BYPASSED.

Do not interpret an optional adapter bypass as comprehensive offline readiness. Phases 15 and 16 require independent egress denial and proved actual target-app workflows.

## Implementation prerequisites

- Java source/JARs and actual launch scripts
- Supported target JVM and build tools
- Explicitly authorized sandbox/capture endpoints and nonproduction synthetic order identifiers
- Network-denied VM/container for offline integration tests
- Diffusion version, FIX engine and SBE codec/schema information if actually used
- Tester capable of manually entering target Swing application orders for Phase 16
