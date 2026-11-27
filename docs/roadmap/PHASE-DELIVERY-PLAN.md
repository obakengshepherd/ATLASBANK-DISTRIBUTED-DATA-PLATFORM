# AtlasBank Phase Delivery Plan

**Status:** Planning baseline for Phases 1–8  
**Authority:** Product Owner-approved scope and decisions in `docs/DECISIONS.md`  
**Rule:** Only the currently authorised phase may be implemented.

This document expands the implementation roadmap into phase-level work packages. It is a planning baseline, not an implementation claim. Detailed specifications are produced immediately before each phase is authorised. Later phases must not be implemented early.

## Delivery principles

- Preserve the accepted Phase 0 foundation and repository history.
- Keep one deployable layered monolith until evidence and Product Owner approval justify otherwise.
- Treat each phase as a sequence of bounded increments with explicit entry conditions, outputs, tests, and exit gates.
- Use synthetic data only. No real payment execution, bank integrations, customer data, or production banking claims.
- Database constraints and transaction semantics must enforce critical invariants; application checks alone are insufficient.
- Do not claim a capability is verified without actual test or runtime evidence.
- Phase-specific unresolved decisions must be closed before the affected behaviour is implemented.

## Phase 0 — Foundation and domain design

**Status: ACCEPTED**

Purpose: establish purpose, boundaries, requirements, domain vocabulary, initial architecture direction, governance, and acceptance criteria.

Baseline evidence:
- Project charter, requirements, conceptual domain model, architecture, decision register, roadmap, acceptance criteria, and FNEOS collaboration rules.
- Product Owner acceptance recorded in the existing project documentation.

Exit: accepted foundation remains authoritative. Reopen only where new evidence or an approved change requires it.

## Phase 1 — Repository and technical foundation

**Status: AUTHORISED; implementation not yet verified**

Objective: establish a reproducible .NET 10 / PostgreSQL 18 development foundation without implementing transaction business behaviour.

Planned increments:
1. Solution and project skeleton; package/version pinning; project-reference boundaries.
2. Local PostgreSQL Compose service; secret-safe configuration; database connection.
3. DbUp migration runner and minimal technical migration journal.
4. Liveness and database-readiness endpoints.
5. xUnit unit/integration test wiring and developer setup documentation.

Deliverables:
- Four application projects: Api, Application, Domain, Infrastructure.
- Separate UnitTests and IntegrationTests projects.
- SQL migration directory and repeatable DbUp execution.
- Docker Compose local PostgreSQL service pinned to a supported 18.x patch.
- .NET User Secrets/environment-variable configuration with safe committed examples only.
- Liveness and database readiness diagnostics.
- Clean-checkout build, run, migration, and test instructions.

Explicit exclusions:
- No transaction, batch, source, idempotency, rejection, lifecycle, reconciliation, or reporting business schema.
- No ingestion or business API endpoints.
- No retry engine, authentication product, broker, microservices, cloud resources, Kubernetes, Redis, EF Core, Testcontainers, or production deployment.

Verification:
- Restore and clean build succeed from repository root.
- Compose database becomes healthy and is reachable from the application.
- Liveness is independent of database availability.
- Readiness fails when the database is unavailable and succeeds when reachable.
- DbUp applies the technical migration; a second run makes no additional schema change.
- Unit and integration tests run against the actual local PostgreSQL service.
- No secrets are tracked.
- A clean checkout can follow the documented setup.

## Phase 2 — Transaction ingestion and provenance

**Entry:** Phase 1 accepted; detailed source and batch contract approved.

Objective: receive synthetic source submissions and preserve exactly what arrived and its context.

Design decisions to close before implementation:
- Source registration and source namespace semantics.
- Batch identity and request identity.
- Payload format/version and request size limits.
- Whether a batch is all-or-nothing or supports partial receipt; do not infer transaction acceptance from batch receipt.
- Raw payload representation, canonical encoding/hash needs, retention assumptions, and safe inspection access.
- Transport-level malformed request handling versus domain validation (Phase 3).

Deliverables:
- Source system model and source configuration contract.
- Batch submission API contract and request/response examples.
- Raw record capture with source, batch, receipt timestamp, and payload provenance.
- Receipt-level status and traceability queries.
- Deterministic synthetic source fixtures and sample batches.
- Database design/migrations for source, batch, and raw intake records, after design approval.

Verification:
- Valid transport requests create traceable receipt records.
- Each raw record is associated with exactly one source and batch.
- Original submitted content is retrievable or inspectable according to the approved access policy.
- Malformed transport input produces stable, documented errors.
- Batch receipt is never presented as successful transaction processing.
- Repeated batch submissions have explicitly defined behaviour.

## Phase 3 — Validation and normalisation

**Entry:** Phase 2 accepted; validation catalogue and supported transaction contract approved.

Objective: turn raw records into validated, normalised candidates while preserving the original.

Design decisions to close:
- Supported transaction types and mandatory/optional fields.
- Currency code support and amount precision/scale/rounding policy.
- Timestamp/time-zone interpretation.
- Normalisation rules that do not alter business meaning.
- Validation rule identifiers, versioning, and rejection reason taxonomy.
- Whether a record can be re-evaluated under a later rule version.

Deliverables:
- Versioned validation rule catalogue.
- Domain validation and normalisation policies.
- Structured validation outcome and rejection reason model.
- Normalised candidate representation.
- Boundary, malformed, and multi-error test fixtures.
- Database changes only for the approved validation outcomes.

Verification:
- Invalid records receive one or more stable, actionable reason codes.
- Valid records normalise deterministically.
- Original input remains unchanged and traceable.
- Currency and monetary boundaries are enforced.
- Validation is not described as authorisation or settlement.
- Tests cover valid, invalid, boundary, and rule-version cases selected for the phase.

## Phase 4 — Identity, idempotency, conflict handling, and reliable persistence

**Entry:** source identity, content-equivalence rules, transaction semantics, and persistence policy approved.

Objective: ensure repeat requests do not create unintended effects and conflicting identity reuse is never silently overwritten.

Design decisions to close:
- Source-scoped transaction identity and uniqueness constraints.
- Idempotency key scope, retention, and replay response.
- Definition of materially equivalent content and canonical fingerprinting, if used.
- Conflict classification and manual-resolution posture; automatic resolution is not assumed.
- Atomic write boundaries across accepted transaction, idempotency evidence, and processing outcome.
- Concurrency strategy using PostgreSQL constraints/locking/transaction semantics.
- Monetary precision and currency constraints.

Deliverables:
- Approved physical relational schema and ERD.
- Source-scoped unique constraints and supporting indexes.
- Persistence/repository operations with explicit SQL.
- Idempotency decision and conflict outcome model.
- Transactional persistence and safe repeat behaviour.
- Concurrency and injected-failure tests.

Verification:
- Equivalent repeat submissions do not create additional accepted business effects.
- Same source identity with materially changed content is recorded as a conflict.
- Identical external IDs from different sources do not collide.
- Concurrent equivalent submissions preserve invariants.
- Related writes commit or roll back consistently.
- No conflicting source content is silently overwritten.
- Database constraints, not only application pre-checks, protect uniqueness.

## Phase 5 — Processing lifecycle, attempts, and observability

**Entry:** accepted persistence and outcome semantics from Phase 4.

Objective: make record processing history, failure classification, and recovery observable.

Design decisions to close:
- Lifecycle state machine and permitted transitions.
- Attempt identity and correlation identifiers.
- Retryable versus terminal failure taxonomy.
- Retry owner, bounded retry policy, and backoff only where justified.
- Audit-event scope and retention.
- Log redaction and operational access boundaries.

Deliverables:
- Processing attempt and lifecycle history model.
- Explicit state transition rules.
- Structured logs correlated by batch, raw record, transaction, and attempt.
- Operational query/API surface for status and failure inspection.
- Bounded retry/recovery behaviour only if approved by the failure model.
- Failure injection and recovery scenarios.

Verification:
- A record's lifecycle can be reconstructed from persisted evidence.
- Invalid transitions are rejected.
- Transient and terminal failures are distinguishable.
- Retries do not bypass idempotency or multiply effects.
- Logs contain useful correlation context and no secrets.
- Technical attempt status remains distinct from business transaction status.

## Phase 6 — Reconciliation

**Entry:** accepted source and transaction identity, persistence, and lifecycle semantics.

Objective: compare the platform's accepted dataset against an independent synthetic source dataset or source control totals.

Design decisions to close:
- Reconciliation scope and source snapshot semantics.
- Matching keys and treatment of repeated source records.
- Exact versus tolerance-based amount comparisons.
- Currency matching and precision rules.
- Time-window and late-arriving record policy.
- Control-total definitions and unmatched/discrepant classifications.
- Re-run and historical result behaviour.

Deliverables:
- Reconciliation input contract and immutable run scope.
- Matching and discrepancy rules.
- Reconciliation run and result schema.
- Matched, source-only, platform-only, and discrepant outcomes.
- Known-answer synthetic datasets and independent expected results.
- Reconciliation query/API contract.

Verification:
- Known-answer fixtures produce exact expected match and discrepancy counts.
- Every result links to a run and its input scope.
- Amounts are compared only under explicit currency/precision rules.
- Re-running a scope has defined, traceable semantics.
- Reconciliation is distinct from ingestion and persistence.

## Phase 7 — Reporting and analytical outputs

**Entry:** accepted transaction, lifecycle, and reconciliation semantics.

Objective: produce trustworthy operational and management summaries.

Design decisions to close:
- KPI dictionary: name, business meaning, numerator/denominator, exclusions, grain, time basis, and owner.
- Reporting cut-off/time-zone semantics.
- Currency grouping and any explicitly approved FX conversion basis (no implicit conversion).
- Source/status filters and reconciliation dimensions.
- Query performance expectations and pagination/export limits.

Deliverables:
- KPI/metric dictionary.
- Currency-aware SQL projections and application queries.
- Operational and management reporting API contracts.
- Reconciliation and processing summaries.
- Sample responses and independently calculated expected totals.

Verification:
- Totals reconcile to underlying records and known-answer fixtures.
- Different currencies remain separate unless a traceable conversion policy is explicitly approved.
- KPI definitions and exclusions are documented.
- Time periods and filters are deterministic.
- Reports do not imply business outcomes unsupported by the data.

## Phase 8 — Engineering hardening and demonstration

**Entry:** Phases 1–7 accepted or explicitly scoped with Product Owner-approved exclusions.

Objective: demonstrate the complete system reproducibly and honestly.

Deliverables:
- End-to-end synthetic scenarios and deterministic data generation.
- Failure/retry/conflict/reconciliation demonstrations.
- Security and configuration review.
- Query and workload performance baseline with reproducible method.
- Clean-environment setup and demonstration guide.
- Architecture, limitations, and known-issues review.
- Final evidence index linking requirements, tests, and outcomes.

Verification:
- A clean environment reproduces the demonstration.
- Automated tests run and actual results are recorded.
- Performance statements include workload, environment, method, and measured result.
- Secrets are absent from repository and logs.
- Non-production limitations are explicit.
- Product Owner accepts the final demonstration.

## Cross-phase traceability

| Capability | Primary phase | Supporting phases |
|---|---:|---|
| Reproducible application foundation | 1 | 8 |
| Source/batch intake and provenance | 2 | 3, 5 |
| Validation and normalisation | 3 | 2, 4 |
| Identity, idempotency, conflict and persistence | 4 | 2, 3, 5 |
| Lifecycle, attempts and recovery | 5 | 4, 8 |
| Reconciliation | 6 | 2, 4, 5 |
| KPI and currency-aware reporting | 7 | 4, 6 |
| End-to-end evidence and demonstration | 8 | 1–7 |

## Change and release gate

A later phase may be specified while an earlier phase is active, but it may not be implemented until its entry conditions are met and the Product Owner authorises it. Any material change to scope, topology, technology, security, identity, idempotency, monetary integrity, or reconciliation semantics requires Product Owner approval and a decision-register update.
