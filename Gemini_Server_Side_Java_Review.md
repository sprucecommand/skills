---
name: java-gcp-server-security-code-review
description: >
  Evidence-driven security, correctness, reliability, and cloud architecture code review
  for server-side Java applications deployed on Google Cloud Platform. Reviews application
  source, configuration, infrastructure-as-code, CI/CD, dependencies, data access,
  authentication/authorization, integrations, runtime configuration, and operational
  controls. Produces prioritized findings with concrete fixes and verification steps.
version: 1.0
---

# Java + GCP Server Application Code Review Skill

## 1. Mission

Perform a rigorous, evidence-driven code and architecture review of a server-side Java
application deployed to Google Cloud Platform.

The goal is not merely to find stylistic issues. The goal is to identify defects,
security vulnerabilities, unsafe assumptions, cloud misconfigurations, reliability gaps,
data-integrity risks, operational weaknesses, supply-chain exposure, performance hazards,
and maintainability problems that could cause:

- Unauthorized access.
- Tenant or account isolation failure.
- Data disclosure, corruption, loss, or unintended retention.
- Privilege escalation.
- Injection, SSRF, deserialization, path traversal, or command execution.
- Authentication or session compromise.
- Payment, financial, or business-logic abuse.
- Duplicate, replayed, or out-of-order side effects.
- Incorrect transaction or concurrency behavior.
- Partial failure and unrecoverable state.
- Denial of service or uncontrolled cloud cost.
- Secret or credential compromise.
- Compromised build or deployment artifacts.
- Insufficient logging, detection, auditability, backup, or recovery.
- Unsafe rollout, migration, or rollback.
- Performance or scalability failure.
- Difficult-to-maintain code that increases security or operational risk.

Do not assume the application is secure because it uses Java, Spring, managed GCP
services, HTTPS, OAuth, IAM, containers, or a private network. Verify how controls are
actually implemented and configured.

Treat source code, deployment configuration, infrastructure-as-code, tests, CI/CD,
runtime configuration, cloud IAM, network configuration, and operational controls as
parts of one system.

---

## 2. Intended Scope

Use this skill for Java server applications including, but not limited to:

- Spring Boot / Spring MVC / Spring WebFlux.
- Jakarta EE / JAX-RS.
- Micronaut.
- Quarkus.
- Dropwizard.
- Vert.x.
- gRPC Java.
- Plain Java HTTP or socket services.
- REST, GraphQL, SOAP, WebSocket, event-driven, batch, and scheduled workloads.

Relevant GCP deployments may include:

- Cloud Run.
- Google Kubernetes Engine.
- Compute Engine.
- App Engine.
- Cloud Functions / Cloud Run functions.
- Cloud Batch.
- Managed instance groups.
- Hybrid or multi-cloud services calling GCP.

Relevant GCP dependencies may include:

- IAM and service accounts.
- Secret Manager.
- Cloud KMS.
- Cloud SQL.
- AlloyDB.
- Spanner.
- Firestore / Datastore.
- Bigtable.
- Memorystore.
- Cloud Storage.
- Pub/Sub.
- Cloud Tasks.
- Eventarc.
- Workflows.
- API Gateway.
- Cloud Load Balancing.
- Identity-Aware Proxy.
- VPC, firewall rules, Cloud NAT, Private Service Connect.
- VPC Service Controls.
- Artifact Registry.
- Cloud Build.
- Cloud Deploy.
- Binary Authorization.
- Cloud Logging.
- Cloud Monitoring.
- Error Reporting.
- Cloud Trace.
- Security Command Center.

Do not assume all of these are present. Discover actual use and review only relevant
controls while recording material missing evidence.

---

## 3. Relationship to Application Discovery

This review assumes application behavior should first be understood well enough to trace:

- Entry points.
- Identity and trust boundaries.
- Authorization decisions.
- Data ownership.
- Transactions.
- External effects.
- Background work.
- Failure and retry paths.
- Configuration-dependent behavior.
- Deployment units.

If a discovery project already exists, reuse its capability, function, workflow,
integration, evidence, risk, and question identifiers where practical.

Do not silently accept discovery documentation as proof. Re-check the code and
configuration for security-sensitive claims.

---

## 4. Review Principles

### 4.1 Evidence before assertion

Every substantive finding must identify evidence such as:

- Repository and revision.
- Relative file path.
- Class, method, symbol, resource, manifest, or configuration key.
- Line range where useful.
- Relevant deployment or IaC object.
- Relevant test or missing test.
- Runtime evidence only when authorized and observed.

Do not report a vulnerability merely because a dangerous API exists. Establish whether
untrusted input or attacker-controlled state can reach it, under what conditions, and
what the resulting impact could be.

### 4.2 Distinguish certainty

Classify each finding as one of:

- **Confirmed** — vulnerable or defective path is established by evidence.
- **Highly likely** — strong evidence exists, but one environmental or runtime fact is missing.
- **Possible** — plausible path exists but exploitability or reachability is not established.
- **Hardening** — not currently demonstrated exploitable, but materially reduces risk.
- **Informational** — useful architectural or maintainability observation.

Also assign confidence: High / Medium / Low.

### 4.3 Fixes must be actionable

Every finding must include:

1. What is wrong.
2. Why it matters.
3. Preconditions for exploitation or failure.
4. Evidence.
5. Affected assets / users / tenants / environments.
6. Recommended fix.
7. Preferred long-term design when different from the immediate fix.
8. Regression or security test.
9. Operational verification.
10. Residual risk after remediation.
11. Whether secret rotation, data repair, incident review, or historical investigation is required.

### 4.4 Review the effective system

A code review is incomplete if it ignores:

- IAM.
- Service accounts.
- Network exposure.
- Secret delivery.
- Runtime identities.
- Container build.
- CI/CD.
- Environment variables.
- Database privileges.
- GCP project separation.
- Infrastructure-as-code.
- Artifact provenance.
- Logging and alerts.

Explicitly state which of these were unavailable.

### 4.5 Do not equate a framework feature with effective protection

Examples:

- `@PreAuthorize` is not proof all reachable methods are protected.
- `@Transactional` is not proof the intended transaction boundary is effective.
- Parameterized SQL in one repository is not proof all dynamic SQL is safe.
- CORS configuration is not CSRF protection.
- JWT signature verification is not sufficient authorization.
- A private IP is not proof of isolation.
- Secret Manager use is not proof the service account has least privilege.
- Encryption at rest is not proof application-level confidentiality requirements are met.
- Managed TLS is not proof outbound TLS verification is correct.
- Cloud Run authentication does not protect a service configured for unauthenticated invocation.
- Kubernetes Secret objects are not equivalent to external secret management.

---

## 5. Review Authorization and Safety

Static source and configuration inspection is allowed unless the user says otherwise.

Do not perform the following without explicit authorization for the relevant environment:

- Start production services.
- Send live traffic.
- Execute destructive tests.
- Replay messages.
- Modify cloud resources.
- Change IAM.
- Rotate secrets.
- Run database migrations.
- Query production customer records.
- Run penetration tests against public or private endpoints.
- Execute unknown repository scripts or build hooks.
- Install software or dependencies.
- Exploit a suspected vulnerability against a real environment.

When execution is not authorized, provide exact safe validation steps rather than
performing them.

Never expose secret values, tokens, private keys, customer data, or proprietary payloads
in the review output.

---

## 6. Baseline Standards

Use these as review guides, not as substitutes for reasoning:

- OWASP Application Security Verification Standard (ASVS), current applicable release.
- OWASP Top 10, current applicable release.
- OWASP API Security guidance where APIs are present.
- Oracle Secure Coding Guidelines for Java SE.
- Current security guidance for the Java/JDK version in use.
- Google Cloud security best practices.
- Google Cloud IAM and service-account security guidance.
- Google Cloud Secret Manager and Cloud KMS guidance.
- Google Cloud software supply-chain guidance.
- SLSA principles where applicable.
- NIST Secure Software Development Framework concepts where useful.

Do not claim compliance with a standard unless the user specifically requests a formal
assessment and sufficient evidence is available.

---

# PART A — ESTABLISH THE REVIEW BASELINE

## 7. Repository and Runtime Baseline

Record:

- Repository name and revision / commit SHA.
- Working-tree changes.
- Java version and vendor.
- Build system and wrapper version.
- Framework and major version.
- Dependency-management mechanism.
- Packaging type.
- Container base image.
- Deployment platform.
- GCP project(s), environment(s), and regions where known.
- Runtime service account.
- Database and messaging technologies.
- Inbound exposure.
- Identity provider.
- CI/CD system.
- Infrastructure-as-code tooling.
- Production configuration source.
- Known environments: local / dev / test / staging / prod.

Flag ambiguity when source defaults cannot establish production values.

---

## 8. Attack Surface Inventory

Identify every applicable entry point:

- REST endpoints.
- GraphQL operations.
- gRPC methods.
- SOAP operations.
- WebSocket endpoints.
- SSE or streaming endpoints.
- Message consumers.
- Pub/Sub push handlers.
- Eventarc handlers.
- Cloud Tasks handlers.
- Webhooks.
- OAuth callbacks.
- Scheduled jobs.
- Batch jobs.
- Startup / shutdown hooks.
- File uploads and imports.
- Admin endpoints.
- Actuator / management endpoints.
- Health / metrics endpoints.
- Debug endpoints.
- Maintenance commands.
- Database triggers and procedures.
- Native/JNI interfaces.
- Shell commands and subprocesses.
- Dynamic script or expression execution.
- Deserialization boundaries.

For each, identify:

- Authentication requirement.
- Authorization rule.
- Caller type.
- Internet / internal / VPC exposure.
- Data sensitivity.
- Mutating vs read-only.
- Idempotency expectations.
- Rate / size controls.
- Trust boundary crossed.
- Downstream effects.

Prioritize externally reachable mutations and administrative operations.

---

# PART B — APPLICATION SECURITY REVIEW

## 9. Authentication

Review:

- Authentication architecture and identity provider.
- OAuth 2.0 / OIDC flows.
- Authorization-code + PKCE where applicable.
- Redirect URI validation.
- `state` and nonce validation.
- Token audience, issuer, signature, expiry, and not-before validation.
- JWKS retrieval and caching.
- Key rotation behavior.
- Algorithm restrictions; reject `none` and unexpected algorithms.
- Access-token vs ID-token confusion.
- Token substitution risks.
- Token replay.
- Refresh-token handling and storage.
- Revocation behavior.
- Logout semantics.
- Session fixation.
- Session rotation after authentication or privilege change.
- Session inactivity and absolute lifetime.
- Cookie flags: Secure, HttpOnly, SameSite, path, domain.
- Persistent login / remember-me behavior.
- Password storage if local credentials exist.
- MFA enforcement for privileged users if applicable.
- API keys and machine credentials.
- Webhook/shared-secret authentication.
- Service-to-service ID tokens.
- Cloud Run / IAP identity propagation.
- Anonymous or fallback authentication paths.
- Test or development authentication that may be enabled in production.

Look for authentication failure that fails open.

---

## 10. Authorization and Access Control

This is a highest-priority review area.

Verify authorization for every sensitive operation, not merely route groups.

Review:

- Role-based access control.
- Attribute-based access control.
- Object/resource ownership checks.
- Tenant/account/organization scoping.
- Administrative overrides.
- Privilege elevation.
- Delegation / impersonation.
- Support or operator access.
- Background-worker authority.
- Internal endpoint assumptions.
- Method-level security.
- URL-level security.
- Repository-level data scoping.
- Field-level or function-level authorization where needed.
- Mass assignment / over-posting.
- IDOR / BOLA.
- Broken function-level authorization.
- Horizontal privilege escalation.
- Vertical privilege escalation.
- Cross-tenant cache leakage.
- Cross-tenant queue or message processing.
- Export/report authorization.
- Search/filter paths that bypass normal authorization.
- Batch endpoints that authorize the batch but not each member.
- Indirect object references.
- Authorization on non-HTTP entry points.

Require denial by default.

Flag authorization that occurs only after data is fetched if unauthorized data can be
observed through timing, logs, errors, cache, or side effects.

---

## 11. Multi-Tenancy and Data Isolation

Where tenancy exists, trace tenant identity through:

request → authentication → authorization → service layer → repository → cache →
message → background worker → downstream call → logs / metrics / export.

Review:

- Tenant selection source.
- Whether caller can override tenant identifiers.
- Database query scoping.
- Composite keys.
- Unique constraints.
- Cache-key tenant prefixing.
- Object-storage key layout.
- Search indexes.
- Pub/Sub messages.
- Cloud Tasks payloads.
- Scheduled work.
- Temporary files.
- Exports.
- Metrics labels.
- Error reports.
- Dead-letter queues.
- Admin APIs.
- Support tooling.

Look specifically for "confused deputy" behavior.

---

## 12. Input Validation and Canonicalization

Review all untrusted inputs:

- Path parameters.
- Query parameters.
- Headers.
- Cookies.
- JSON/XML bodies.
- Form bodies.
- Multipart uploads.
- GraphQL variables.
- gRPC messages.
- Message/event payloads.
- Database-sourced values originally supplied by users.
- Files.
- Cloud Storage object names.
- Pub/Sub attributes.
- Callback URLs.
- Remote API responses.
- Configuration received from external systems.

Check:

- Type validation.
- Length limits.
- Range limits.
- Collection size limits.
- Unicode normalization where relevant.
- Enum allowlists.
- Canonicalization before validation.
- Duplicate parameter handling.
- Content-Type enforcement.
- Numeric overflow / underflow.
- Date/time parsing.
- Locale assumptions.
- Regex denial-of-service.
- Recursive / deeply nested structures.
- JSON/XML parser limits.
- ZIP / archive bombs.
- Image/media parser abuse.
- CSV formula injection in generated exports.
- Spreadsheet injection.
- XML External Entity risks.
- XInclude.
- YAML unsafe constructors.
- Protobuf unknown-field handling where material.

Prefer allowlists for constrained values.

---

## 13. Injection

Trace attacker-controlled data into:

- SQL.
- JPQL / HQL.
- Criteria API dynamic fragments.
- Native queries.
- NoSQL queries.
- Search expressions.
- LDAP.
- XPath.
- XML.
- Shell commands.
- `ProcessBuilder`.
- Runtime execution.
- Template engines.
- Expression languages.
- SpEL.
- OGNL.
- JEXL.
- JavaScript engines.
- Logging format strings.
- HTTP headers.
- Email headers.
- File paths.
- Object-store paths.
- GraphQL query construction.
- Regular expressions.
- Dynamic class loading.

Look for partial parameterization where identifiers, order-by clauses, table names, or
fragments remain attacker-controlled.

---

## 14. SSRF and Outbound Request Security

Review all server-initiated HTTP, HTTPS, gRPC, socket, DNS, and URL access.

Identify whether users can influence:

- Scheme.
- Host.
- Port.
- Path.
- Redirect destination.
- Proxy.
- DNS name.
- IP address.
- Header.
- Credential selection.

Protect:

- GCP metadata server.
- Link-local addresses.
- Loopback.
- RFC1918/private ranges where not intended.
- Internal service names.
- Kubernetes services.
- Admin endpoints.
- Cloud SQL proxies.
- Redis/Memorystore.
- Internal load balancers.

Review:

- URL parsing inconsistencies.
- DNS rebinding.
- Redirect following.
- IPv6 representations.
- Decimal/octal/hex IP forms where relevant.
- Credential forwarding to redirected hosts.
- Proxy environment variables.
- Connection timeout.
- Read timeout.
- Response-size limit.

Prefer destination allowlists or purpose-built clients.

---

## 15. CSRF, CORS, Browser and Web Security

When browser clients or cookies are involved, review:

### CSRF

- State-changing requests.
- CSRF tokens.
- SameSite cookie policy.
- Origin / Referer validation where appropriate.
- JSON endpoints that accept form-compatible content.
- GET endpoints with side effects.
- Login CSRF.
- OAuth callback handling.

### CORS

- Allowed origins.
- Wildcards.
- Credentialed CORS.
- Origin reflection.
- Null origins.
- Preflight handling.
- Environment-specific origin lists.

### Response security

Review:

- Content Security Policy where application serves HTML.
- HSTS.
- `X-Content-Type-Options`.
- Frame restrictions / `frame-ancestors`.
- Referrer policy.
- Cache-Control for sensitive responses.
- MIME correctness.
- Open redirects.
- Host-header trust.
- Forwarded-header trust.

Do not cargo-cult browser headers onto pure machine APIs; explain applicability.

---

## 16. File Upload, Download, and Path Safety

Review:

- Filename handling.
- Path traversal.
- Absolute paths.
- Symlinks.
- Archive extraction.
- Zip Slip.
- File extension allowlist.
- MIME inspection.
- Size limits.
- Storage quotas.
- Malware scanning if business risk warrants it.
- Content sniffing.
- Public bucket/object exposure.
- Signed URL lifetime and scope.
- User-controlled `Content-Disposition`.
- Cross-tenant object keys.
- Temporary file permissions.
- Secure deletion requirements.
- Cleanup after failure.
- Download authorization.
- Range requests.
- Object generation races.

Never trust the submitted filename as a filesystem path.

---

## 17. Serialization and Deserialization

Review:

- Java native serialization.
- `ObjectInputStream`.
- RMI.
- Hessian or legacy RPC.
- Kryo.
- XStream.
- XML decoders.
- Jackson polymorphic typing.
- Gson custom type adapters.
- YAML.
- Custom binary formats.
- Cached serialized objects.
- Session serialization.
- Message queue payload deserialization.

Flag deserialization of attacker-controlled data into arbitrary object graphs.

Review type allowlists, size/depth limits, and gadget exposure.

---

## 18. Cryptography

Inventory cryptographic usage and its purpose.

Review:

- TLS configuration.
- Certificate validation.
- Hostname validation.
- Trust-all managers.
- Disabled hostname verifier.
- Private CA handling.
- mTLS where required.
- Password hashing.
- Encryption.
- Signing.
- HMAC.
- Random identifiers.
- API keys.
- Token generation.
- Nonces.
- IV generation.
- Key derivation.

Flag:

- MD5 or SHA-1 for security-sensitive integrity.
- ECB mode.
- Static IVs.
- Reused nonces.
- Predictable `Random`.
- Hard-coded keys.
- DIY cryptographic protocols.
- Insecure key storage.
- Inadequate key rotation.
- Encryption without authentication where AEAD is appropriate.
- Comparing MACs/tokens with inappropriate equality logic where timing matters.

Prefer platform/JDK cryptographic providers and well-established constructions.

---

## 19. Secrets and Credentials

Search for:

- Passwords.
- API keys.
- OAuth client secrets.
- Refresh tokens.
- Service-account JSON keys.
- Private keys.
- Database credentials.
- Webhook secrets.
- Encryption keys.
- Signing keys.
- Bearer tokens.
- Test secrets that might be valid outside tests.

Inspect:

- Git history where authorized.
- Configuration files.
- Dockerfiles.
- CI/CD definitions.
- Test fixtures.
- Logs.
- Exception messages.
- Environment dumps.

For GCP:

- Prefer attached workload identity / service account credentials over exported keys.
- Prefer Workload Identity Federation where appropriate.
- Avoid long-lived service-account keys.
- Use Secret Manager for secret material.
- Grant secret access at the smallest practical resource scope.
- Separate production and non-production secret access.
- Review rotation and rollback behavior.
- Review Secret Manager version pinning vs `latest`.
- Verify old versions are disabled/destroyed as policy requires.
- Review secret caching and in-memory exposure.

If an actual secret is discovered, never reproduce it. Record only its location/type and
recommend rotation if exposure is possible.

---

## 20. Sensitive Data and Privacy

Classify important data:

- Credentials.
- Tokens.
- Personal information.
- Payment data.
- Financial data.
- Health data.
- Location data.
- Communications.
- Customer content.
- Proprietary business data.

Review:

- Data minimization.
- Field-level access.
- Logging.
- Exception messages.
- Tracing.
- Metrics labels.
- Analytics.
- Cache.
- Temporary storage.
- Backups.
- Exports.
- Test fixtures.
- Development copies.
- Retention.
- Purging.
- Encryption.
- Tokenization / pseudonymization.
- Data residency where applicable.

Flag sensitive values embedded in URLs because URLs may enter access logs, browser
history, proxies, traces, and monitoring systems.

Do not assert regulatory compliance without a formal scoped assessment.

---

## 21. Business Logic Security

Review workflows for abuse that scanners usually miss.

Examples:

- Price or amount tampering.
- Negative quantities.
- Duplicate redemption.
- Coupon abuse.
- Replayed requests.
- Order/payment mismatch.
- State-transition skipping.
- Race-to-claim resource.
- Account linking or invitation abuse.
- Enumeration.
- Bypass of approval.
- Improper cancellation after irreversible external effect.
- Refund abuse.
- Reconciliation gaps.
- Reuse of expired or already-consumed artifacts.
- Trusting client-computed totals.
- Inconsistent validation between create/update/admin paths.
- Performing authorization only at initial workflow step.
- Time-of-check / time-of-use races.

Document invariants the code is expected to preserve.

---

# PART C — DATA AND TRANSACTION CORRECTNESS

## 22. Database Security

Review:

- Connection authentication.
- TLS to database.
- Database IAM where applicable.
- Credential storage.
- Database user privilege.
- Shared privileged users.
- Schema ownership.
- Public IP exposure.
- Authorized networks.
- Cloud SQL connector / Auth Proxy configuration.
- Connection pool settings.
- Connection leaks.
- Query timeouts.
- Statement timeouts.
- Long-running transactions.
- Audit requirements.
- Database logging exposure.

Review whether application credentials can:

- Drop schema.
- Create users.
- Alter infrastructure.
- Read unrelated databases.
- Bypass row-level controls.

Prefer least privilege.

---

## 23. Transactions and Atomicity

For important mutations, trace:

1. Validation.
2. Database reads.
3. Database writes.
4. External calls.
5. Message publication.
6. Commit.
7. Response.

Review:

- Actual transaction boundary.
- Proxy/self-invocation issues.
- Checked vs unchecked exception behavior.
- Rollback configuration.
- Nested transactions.
- `REQUIRES_NEW`.
- Async work escaping transaction.
- Lazy loading outside transactions.
- Flush timing.
- External side effects inside DB transactions.
- Publication-before-commit.
- Publication-after-commit crash window.
- Duplicate event handling.
- Lost response after successful commit.

Flag impossible atomicity assumptions across database and remote services.

---

## 24. Concurrency and Race Conditions

Inspect:

- Read-modify-write flows.
- Inventory/reservation.
- Quotas.
- Counters.
- Balances.
- State transitions.
- Idempotency records.
- Job claiming.
- Cache population.
- Refresh tokens.
- Leader election.
- Scheduled tasks.
- File generation.
- Unique resource creation.

Review:

- Optimistic locking.
- Version columns.
- Unique constraints.
- Atomic SQL operations.
- Pessimistic locking.
- Lock ordering.
- Distributed locks.
- Lock expiry.
- Fencing tokens.
- Thread-safe collections.
- Mutable singletons.
- `static` mutable state.
- Race-prone check-then-act.

Do not treat `synchronized` as distributed locking.

---

## 25. Idempotency, Replay, and Duplicate Delivery

Review every mutating externally callable or asynchronously delivered operation.

Determine:

- Idempotency key.
- Scope.
- Persistence.
- Expiry.
- Collision behavior.
- Concurrent duplicate behavior.
- Replayed message behavior.
- Duplicate webhook handling.
- Retry behavior after timeout.
- Lost response behavior.
- External system idempotency guarantees.

For payments, provisioning, notifications, fulfillment, or irreversible effects,
idempotency should receive special scrutiny.

---

## 26. Schema and Migration Safety

Review:

- Migration tool.
- Migration ownership.
- Forward compatibility.
- Backward compatibility.
- Rolling deployment compatibility.
- Destructive schema changes.
- Renames.
- NOT NULL additions.
- Default behavior.
- Long locks.
- Large table rewrites.
- Index creation.
- Data backfills.
- Retryability.
- Rollback feasibility.
- Multiple app versions running simultaneously.
- Migration permissions.
- Production startup migration behavior.

Flag application startup that can unexpectedly perform high-risk production migrations.

---

# PART D — JAVA-SPECIFIC REVIEW

## 27. Java Language and JDK Security

Review:

- Supported JDK version.
- Patch level.
- End-of-life JDKs.
- Unsafe reflection.
- `setAccessible`.
- Method handles.
- Dynamic proxies.
- Dynamic class loading.
- Custom class loaders.
- Java agents.
- JNI/JNA/native libraries.
- Temporary file APIs.
- File permissions.
- Process execution.
- Environment-variable exposure.
- Security-sensitive system properties.
- Unsafe API use.
- Object mutability.
- Exposure of mutable internal state.
- Sensitive data retained in long-lived strings where relevant.
- Resource cleanup via try-with-resources.
- Integer overflow in security/business calculations.
- Locale-sensitive security comparisons.
- Charset assumptions.
- Unicode edge cases.

Treat JNI/native code as a separate high-risk trust boundary.

---

## 28. Framework-Specific Security

When Spring is present, inspect at minimum:

- SecurityFilterChain configuration.
- Multiple filter chains and order.
- `requestMatchers`.
- Permit-all paths.
- Method security.
- CSRF settings.
- CORS settings.
- Session policy.
- OAuth2 resource server.
- JWT decoder.
- Authentication providers.
- Password encoder.
- Exception handling.
- Actuator exposure.
- Management port.
- Forwarded headers.
- Error endpoints.
- Data binding.
- Jackson configuration.
- SpEL.
- Spring Data query construction.
- `@Async`.
- `@Scheduled`.
- `@Transactional`.
- Cache abstraction.
- Environment profiles.
- Development-only beans.
- Debug endpoints.

For WebFlux, additionally review blocking work on event-loop threads and context/security
propagation.

For other frameworks, inspect the equivalent authentication, authorization, routing,
serialization, error handling, and management configuration.

---

## 29. Dependency and Library Review

Inspect direct and transitive dependencies.

Look for:

- Known vulnerabilities.
- Unsupported libraries.
- Abandoned libraries.
- Snapshot dependencies.
- Dynamic versions.
- Unpinned plugins.
- Duplicate conflicting versions.
- Shaded vulnerable code.
- Unnecessary dependencies.
- Dependencies loaded from untrusted repositories.
- Repository mirrors.
- Dependency confusion.
- Typosquatting risk.
- Plugin execution during build.
- Test-only dependency leakage.
- Native libraries.
- JavaScript or frontend assets bundled in the server.

Do not blindly recommend upgrading to "latest." Determine compatibility and identify the
minimum safe supported upgrade when possible.

Produce an SBOM recommendation if one is absent.

---

# PART E — GCP CLOUD SECURITY REVIEW

## 30. IAM and Service Accounts

Treat GCP identity configuration as part of the application.

Review:

- Which service account each deployable unit runs as.
- Whether default service accounts are used.
- Project/folder/org-level grants.
- Primitive roles such as Owner/Editor/Viewer.
- Broad predefined roles.
- Custom roles.
- Service Account User.
- Service Account Token Creator.
- Service account impersonation.
- Service-account key creation.
- Service-account keys stored in files or secrets.
- Workload Identity Federation.
- Workload Identity Federation for GKE.
- Cross-project access.
- Privilege escalation paths.
- IAM conditions.
- Resource-level IAM.
- Domain-wide delegation if applicable.

Prefer:

- Dedicated single-purpose runtime identities.
- Short-lived credentials.
- No downloadable keys where a stronger method exists.
- Least privilege at resource scope.

Evaluate what an attacker gains if the runtime service account is compromised.

---

## 31. GCP Metadata Service Exposure

On platforms where metadata server access is relevant, review SSRF and untrusted code
paths that might obtain workload credentials.

Check:

- Whether arbitrary URL fetches can reach metadata endpoints.
- Whether user-supplied code or plugins run under privileged workload identity.
- Whether proxies expose metadata indirectly.
- VM/GKE-specific metadata protections.

Treat metadata access as credential access.

---

## 32. Cloud Run

When deployed to Cloud Run, review:

- Public vs authenticated invocation.
- Ingress setting.
- Runtime service account.
- Service-to-service authentication.
- ID token audience.
- Domain mappings.
- Load balancer path.
- Cloud Armor where applicable.
- VPC egress.
- Direct VPC egress / connector configuration.
- Min/max instances.
- Concurrency.
- Request timeout.
- CPU allocation.
- Startup probes.
- Health checks where applicable.
- Environment variables.
- Secret mounts.
- Revision traffic splitting.
- Old revisions.
- Deployment permissions.
- Binary Authorization if used.
- Jobs vs services.
- Admin APIs accidentally exposed publicly.

Do not assume "internal" ingress alone provides application-level authorization.

---

## 33. GKE

When deployed to GKE, review:

- Workload Identity Federation for GKE.
- Pod service accounts.
- Kubernetes RBAC.
- Cluster-admin grants.
- Namespace isolation.
- NetworkPolicy.
- Pod Security Standards.
- Privileged pods.
- Host networking.
- Host PID/IPC.
- HostPath.
- Capabilities.
- `runAsNonRoot`.
- Read-only root filesystem.
- Seccomp.
- Resource requests/limits.
- Secrets.
- ConfigMaps containing secrets.
- Image tags vs digests.
- Admission controls.
- Binary Authorization.
- Public control-plane access.
- Authorized networks.
- Private clusters.
- Node service accounts.
- Metadata protection.
- Ingress.
- Internal/external load balancers.
- Service mesh / mTLS where present.

---

## 34. Compute Engine

When deployed to VMs, review:

- Instance service account.
- IAM permissions.
- Access scopes.
- Metadata startup scripts.
- Shielded VM.
- OS patching.
- SSH access.
- OS Login.
- Serial console.
- Public IP.
- Firewall tags/service accounts.
- Persistent disk encryption requirements.
- Instance templates.
- Managed instance groups.
- Secrets on disk.
- Container or systemd unit permissions.
- Local admin/sudo paths.

---

## 35. Network Security

Review:

- Internet-exposed endpoints.
- Load balancer topology.
- Firewall rules.
- `0.0.0.0/0` and `::/0`.
- Open management ports.
- VPC segmentation.
- Shared VPC.
- VPC peering.
- Private Google Access.
- Private Service Connect.
- Cloud NAT.
- Egress controls.
- DNS.
- Internal load balancers.
- Service perimeters.
- VPC Service Controls where high-value data warrants them.
- TLS termination.
- Backend TLS.
- mTLS.
- Cloud Armor / WAF where applicable.

Do not substitute network location for application authorization.

---

## 36. Secret Manager and KMS

Review:

- Secret IAM.
- Runtime access.
- CI/CD access.
- Human access.
- Project separation.
- Secret replication policy.
- Rotation.
- Version lifecycle.
- Audit logs.
- Secret injection method.
- Secret caching.
- Secret leakage into logs.
- KMS key IAM.
- Key rotation.
- Destruction schedule.
- Separation of key admin vs key user when required.
- CMEK requirements where applicable.

Avoid giving the application broader Secret Manager or KMS access than it needs.

---

## 37. Cloud Storage

Review:

- Uniform bucket-level access.
- Public access prevention.
- IAM.
- Signed URLs.
- Signed policy documents.
- Object naming.
- Cross-tenant paths.
- Upload validation.
- Retention policies.
- Object versioning.
- Lifecycle policies.
- Event notifications.
- Customer-controlled metadata.
- Content-Type.
- Cache-Control.
- CORS.
- Temporary staging buckets.

Check whether application-generated signed URLs are narrower and shorter-lived than the
underlying application credential.

---

## 38. Pub/Sub, Cloud Tasks, Eventarc, and Async GCP Services

Review:

- Producer identity.
- Consumer identity.
- Topic/subscription IAM.
- Push endpoint authentication.
- OIDC tokens.
- Audience validation.
- Ack timing.
- Retry policy.
- Dead-letter topic.
- Ordering keys.
- Exactly-once assumptions.
- Duplicate delivery.
- Message retention.
- Replay.
- Poison messages.
- Payload size.
- Sensitive payloads.
- Tenant identifiers.
- Correlation IDs.
- Cloud Tasks target authentication.
- Task-name deduplication.
- Schedule time.
- Deadline.
- Retry amplification.

Never assume message delivery implies exactly-once business effects.

---

## 39. Databases on GCP

For Cloud SQL / AlloyDB / Spanner / Firestore and other data services, review:

- Network access.
- Public vs private endpoints.
- IAM DB authentication where applicable.
- TLS.
- Application DB account.
- Least privilege.
- Backups.
- PITR.
- Replication.
- Restore testing evidence.
- Deletion protection.
- Encryption requirements.
- Query performance.
- Hot spots.
- Transaction semantics.
- Isolation guarantees.
- Consistency assumptions.

Replication is not a backup.

---

## 40. Logging, Monitoring, Detection, and Audit

Review application and GCP logs for:

- Authentication events.
- Authorization denials.
- Admin actions.
- Sensitive configuration changes.
- Security-relevant business actions.
- External integration failures.
- Retry exhaustion.
- Dead-letter growth.
- High error rates.
- Suspicious enumeration.
- Rate-limit triggers.
- IAM changes.
- Secret access where appropriate.
- Deployment history.
- Database anomalies.

Check that logs do NOT contain:

- Passwords.
- Full bearer tokens.
- Refresh tokens.
- API secrets.
- Private keys.
- Session cookies.
- Sensitive request bodies by default.
- Excessive personal information.

Review:

- Structured logging.
- Correlation / trace IDs.
- Request IDs.
- Tenant/user attribution where appropriate.
- Log injection.
- Log retention.
- Alert policies.
- Audit logs.
- Data Access logs where needed.
- Access to log sinks.
- Exported logs.
- Redaction.

A log without an alert or review path may not provide timely detection.

---

# PART F — RELIABILITY AND AVAILABILITY

## 41. Exceptional Conditions

Review how the application handles:

- Nulls.
- Timeouts.
- Partial responses.
- Dependency outages.
- Rate limits.
- Invalid states.
- Database deadlocks.
- Pool exhaustion.
- Disk full.
- Memory pressure.
- Thread exhaustion.
- Executor rejection.
- Message backlog.
- Duplicate messages.
- Clock skew.
- Missing configuration.
- Secret unavailable.
- Token refresh failure.
- Network partition.
- Process shutdown.
- Deployment during work.

Look for exception handling that:

- Swallows failures.
- Returns success on failure.
- Exposes stack traces.
- Leaks sensitive data.
- Retries unsafe operations.
- Converts specific security failures into generic permissive behavior.

Fail securely.

---

## 42. Timeouts, Retries, Circuit Breakers

For every remote dependency, inspect:

- Connect timeout.
- Read timeout.
- Overall deadline.
- Retry count.
- Retry conditions.
- Backoff.
- Jitter.
- Retry budget.
- Circuit breaker.
- Bulkhead.
- Fallback.
- Cancellation.

Flag:

- Infinite timeout.
- Infinite retry.
- Retry of non-idempotent effects without protection.
- Layered retries that amplify traffic.
- Immediate tight retry loops.
- Fallback that bypasses authorization or returns unsafe stale data.

---

## 43. Resource Exhaustion and DoS

Review limits for:

- HTTP body.
- Headers.
- Multipart upload.
- JSON depth.
- Collection size.
- Decompression.
- Database queries.
- Pagination.
- Export size.
- Regex work.
- Thread pools.
- Executors.
- Queues.
- Connection pools.
- Cache.
- Local disk.
- Temporary files.
- Memory.
- Pub/Sub backlog.
- Batch size.

Review cost-amplification attacks specific to cloud:

- Expensive database scans.
- Unbounded API fan-out.
- Unbounded Cloud Tasks creation.
- Storage growth.
- Large logs.
- Autoscaling abuse.
- High-cost external API calls.

---

## 44. Graceful Shutdown and Restart Recovery

Review:

- SIGTERM handling.
- Request draining.
- Worker shutdown.
- Message acknowledgement.
- Lease release.
- In-flight transactions.
- Scheduled work.
- Temporary files.
- Connection closure.
- Executor termination.
- Checkpointing.
- Restart recovery.

Cloud Run/GKE scaling and deploys make shutdown correctness important.

---

## 45. Health Checks

Review:

- Liveness.
- Readiness.
- Startup probes.
- Dependency checks.
- Expensive health checks.
- Sensitive information exposed by health endpoints.
- Whether readiness prevents traffic before required initialization.
- Whether liveness can cause restart loops during downstream outages.

Do not make liveness depend on every remote dependency.

---

# PART G — PERFORMANCE AND SCALABILITY

## 46. Java Runtime and Threading

Review:

- Request thread pools.
- Async executors.
- Virtual threads if used.
- ForkJoin common pool.
- Blocking calls.
- WebFlux event-loop blocking.
- Deadlocks.
- Unbounded thread creation.
- Thread-local leakage.
- Context propagation.
- MDC cleanup.
- Connection pool alignment with concurrency.

Check GCP platform concurrency against JVM and database pool limits.

---

## 47. Database Performance

Review:

- N+1 queries.
- Missing indexes.
- Unbounded scans.
- Pagination.
- Offset pagination at large scale.
- Batch operations.
- Fetch joins.
- Lazy loading.
- Lock duration.
- Query timeout.
- Transaction duration.
- Connection pool size.
- Pool exhaustion.
- Retry on transient DB errors.

Do not optimize at the expense of authorization or data correctness.

---

## 48. Cache Correctness

Review:

- Cache key composition.
- Tenant/user scoping.
- TTL.
- Invalidation.
- Negative caching.
- Stale data.
- Stampede protection.
- Cache poisoning.
- Serialization.
- Sensitive data.
- Authorization-dependent cached output.
- Local vs distributed cache differences.

Never cache an authorization-sensitive response under a key that omits the security
context required to distinguish callers.

---

# PART H — SUPPLY CHAIN, BUILD, AND DEPLOYMENT

## 49. Build Security

Review:

- Maven/Gradle wrapper.
- Plugin versions.
- Repository URLs.
- HTTP repositories.
- Internal repositories.
- Dependency locking / verification.
- Checksums/signatures where supported.
- Build scripts.
- Generated code.
- Downloaded executables.
- Shell scripts.
- Test containers.
- Code generation.
- Annotation processors.
- Build secrets.
- Reproducibility.

Treat build plugins and annotation processors as executable code.

---

## 50. Container Security

Review:

- Base image provenance.
- Digest pinning.
- Image age.
- Vulnerabilities.
- Minimal runtime.
- Root user.
- File permissions.
- Package managers left in final image.
- Build tools left in final image.
- Secrets copied into layers.
- Multi-stage builds.
- `.dockerignore`.
- Health behavior.
- Exposed ports.
- Writable filesystem.
- JVM flags.
- CA trust store.
- Debug agents.
- JDWP exposure.

Prefer immutable, minimal runtime images.

---

## 51. CI/CD Security

Review:

- Who can modify pipeline files.
- Branch protection.
- Review requirements.
- Build identity.
- Deployment identity.
- Separation of duties where warranted.
- Long-lived credentials.
- OIDC / federation.
- Artifact signing / attestation.
- Artifact promotion.
- Environment approval.
- Secret exposure.
- Fork pull-request behavior.
- Untrusted build execution.
- Pull-request access to secrets.
- Production deploy permissions.
- Rollback permissions.
- Audit trail.

Where container-based deployment is used, evaluate Artifact Registry vulnerability
scanning, provenance/attestation, and Binary Authorization or equivalent policy controls.

---

## 52. Infrastructure-as-Code

Review Terraform, Pulumi, Deployment Manager, Helm, Kubernetes YAML, scripts, or other IaC.

Look for:

- Broad IAM.
- Public access.
- Permissive firewall rules.
- Missing encryption settings.
- Missing deletion protection.
- Shared identities.
- Default service accounts.
- Hard-coded secrets.
- Unpinned modules/providers/images.
- Drift-prone manual configuration.
- Dangerous lifecycle rules.
- Public buckets.
- Missing audit settings.
- Missing log retention.
- Missing backup/PITR.
- Overly broad network access.
- Environment mixing.

Distinguish IaC intent from proven deployed state.

[O---

# PART I — API AND INTEGRATION REVIEW

## 53. API Contract Safety

Review:

- Versioning.
- Backward compatibility.
- Validation.
- Error shape.
- Sensitive errors.
- Pagination.
- Filtering.
- Sorting.
- Field selection.
- Resource enumeration.
- Bulk endpoints.
- Partial updates.
- ETags / optimistic concurrency.
- Content negotiation.
- Idempotency.
- Rate limiting.

Avoid returning internal class names, SQL errors, filesystem paths, stack traces, or secret
configuration.

---

## 54. Webhooks

Review:

- Signature / MAC verification.
- Timestamp tolerance.
- Replay prevention.
- Canonical payload.
- Raw-body verification before parsing where required by protocol.
- Secret rotation.
- Multiple active keys.
- Source IP assumptions.
- Duplicate delivery.
- Asynchronous processing.
- Fast acknowledgement.
- Retry behavior.
- Idempotency.

Do not rely solely on source IP unless the provider explicitly guarantees a stable secure
mechanism and it is implemented safely.

---

## 55. Outbound Third-Party Integrations

Review:

- Credential handling.
- OAuth token lifecycle.
- Scope.
- Rotation.
- Timeout.
- Retry.
- Idempotency.
- TLS.
- Certificate verification.
- Webhook reconciliation.
- Rate limiting.
- Quota.
- Failure mapping.
- Data sent externally.
- PII leakage.
- Sandbox vs production endpoints.
- Endpoint configuration injection.
- Auditability.

When an external request may have succeeded despite timeout, require reconciliation or
idempotent retry.

---

# PART J — TESTING QUALITY

## 56. Security Test Coverage

Look for tests covering:

- Authentication required.
- Unauthorized role.
- Wrong tenant.
- Wrong object owner.
- Admin-only function.
- Expired token.
- Invalid audience.
- Tampered token.
- Duplicate requests.
- Concurrent requests.
- Replay.
- Invalid state transition.
- SQL/meta-character inputs.
- SSRF targets.
- Path traversal.
- Oversized body.
- Malformed JSON/XML.
- File upload abuse.
- Rate limit.
- Secret redaction.
- Webhook signature.
- Transaction rollback.
- External timeout.
- Partial failure.
- Retry.
- Dead-letter handling.

Security tests should assert denial as well as success.

---

## 57. Test Trustworthiness

Review:

- Tests disabled or ignored.
- Tests dependent on order.
- Mocking that removes the security control under test.
- Tests that only assert HTTP status.
- Flaky integration tests.
- Production profile differences.
- Test-only configuration accidentally packaged.
- Fixtures containing secrets or personal data.

Do not infer production safety solely from unit tests.

---

# PART K — OPERATIONS, BACKUP, RECOVERY, AND INCIDENT READINESS

## 58. Backup and Disaster Recovery

Review only based on evidence:

- Backup configuration.
- Backup retention.
- PITR.
- Cross-region strategy.
- Object versioning.
- Restore procedures.
- Restore tests.
- RPO/RTO if documented.
- Secret recovery.
- KMS key dependency.
- Infrastructure reconstruction.
- Dependency on a single project or region.
- Accidental deletion protection.

Explicitly distinguish replication from backup.

---

## 59. Incident Readiness

Review whether operators can determine:

- Who performed a security-sensitive action.
- Which deployment introduced a defect.
- Which users/tenants were affected.
- Whether a credential was used.
- Whether data was read or changed.
- Whether retries caused duplicate effects.
- Which build artifact is running.
- Which source commit produced it.

Recommend incident playbook gaps only where they materially affect discovered risks.

---

# PART L — CODE QUALITY THAT AFFECTS RISK

## 60. Maintainability and Defect Risk

Review:

- Overly large classes/methods.
- Duplicated security/business rules.
- Ambiguous naming.
- Hidden side effects.
- Static/global mutable state.
- Excessive exception swallowing.
- Magic values affecting business/security rules.
- Configuration scattered across code.
- Tight coupling to external systems.
- Poor testability.
- Dead code with reachable registration.
- Old compatibility paths.
- Deprecated APIs.
- TODO/FIXME security debt.
- Disabled validation.
- Copied security logic.

Do not file cosmetic style findings unless they materially affect comprehension,
correctness, maintainability, or security.

---

# PART M — REQUIRED REVIEW WORKFLOW

## 61. Phase 1 — Triage

First identify:

1. Publicly reachable entry points.
2. Authentication and authorization architecture.
3. Privileged/admin operations.
4. Runtime service account and IAM.
5. Secret handling.
6. Data stores and sensitive data.
7. External effects.
8. Build/deploy path.
9. High-risk dependencies.
10. Major asynchronous workflows.

Produce early critical findings immediately; do not wait for the entire review.

---

## 62. Phase 2 — Deep Traces

Trace high-risk flows end to end.

For each:

trigger
→ authentication
→ authorization
→ validation
→ business rules
→ data reads
→ data writes
→ external effects
→ transaction commit
→ message/event publication
→ response
→ retry/recovery/reconciliation

Review normal, malicious, duplicate, concurrent, timeout, and partial-failure paths.

---

## 63. Phase 3 — Cross-Cutting Search

Perform targeted searches for risky APIs and constructs relevant to the stack, then verify
reachability before filing findings.

Examples to investigate:

- `Runtime.getRuntime().exec`
- `ProcessBuilder`
- `ObjectInputStream`
- `setAccessible`
- custom `TrustManager`
- hostname verifier overrides
- `SSLContext`
- raw SQL concatenation
- `createNativeQuery`
- SpEL / expression evaluation
- `Class.forName`
- `URL`
- `URI`
- `HttpClient`
- OkHttp / Apache HttpClient
- file/path APIs
- temp files
- archive extraction
- YAML parsing
- XML parser factories
- Jackson default typing
- `Random`
- weak hashes
- hard-coded credentials
- `permitAll`
- disabled CSRF
- wildcard CORS
- actuator exposure
- debug endpoints
- `@Async`
- `@Scheduled`
- `@Transactional`
- mutable `static` fields
- catch-all exception blocks

A search hit is a lead, not a vulnerability.

---

## 64. Phase 4 — Cloud and Deployment Review

Review:

- Terraform / IaC.
- Kubernetes manifests.
- Cloud Run service/job definitions.
- GCP IAM bindings.
- Service account configuration.
- Secret Manager.
- Network exposure.
- CI/CD.
- Artifact Registry.
- Logging / Monitoring.
- Database configuration.

Compare deployment configuration with assumptions made by the Java application.

---

## 65. Phase 5 — Completeness Pass

Before declaring review complete, reconcile:

- Every externally reachable mutating endpoint.
- Every admin operation.
- Every tenant boundary.
- Every authentication mechanism.
- Every external integration.
- Every secret source.
- Every database.
- Every message consumer/producer.
- Every scheduled job.
- Every file upload/import.
- Every dynamic execution path.
- Every deployable unit.
- Every runtime service account.
- Every production deployment path.

Anything unreviewed must be explicitly listed.

---

# PART N — FINDING FORMAT

## 66. Required Finding Template

Use stable identifiers: `FIND-0001`, `FIND-0002`, ...

### FIND-XXXX — Short title

**Severity:** Critical / High / Medium / Low / Informational  
**Confidence:** High / Medium / Low  
**Status:** Confirmed / Highly likely / Possible / Hardening / Informational  
**Category:** e.g. Access Control / IAM / Injection / Reliability / Supply Chain  
**Affected component:**  
**Affected environment(s):**  
**Exploitability:**  
**Business impact:**  

**Summary**

Explain the issue in plain language.

**Evidence**

List exact source/config/IaC references and what each establishes.

**Attack or failure scenario**

Explain the minimum realistic sequence required to trigger the issue.

**Root cause**

Describe the design or implementation mistake, not merely the symptom.

**Recommended remediation**

Provide the safest practical change.

**Preferred long-term remediation**

Include only if materially different.

**Verification**

State exact unit/integration/security/runtime checks that demonstrate the fix.

**Operational follow-up**

State whether to rotate credentials, search logs, repair data, revoke tokens, invalidate
sessions, rebuild artifacts, or investigate historical exposure.

**Residual risk**

Explain what remains after the proposed fix.

**References**

Map to ASVS / OWASP / CWE / Java / GCP guidance where useful.

---

## 67. Severity Model

### Critical

Reasonable exploitation can cause one or more of:

- Remote code execution.
- Broad authentication bypass.
- Cross-tenant compromise at scale.
- Administrative privilege escalation.
- Theft of high-value credentials enabling broad cloud compromise.
- Unrestricted sensitive production data access.
- Material integrity compromise of build/deployment pipeline.
- Catastrophic financial/business action.

### High

Likely substantial confidentiality, integrity, availability, or privilege impact requiring
limited preconditions.

### Medium

Meaningful security or reliability impact with stronger preconditions, narrower scope,
or effective compensating controls.

### Low

Limited impact, defense-in-depth gap, or issue requiring unusual conditions.

### Informational

No demonstrated security defect; useful hardening, maintainability, or architecture note.

Severity must consider business context, not CWE label alone.

---

# PART O — OUTPUT CONTRACT

## 68. Required Deliverables

Create a review directory such as:

`code-review/`

Recommended files:

1. `README.md`
   - Scope.
   - Baseline.
   - Reviewed revision.
   - Environments.
   - Review limitations.
   - Status.

2. `01-executive-summary.md`
   - Overall risk posture.
   - Top risks.
   - Immediate actions.
   - Material unknowns.

3. `02-findings.md`
   - All findings ordered by severity and priority.

4. `03-security-architecture-review.md`
   - Trust boundaries.
   - Identity.
   - Authorization.
   - Tenancy.
   - Sensitive data.
   - External integrations.

5. `04-gcp-security-review.md`
   - IAM.
   - Service accounts.
   - Network.
   - Secrets.
   - GCP services.
   - Deployment security.

6. `05-data-transactions-concurrency.md`
   - Database security.
   - Transactions.
   - Idempotency.
   - Races.
   - Consistency.

7. `06-reliability-and-performance.md`
   - Timeouts.
   - Retries.
   - Backpressure.
   - DoS.
   - Resource pools.
   - Recovery.

8. `07-supply-chain-and-cicd.md`
   - Dependencies.
   - Build.
   - Containers.
   - Artifact provenance.
   - Deployment controls.

9. `08-test-gaps.md`
   - Security regression tests.
   - Failure-path tests.
   - Cloud configuration tests.

10. `09-remediation-plan.md`
    - Immediate / short-term / structural fixes.
    - Dependencies between fixes.
    - Suggested verification.

11. `10-open-questions-and-gaps.md`
    - Missing evidence.
    - Unknown runtime state.
    - Required owner answers.

12. `evidence/`
    - Source and configuration evidence ledger.

Do not create empty documents merely to satisfy this list. Combine sections when the
review is small.

---

## 69. Executive Summary Format

Summarize:

- Critical findings.
- High findings.
- Most important systemic weakness.
- Most important GCP/IAM weakness.
- Most important data-integrity/reliability weakness.
- Most important supply-chain weakness.
- Immediate containment required.
- Remediation priorities.
- Review coverage.
- Material blind spots.

Avoid false precision such as a numeric "security score" unless the user requests one and
the scoring method is defined.

---

## 70. Remediation Priorities

Use:

### P0 — Immediate containment
Active or easily exploitable critical risk. May require disabling functionality,
restricting access, rotating credentials, or blocking exposure before a code fix.

### P1 — Urgent
High-risk defect to remediate before the next normal release or on an accelerated release.

### P2 — Planned
Meaningful medium-risk issue for scheduled remediation.

### P3 — Hardening
Low-risk or defense-in-depth work.

Do not equate severity automatically with implementation order. A lower-severity issue
may be fixed first if it is a prerequisite or extremely low effort.

---

# PART P — REVIEW QUESTIONS TO ASK OWNERS

## 71. Ask Only Material Questions

Do not stall the review waiting for every answer. Continue static analysis and record
unknowns.

Ask when necessary:

### Runtime

- Which GCP compute product runs each component?
- Which GCP project(s) correspond to production, staging, and development?
- Which service account runs each component?
- Is the service publicly reachable, load-balancer-only, internal, or IAP protected?
- Which regions are active?

### Identity

- Who authenticates users?
- Is access token or ID token expected at each boundary?
- Are there machine-to-machine callers?
- Are there administrator/support impersonation features?

### Tenancy

- Is the system multi-tenant?
- What field or identity defines tenant ownership?
- Are any database/schema/bucket resources shared across tenants?

### Data

- Which data is considered sensitive or regulated?
- Is payment card data ever handled directly?
- What is the system of record?
- What are retention/deletion requirements?

### Integrations

- Which external calls have irreversible side effects?
- Which providers support idempotency?
- Which webhooks are externally reachable?
- What is the expected reconciliation mechanism?

### Operations

- How are secrets injected?
- How are production deployments approved?
- What backup restore has actually been tested?
- Which alerts page an operator?
- Is production infrastructure fully represented by IaC?

### Authorization to test

- May builds/tests be executed?
- May containers be built?
- May local static analyzers be run?
- May dependency vulnerability scans be run?
- May staging endpoints be probed?
- May GCP configuration be read using `gcloud`?
- May production configuration be inspected read-only?

---

# PART Q — TOOLING GUIDANCE

## 72. Static Tools

When locally available and authorized, tools may supplement manual review:

- Compiler warnings.
- Maven/Gradle dependency reports.
- OWASP Dependency-Check.
- `jdeps`.
- SpotBugs.
- FindSecBugs.
- Error Prone.
- Semgrep.
- CodeQL.
- Trivy.
- Grype.
- Syft.
- Gitleaks or equivalent secret scanning.
- Checkov.
- tfsec.
- kube-linter.
- kube-score.
- Hadolint.
- Google Cloud security posture tooling where available.

Tool output is evidence to investigate, not an automatic finding.

Manually verify reachability, exploitability, context, compensating controls, and false
positives.

Do not install tools without authorization.

---

# PART R — REVIEW ANTI-PATTERNS

## 73. Do Not

- Do not produce a giant checklist with no source evidence.
- Do not mark every dependency CVE as exploitable.
- Do not claim a vulnerability from a grep hit alone.
- Do not ignore GCP because "cloud security is infrastructure."
- Do not assume internal traffic is trusted.
- Do not assume service-account keys are necessary.
- Do not treat encryption at rest as sufficient cryptographic review.
- Do not recommend disabling TLS verification to solve connectivity.
- Do not suggest wildcard CORS as a fix.
- Do not suggest `permitAll` to bypass authentication problems.
- Do not suggest broad IAM roles as a quick fix.
- Do not recommend retries without checking idempotency.
- Do not recommend distributed locks before checking database-native atomicity.
- Do not recommend a WAF as a substitute for fixing application authorization.
- Do not report framework defaults without checking the effective version/config.
- Do not claim production configuration from `application.yml` defaults alone.
- Do not expose secrets in findings.
- Do not run exploits against production without explicit authorization.
- Do not claim compliance from source review alone.
- Do not bury Critical/High findings until the end.

---

# PART S — COMPLETENESS GATES

## 74. Review Is Not Complete Until

Within the agreed scope:

1. All deployable units are identified.
2. All externally reachable entry points are inventoried.
3. All sensitive mutations have authorization reviewed.
4. Tenant isolation is traced where applicable.
5. Secret sources and runtime identities are identified.
6. GCP IAM for runtime components is reviewed or listed as unavailable.
7. Database transaction and duplicate behavior is reviewed for critical workflows.
8. External side effects are reviewed for timeout/retry/idempotency behavior.
9. Async consumers and scheduled jobs are reviewed.
10. File and dynamic execution paths are reviewed where present.
11. Dependency and build configuration are reviewed.
12. Container/deployment configuration is reviewed.
13. CI/CD privilege and credential paths are reviewed or listed as unavailable.
14. Logging/redaction and security detection are reviewed.
15. Backup/recovery claims are separated from actual evidence.
16. Every Critical/High finding has a concrete remediation and verification plan.
17. Remaining blind spots are listed explicitly.

Use the phrase **"complete against the inspected evidence"** only when these gates are
satisfied for the agreed scope.

Never imply that static code review proves production security.

---

# PART T — BEGIN REVIEW

## 75. Start Here

1. Read repository instructions.
2. Establish revision and scope.
3. Read existing application-discovery outputs if present.
4. Inventory deployment units and entry points.
5. Identify authentication, authorization, tenant, secret, database, and GCP identity boundaries.
6. Review the highest-risk externally reachable mutation end-to-end.
7. Report any Critical or High issue immediately.
8. Continue across the attack surface.
9. Review GCP/IaC/CI/CD configuration.
10. Perform cross-cutting risky-API searches.
11. Reconcile findings and coverage.
12. Leave an exact continuation checkpoint if the review spans sessions.

Do not return only a plan. Produce concrete findings from the available evidence in the
first review session.

---

## 76. Continuation Prompt

Continue the Java + GCP server security code review. Read the existing `code-review/`
README, findings, evidence ledger, remediation plan, open questions, and coverage state.
Check whether the repository revision or infrastructure configuration changed. Revalidate
security-sensitive assumptions affected by changes. Resume the next highest-risk
unreviewed area rather than restarting. Report newly discovered Critical or High findings
immediately and update coverage before ending.

---

## 77. Final Review Prompt

Perform a final completeness and adversarial pass over the Java + GCP application review.

Specifically search for:

- Unreviewed public or privileged entry points.
- Authorization bypass.
- Cross-tenant leakage.
- Authentication confusion.
- SSRF.
- Injection.
- Unsafe deserialization.
- Command execution.
- Secret leakage.
- Excessive IAM.
- Service-account impersonation paths.
- GCP metadata credential exposure.
- Public cloud resources.
- Transaction crash windows.
- Race conditions.
- Duplicate external effects.
- Retry amplification.
- Unbounded resource consumption.
- Sensitive logging.
- Missing auditability.
- Supply-chain compromise paths.
- Unsafe CI/CD trust boundaries.
- Stale or vulnerable runtime/dependencies.
- Deployment or migration risks.
- Unsupported claims in the review itself.

For each discovered issue, verify evidence, severity, confidence, remediation, and
regression test. Remove unsupported findings or downgrade confidence when evidence does
not justify the claim.

State final coverage and blind spots explicitly.

