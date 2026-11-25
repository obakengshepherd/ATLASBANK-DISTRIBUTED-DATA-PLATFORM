# Domain Model

**Status:** Conceptual domain baseline. This is not a physical database schema.

## 1. Purpose

This document establishes shared language and the conceptual responsibilities that guide the design. Names below describe concepts; they do not require a one-to-one table, class, or service for every item.

## 2. Domain language

| Term | Meaning in AtlasBank |
|---|---|
| Source system | A synthetic external system that submits financial records. |
| Ingestion batch | A named submission or collection of records received from a source. |
| Raw transaction record | The original received representation, retained for traceability. |
| Normalised transaction | A validated, consistently represented transaction candidate. |
| Financial transaction | A transaction record accepted into the platform's authoritative internal dataset. |
| Source transaction identity | The identity assigned by a source, interpreted within that source's namespace. |
| Idempotency | A processing property whereby repeating an equivalent request does not create unintended additional effects. |
| Duplicate/repeat | A repeated representation or submission of an already known transaction. |
| Conflict | Reuse of an identity with content that differs in a way that violates the identity contract. |
| Rejection | A recorded decision not to accept a submitted record for the relevant processing stage. |
| Processing attempt | A particular attempt to process a record or batch. |
| Lifecycle history | An ordered record of meaningful state transitions. |
| Reconciliation | A comparison between platform records and an independent source dataset or control. |
| Audit event | A traceable record of a material system action or decision. |

## 3. Conceptual entities and responsibilities

| Concept | Responsibility |
|---|---|
| SourceSystem | Identifies a synthetic source and its identity namespace/configuration. |
| IngestionBatch | Groups a source submission and tracks receipt-level outcomes. |
| RawTransactionRecord | Preserves the received payload and its provenance. |
| FinancialTransaction | Represents an accepted transaction in the platform's internal model. |
| IdempotencyRecord | Supports recognition of repeated requests and safe processing outcomes. |
| ProcessingAttempt | Captures a processing attempt, result, timing, and relevant diagnostic context. |
| RejectionRecord | Captures a rejected record and one or more reasons. |
| TransactionStatusHistory | Captures meaningful transaction lifecycle transitions. |
| ReconciliationRun | Defines a particular comparison execution and its scope. |
| ReconciliationResult | Captures matched, unmatched, and discrepant outcomes. |
| Currency | Identifies the currency associated with a monetary amount. |
| AuditEvent | Records material actions and decisions for traceability. |

These concepts may be combined or separated during physical modelling based on access patterns, consistency requirements, and lifecycle needs.

## 4. Identity and idempotency

A source transaction identifier must be interpreted within a defined source namespace. An identifier from one source must not accidentally collide with the same identifier from another source.

The initial decision model must distinguish at least these cases:

| Incoming record | Expected interpretation |
|---|---|
| Same source identity, equivalent content | Repeat submission; do not create an unintended second transaction effect. |
| Same source identity, materially different content | Conflict; preserve evidence and apply an explicit conflict policy. |
| Different source identity | Potentially a distinct transaction; do not infer duplication from similar amounts or timestamps alone. |

Idempotency is broader than finding duplicate rows. It concerns the observable effects of processing, including persistence and related state changes. The implementation must define the idempotency key, content comparison semantics, and concurrency protection before coding this behaviour.

Detection and resolution are separate. The initial requirement is to detect and record conflicts; automatic resolution is not assumed.

## 5. Transaction lifecycle

The lifecycle must distinguish receipt, validation, processing, persistence, and reconciliation. These are different stages and should not be collapsed into a single ambiguous status.

A conceptual progression is:

1. Received — input has arrived and has an identifiable receipt context.
2. Validated or rejected — validation outcome is known.
3. Processing — the platform is applying the accepted processing rules.
4. Persisted — accepted transaction data has been committed.
5. Reconciliation evaluated — the transaction has been considered by a reconciliation run.

This is a conceptual sequence, not an approved exhaustive state machine. Detailed states, transitions, terminal conditions, retry behaviour, and whether reconciliation status belongs to the transaction or to a separate reconciliation result remain design decisions.

A rejected input may still have a valid ingestion and audit history. A processing attempt can succeed technically while a business outcome remains unsuccessful or unresolved.

## 6. Monetary and currency rules

- Monetary amounts must use exact decimal representations.
- Every monetary amount must have an explicit currency.
- Precision and scale rules must be selected for the supported domain and enforced consistently.
- Currency codes must be validated against the project's supported currency reference set.
- Values in different currencies must remain separate in totals and reports.
- No implicit FX conversion is allowed.
- If conversion is introduced later, the rate, source, timestamp, direction, precision, and rounding policy must be explicit and traceable.

## 7. Reconciliation model

Reconciliation compares platform data with an independent source representation or control. It is not the same operation as saving a transaction.

A reconciliation run should have a defined scope and identifiable input set. Its results should distinguish:

- Matched records.
- Source records with no corresponding platform record.
- Platform records with no corresponding source record.
- Records with identity matches but value or attribute discrepancies.
- Aggregate/control-total differences where such controls are supplied.

Matching keys, tolerance rules, handling of late-arriving records, and treatment of reversals or adjustments require explicit design.

## 8. Provenance and auditability

For each received record, the system should be able to explain:
- Which synthetic source submitted it.
- Which batch or request carried it.
- What original input was received.
- When it was received and processed.
- Which validation and processing decisions were made.
- Whether it was accepted, rejected, recognised as a repeat, or flagged as a conflict.
- Which persisted transaction or reconciliation result is related to it, if any.

Audit records should be useful and appropriately scoped. They must not become a place to store secrets or unnecessary sensitive data.

## 9. Domain invariants

The following invariants guide design and tests:

1. A source transaction identity is unique within its defined source namespace according to the agreed identity contract.
2. An equivalent repeat submission must not create unintended additional business effects.
3. Reuse of an identity with conflicting content must not be silently treated as an ordinary repeat.
4. A rejected record retains its rejection reason and provenance.
5. A persisted transaction must be traceable to its source input and processing history.
6. Related writes that must be consistent must commit atomically or have an explicitly designed recovery strategy.
7. A reconciliation result refers to a defined reconciliation run and input scope.
8. Currency is part of the meaning of every monetary amount.
9. No cross-currency aggregate is valid without an explicit conversion basis.
10. Technical processing status must not be confused with authorisation, settlement, or business success.

## 10. Open domain decisions

- Exact source identity contract and content-equivalence definition.
- Supported transaction categories, direction, and lifecycle.
- Reversal, correction, and adjustment semantics.
- Validation rules and rule versioning.
- Conflict handling and operator intervention.
- Currency reference data and permitted precision.
- Reconciliation matching and tolerance rules.
- Retention and privacy treatment of raw payloads and audit history.
