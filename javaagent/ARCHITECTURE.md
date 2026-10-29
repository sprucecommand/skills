# Selective Service Virtualization for a Java Swing Thick Client

**Version:** 2.0 (design specification)  
**Primary outcome:** a tester manually uses the unmodified application's Swing UI while intercepted external services behave as live-approved test services, recorded fixtures, simulated services, or explicitly blocked services. A separate Swing console controls the agent. **No UI action capture, UI driving, or automatic order-entry implementation is included.**

## 1. Target process architecture

```text
                       ORIGINAL JAVA SWING APP (JVM A)
  +--------------------------------------------------------------------+
  | normal Swing UI, application objects, service clients, listeners  |
  | +--------------------- -javaagent ------------------------------+ |
  | | startup interception, protocol adapters, service registry,   | |
  | | proxy-aware routing, replay scheduler, connection monitor,   | |
  | | safe event snapshots, egress policy checks                    | |
  | +-----------+-----------------------+---------------------------+ |
  +-------------|-----------------------|-----------------------------+
                | localhost authenticated IPC / event stream
                |                       | logical service routes
  +-------------v---------------+       +-----------------------------+
  | SIMULATOR CONSOLE (JVM B)   |                                     |
  | Swing UI: Service Catalog   |   SIMULATOR BACKEND (inside JVM B   |
  | routing, timeline, payloads |   or a separately launched JVM C) |
  | recording, playback, market |   +------------------------------+ |
  | book, orders, fills, tests  +-->| event store / replay scheduler| |
  +-----------------------------+   | reference / market state       | |
                                    | mini OMS + matching engine     | |
                                    | event -> protocol encoders     | |
                                    +------------------------------+ |
  APPROVED TEST SERVICES <----- LIVE / CAPTURE routes -----------------+
  EXTERNAL EGRESS DENY <----- strict offline mode at OS/container/VM layer
```

**Separation of responsibility:** The Java agent instruments and routes. The simulator owns business state and deterministic outcomes. The Swing console is a control/observability client, not a business controller inside the target app. The original application instantiates its own forms, services, clients and protocol listeners. JVM A should not require the simulator Swing window to be visible: agent control must function if the console disconnects. Where JVM B is not available, explicitly fail a simulated service rather than sending to live.

## 2. Runtime modes and service-specific policies

- Global modes: `DISCOVER` (metadata only), `CAPTURE` (approved test destinations and redacted recording), `HYBRID` (mixed routes with explicit live test allowlist), `OFFLINE` (all egress denied externally), and `VALIDATE` (offline + assertions).
- Per-service route: `LIVE_TEST` (only explicit approved nonproduction endpoint), `RECORD` (live test pass-through plus capture), `REPLAY` (recorded exact response/event), `SIMULATE` (stateful generated behavior), `BLOCK` (explicit failure).
- Each service has `serviceId`, protocol, destination identity, matcher, source class/method, logical endpoint (not simply proxy hostname), session/connection constraints, dependencies, ownership consistency group, mode, fixtures, health, capture coverage, and simulation capability.
- Resolution precedence: explicit endpoint/topic/template rules > service policy > connection-level fallback > **BLOCK**. Unmatched egress in strict offline mode must fail, never fall back to live.
- Some service boundaries are coarser than individual messages: FIX sessions, HTTP2 multiplexing, proxy tunnels and encrypted binary streams may require whole-connection virtualization or interception after application-level decoding.
- `LIVE_TEST` and `RECORD` are prohibited for write-capable trading services unless there is a separate explicit and verified nonproduction authorization policy. No production order path is an allowed fallback.

## 3. Instrumentation inventory and proxy strategy

Discover at source/dependency AND controlled runtime levels:

- HTTP stacks: JDK `HttpURLConnection`, `HttpClient`, Apache, OkHttp, Spring/JAX-RS clients and application wrappers. Record request method, original target URL, canonicalizable headers/body and status/body/error, with redaction and byte limits. Support synchronous/async/cancellation, streaming, compression, redirects, proxies and retries where used.
- Diffusion Java SDK: session lifecycle, topic selector registrations, initial values, updates, removal/disconnect, decoded values including delta-reconstructed values, delivery ordering and listener identity. No need for proprietary wire decoding when SDK callbacks are available.
- FIX: actual engine version, logon/session state, `toApp`/`fromApp` style interception or equivalent, sequence numbers, session settings, message dictionaries, resend/reset/heartbeat lifecycle. Never inject an isolated execution report into an uninitialized session without a supported adapter.
- SBE: schema XML or generated codecs, header block length/version/templateId/schemaId, message framing, flyweight buffer copies at stable dispatcher boundary, field decode, binary-faithful replay and encoding. Do not assume every binary socket contains SBE.
- Custom TCP/TLS: framing, connection/session handling, NIO/Netty/native/JNI, length-prefix/delimiters, TLS boundaries, endian, compression, message identity, request/response vs pubsub. Do not treat `Socket.read` chunks as messages.
- Proxies: JVM properties, `ProxySelector`, PAC/system proxy, HTTP CONNECT, SOCKS, credentials, explicit HTTP client proxies, Diffusion SDK proxies, FIX gateways and Netty proxy handlers. Track `logicalService`, original target host:port, chosen proxy host:port and actual socket. Intercept above encryption/proxy whenever possible. Handle proxy auth as redacted metadata, not recorded secret replay by default.
- Startup and adjacent dependencies: login, entitlements, DNS, reference data, time sync, static files, local databases, native libraries, certificates/truststores, licensing, remote configuration and local child processes.
- Runtime coverage enumerates observed path + intercepted path + unknown outbound sockets. Source inspection is not proof of full offline coverage.

## 4. Protocol adapter and shared event contract

`ProtocolAdapter` detects applicable classes, installs narrow Byte Buddy transformations, emits immutable capture events, exposes deterministic replay hooks and advertises capability flags (`capture`,`replay`,`simulate`,`decode`,`fault`,`proxy-aware`). Avoid broadly instrumenting all Java methods or loading application classes from an agent-specific class loader in ways that cause type identity failures. Use reentrancy guards, classloader-aware matchers and bounded queues. Keep binary blobs separate from indexed metadata.

Canonical event envelope (illustrative):

```json
{
  "eventId":"...", "sessionId":"...", "correlationId":"...",
  "monotonicNanos":1234, "wallClockUtc":"...", "sequence":42,
  "serviceId":"orders", "protocol":"HTTP", "direction":"OUTBOUND",
  "logicalDestination":"orders.test.invalid", "proxyDestination":"proxy.test.invalid:8080",
  "connectionId":"...", "topicOrTemplate":"...", "schemaFingerprint":"...",
  "payloadBlobSha256":"...", "decodedFieldsRef":"...",
  "redactionPolicy":"...", "replayCompatibility":"..."
}
```

Capture **the concrete transport effect** as well as decoded semantics when needed. For SBE/flyweights or reused direct buffers, copy raw data in the capture callback before the producer changes it. Do expensive decoding/storage off hot threads; preserve immutable ordering metadata. No unbounded logging, no raw credentials or secret keys.

## 5. Replay and matching model

A request is matched by service, operation, canonicalized request keys (selected headers/body fields), correlation context and prior fixture state. An unmatched request returns explicit `NO_FIXTURE` or simulator error, never silently falls through to real services. Store request normalization/ignore rules as auditable scenario configuration. Use replay clock (wall vs monotonic), deterministic seed, stable event journal, session correlation, timeouts and strict ordering within relevant transport contexts. Async callbacks execute on a compatible dispatcher; never run long IO on Swing EDT.

Recorded replay matches known inputs. Stateful simulation supports unseen orders and requests. Hybrid mode mixes recorded market/reference data with dynamically simulated trading, explicitly tracking provenance of each field.

## 6. Business simulation: market state + mini OMS/matching engine

- **Reference repository:** instrument ID, exchange, currency, instrument type, tick size, lot size, trading status, price bands, session calendar, configured account restrictions; each field has a source/provenance and missing-field policy.
- **Market generator:** either snapshot + timed replay, seeded synthetic quotes/depth, or explicit manual quote/book scenarios. Maintain bid <= ask for a normal unlocked book unless a negative test explicitly models a crossed market; respect tick sizes, bounded price process, staleness and exchange session status. No invented hidden depth when only top-of-book is known.
- **Order engine:** canonical `New`, `Accepted`, `Rejected`, `PartiallyFilled`, `Filled`, `CancelRequested`, `Canceled`, `ReplaceRequested`, `Replaced`, `Expired` events; supported type/time-in-force matrix based on actual app. Use `BigDecimal` or fixed-point price, integer quantities as appropriate, deterministic IDs, clock and sequenced journal.
- **Matching:** simple price-time/FIFO liquidity model with configured synthetic resting liquidity, book updates and conservation checks (sum fills <= order quantity; `leavesQty = orderQty - cumQty`). Marketable limit orders cross eligible levels at or better than the limit; nonmarketable rest. Synthetic book depletion is tracked, with configurable replenishment rather than assuming exchange truth. Support forced fill/reject/partial-fill and fault injections from separate console.
- **State owners:** one authoritative simulated store of orders/executions/positions, with explicit accounting model (net quantity and price accounting) based on needed UI behaviors. Do not blend synthetic trading state into a real positions service without an overlay or mandatory consistency-group virtualization.
- **Protocol bridge:** canonical business events encoded using discovered actual HTTP/FIX/SBE/Diffusion message contracts; no guessed template IDs or topic names. Simulation readiness for order flow requires proof of actual inbound/outbound protocol mapping. Engine does not place real trades.

## 7. Simulator Swing console

Independent JVM showing tabs: `Agents`, `Connections & Proxy`, `Services & Routing`, `Timeline`, `Payload Decoder`, `Recordings`, `Replay & Validation`, `Instrument/Market Book`, `Orders/Executions/Positions`, `Faults`, `Coverage & Gates`, `Audit`. Provide per-service Live test / Record / Replay / Simulate / Block drop-down with dependency warnings; support session reset/restart, safe runtime policy changes only when quiescent, filter/search payload and hex/structured views, recording lifecycle, speed control, manual market/working-order outcomes, correlation inspection, protocol adapter health and export logs/reports. Swing console updates asynchronously to avoid freezing EDT. Local control channel authenticates the console, checks permissions and supports reconnection/version negotiation.

**Explicit exclusion:** No automation of target application's Swing UI; no synthesized clicks, keyboard events, form manipulation, ActionListener invocation, business method invocation or screenshot-driven UI scripts. User manually interacts with the original app. The simulator console can have controls for simulator state and outcomes.

## 8. Hard isolation and verification

A Java agent is not a sufficient network firewall. Strict offline/validate runs must be launched within an independently enforced deny-by-default network policy at OS/container/VM boundary, permitting only required local IPC. Verify failed outbound attempts and DNS behavior. Provide a separate `HYBRID` profile for explicitly allowlisted nonproduction read-only/live services; don't label hybrid as offline. Fail closed on agent, simulator or adapter failure. Prefer intercepting before transport rather than trusting proxy redirection or truststore hacks.

Always use synthetic or authorized test fixtures. Redact tokens, personally identifying data, account details and trading credentials. The console may show sanitized previews and gated raw bytes. Follow least privilege, audit updates, and avoid leaking secrets via Gemini prompts or generated handoff files.

## 9. Acceptance scenario

1. Start approved sandbox capture, manually open Swing trading application, gather initial instrument/reference data, market updates and protocol messages.
2. Save normalized, redacted recording including schema fingerprints, dependency graph and verified replay readiness.
3. Launch in strict externally enforced offline network mode. Original Swing UI opens with simulated startup services and subscribed data.
4. Manually enter a new order (not necessarily one recorded earlier). Agent intercepts outbound order through discovered protocol boundary.
5. Mini OMS validates instrument/account rules, creates order state, responds with actual discovered protocol contract and emits ACK, fills, updates and position changes.
6. Original app displays consistent order blotter, execution and position views. Manual console controls can force partial fill/reject/disconnect.
7. No external packets leave the sandbox; unknown paths fail. Test report includes completeness, assertions, timings and all accepted bypasses.

No acceptance may falsely claim a given protocol works until an end-to-end fixture proves its real target-app decoding path.

## 10. Stage-by-stage workflow and gate policy

Each stage 00–16 runs in a **fresh, interactive Gemini CLI session** using `prompts/NN-*.md`. The first task in every session is to inspect `docs/GATE_LOG.md`, previous evidence and the standing rules in `GEMINI.md`; no gate automation tools or scripts are involved. Each stage produces `docs/evidence/NN.md`, records a GO or NO_GO with supporting evidence, and stops. The next stage starts only when the previous stage is GO, or the user has granted a **specific noncritical BYPASSED** approval directly in the interactive Gemini CLI conversation.

On NO_GO, Gemini shows the failed criteria and asks the user to choose FIX / BYPASS NN / STOP. Only an explicit user reply containing phase, scoped omission, reason and risk acceptance authorizes a bypass. Record this in `docs/GATE_LOG.md` without hiding the failure; never bypass critical security/trading/egress violations. A bypass never changes a failed test to PASS and cannot certify unsupported workflows as fully offline-ready.

See `GATE_POLICY.md`, `ROADMAP.md` and each `prompts/NN-*.md` for acceptance requirements. Do not add target Swing user action recording or replay.
