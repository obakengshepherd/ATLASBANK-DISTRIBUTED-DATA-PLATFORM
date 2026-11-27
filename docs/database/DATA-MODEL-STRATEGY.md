# Data Model and Relational Design Strategy

**Status:** Conceptual planning baseline only; no physical business schema is approved or implemented.  
**Authority:** `docs/DOMAIN_MODEL.md`, `docs/REQUIREMENTS.md`, and `docs/DECISIONS.md`.

## 1. Purpose and boundary

This document makes the expected relational shape and relationship responsibilities explicit for future design. It does not authorise business tables during Phase 1. Physical table names, columns, data types, constraints, indexes, partitioning, and transaction boundaries must be specified and approved in the relevant later-phase design.

PostgreSQL is the approved relational database. Dapper and explicit parameterised SQL are the approved data-access approach. SQL migrations are the schema source of truth.

## 2. Conceptual relationship map

The anticipated relationship direction is:

- **SourceSystem 1 → many IngestionBatch.** A batch belongs to one registered synthetic source.
- **IngestionBatch 1 → many RawTransactionRecord.** Each received raw record belongs to the batch in which it arrived.
- **SourceSystem 1 → many RawTransactionRecord** (directly or derivably through the batch). Source provenance must be unambiguous.
- **RawTransactionRecord 0..1 → accepted FinancialTransaction.** A raw record may fail validation or otherwise not become an accepted transaction. The final cardinality and correction semantics are design decisions.
- **FinancialTransaction 1 → many ProcessingAttempt / TransactionStatusHistory.** Processing attempts and state changes are historical evidence, not mutable fields that erase prior outcomes.
- **RawTransactionRecord 0..many → RejectionRecord.** Multiple validation or processing reasons may be retained where the rules require them.
- **FinancialTransaction / RawTransactionRecord → Idempotency and conflict evidence.** Identity decisions must be source-scoped and traceable. The exact ownership and uniqueness key are deferred to Phase 4.
- **ReconciliationRun 1 → many ReconciliationResult.** Each result belongs to a defined run and its immutable input scope.
- **Currency 1 → many monetary facts** (or an explicit ISO currency code on each fact). The implementation must choose one consistent representation and enforce supported code/precision rules.
- **AuditEvent** references the relevant aggregate or processing context without becoming an unbounded generic event store.

## 3. Logical data groups

| Group | Concepts | Responsibility |
|---|---|---|
| Source and intake | SourceSystem, IngestionBatch, RawTransactionRecord | Source identity, receipt context, immutable original input |
| Validated data | Normalised candidate, FinancialTransaction | Validated representation and accepted financial fact |
| Processing integrity | IdempotencyRecord, conflict outcome, ProcessingAttempt | Safe repeat handling, conflict evidence, attempt history |
| Rejection and lifecycle | RejectionRecord, TransactionStatusHistory | Explainable rejection and ordered state transitions |
| Reconciliation | ReconciliationRun, ReconciliationResult | Comparison scope, matched/unmatched/discrepant outcomes |
| Reference data | Currency and supported transaction types | Controlled code sets and semantics |
| Audit | AuditEvent | Material actions and decisions with correlation context |

## 4. Relational integrity principles

- Use primary keys for stable internal row identity.
- Use foreign keys for mandatory relationships and explicit deletion behaviour.
- Enforce source-scoped external identity with a database uniqueness constraint once the identity contract is approved.
- Do not rely on a check-then-insert application sequence as the sole concurrency control.
- Use unique constraints/indexes for idempotency keys only after their scope and retention semantics are approved.
- Use exact numeric storage for amounts; never binary floating-point for authoritative money.
- Store currency explicitly with each monetary amount and prevent invalid or unsupported codes.
- Define precision, scale, sign, rounding, and maximum amount rules before physical schema implementation.
- Preserve raw source content separately from normalised/accepted values; do not mutate original evidence during normalisation.
- Model processing attempts and lifecycle transitions as append-oriented history where appropriate.
- Make reconciliation run scope reproducible; results must identify the run and compared records/control totals.
- Choose deletion and retention behaviour deliberately; do not cascade-delete audit evidence accidentally.

## 5. Transaction boundary principles

The later physical design must identify which writes are atomic. In particular:
- Intake receipt and raw payload persistence must have explicit consistency semantics.
- Accepted transaction persistence must be atomic with the identity/idempotency evidence needed to prevent duplicate effects.
- Processing outcome and associated history must not contradict committed data.
- Rejection persistence must preserve the input and all applicable reason codes.
- Reconciliation run creation and its result set must be traceable and safely repeatable.

The exact PostgreSQL isolation level, conflict handling SQL, locking strategy, and transaction scope are Phase 4/5 design decisions, not assumptions made here.

## 6. Indexing and query design

Indexes must be derived from approved access patterns and measured queries. Likely lookup dimensions include source identity, batch, receipt time, processing status, internal transaction identity, reconciliation run, and correlation identifier. Do not create speculative indexes without a query or integrity purpose. Uniqueness constraints are integrity controls, not merely performance optimisations.

## 7. Data lifecycle and privacy posture

All data is synthetic. Nevertheless:
- Avoid placing secrets or real personal/financial data in fixtures.
- Restrict raw-payload inspection to the intended local/demo access surface.
- Define retention, redaction, and deletion semantics before implementation of operational deletion or export.
- Logs should carry identifiers and safe diagnostic fields, not indiscriminately duplicate full payloads.
- Preserve enough synthetic provenance and decision evidence to reproduce the demonstration.

## 8. Phase ownership

| Phase | Database design responsibility |
|---|---|
| 1 | Technical migration journal and connectivity proof only; no business schema |
| 2 | Source, batch, and raw-intake physical design |
| 3 | Validation outcome and rejection persistence design |
| 4 | Accepted transaction, identity, idempotency, constraints, and atomic persistence design |
| 5 | Attempts, lifecycle history, audit, and recovery evidence |
| 6 | Reconciliation run/result schema and comparison scope |
| 7 | Reporting projections and query/index review |
| 8 | Final integrity, performance, backup/reproducibility, and documentation review |

## 9. Approval rule

Before a phase creates or materially changes business schema, its ERD/data dictionary, key constraints, migration plan, transaction boundaries, and test scenarios must be documented and reviewed. Material integrity or identity decisions require explicit Product Owner approval and an entry in `docs/DECISIONS.md`.
