# Requirements

**Status:** Foundation baseline; detailed design remains open where noted.

This document describes what AtlasBank must support. It does not prescribe detailed implementation mechanisms; those belong in [ARCHITECTURE.md](ARCHITECTURE.md).

## 1. Business requirements

| ID | Requirement |
|---|---|
| BR-01 | Receive financial transaction records from synthetic external sources. |
| BR-02 | Validate incoming records against defined structural and business rules. |
| BR-03 | Normalise transaction data into a consistent internal representation while preserving original input. |
| BR-04 | Prevent unintended repeated processing effects when a source resubmits the same transaction. |
| BR-05 | Detect conflicting records that reuse an identity with materially different content. |
| BR-06 | Reliably persist accepted transactions and the relevant processing evidence. |
| BR-07 | Preserve rejected records and explain why they were rejected. |
| BR-08 | Track transaction processing lifecycle and status history. |
| BR-09 | Reconcile platform transaction records against source records or control totals. |
| BR-10 | Produce currency-aware reports and summaries. |
| BR-11 | Retain processing history sufficient to investigate outcomes. |
| BR-12 | Monitor pipeline activity, failures, and operational performance. |

## 2. Functional requirements

The following functional requirements refine the business requirements. They are capability statements, not claims of implementation.

### Ingestion and provenance

- FR-ING-01: The system shall accept transaction records associated with a known synthetic source.
- FR-ING-02: The system shall support grouping submitted records into an identifiable ingestion batch.
- FR-ING-03: The system shall retain the original submitted payload or an equivalent immutable representation.
- FR-ING-04: The system shall associate each received record with source, batch, receipt time, and processing outcome context.
- FR-ING-05: The system shall distinguish a batch receipt from successful transaction processing.

### Validation and normalisation

- FR-VAL-01: The system shall validate required fields, formats, and supported value constraints.
- FR-VAL-02: The system shall validate monetary amount and currency information.
- FR-VAL-03: The system shall normalise accepted values consistently without silently changing their business meaning.
- FR-VAL-04: The system shall retain one or more machine-readable rejection reasons for records that fail validation.
- FR-VAL-05: Validation shall not be represented as payment authorisation or settlement.

### Identity, duplicates, conflicts, and idempotency

- FR-ID-01: The system shall define transaction identity in the context of its source system.
- FR-ID-02: The system shall recognise a repeat submission with the same identity and equivalent content.
- FR-ID-03: The system shall detect reuse of an identity with materially different content.
- FR-ID-04: The system shall prevent a repeated request from creating unintended duplicate business effects.
- FR-ID-05: The system shall preserve evidence of duplicate/repeat and conflict decisions.
- FR-ID-06: The system shall remain correct when equivalent submissions are processed concurrently.

### Persistence and lifecycle

- FR-DAT-01: The system shall persist accepted transaction data with appropriate integrity constraints.
- FR-DAT-02: The system shall persist related processing records consistently.
- FR-DAT-03: The system shall retain rejected input and rejection reasons.
- FR-DAT-04: The system shall record processing attempts and meaningful lifecycle transitions.
- FR-DAT-05: The system shall avoid inconsistent partial state when a persistence operation fails.

### Reconciliation and reporting

- FR-REC-01: The system shall support reconciliation between platform records and synthetic source records or control totals.
- FR-REC-02: Reconciliation shall identify matched, unmatched, and discrepant records or totals.
- FR-REC-03: Reconciliation runs shall be identifiable and retain their result context.
- FR-REP-01: Reports shall show monetary amounts grouped by currency.
- FR-REP-02: Reports shall expose relevant time periods, source context, and processing status.
- FR-REP-03: Any future cross-currency conversion shall require an explicit rate, source, timestamp, and conversion policy.

### Operations and traceability

- FR-OPS-01: The system shall expose useful ingestion and processing outcomes.
- FR-OPS-02: Failures shall be recorded with enough context to support investigation without exposing secrets.
- FR-OPS-03: A transaction or source record shall be traceable across its relevant processing history.
- FR-OPS-04: The system shall provide a way to inspect reconciliation and processing outcomes.

## 3. Non-functional requirements

| ID | Quality attribute | Requirement |
|---|---|---|
| NFR-REL-01 | Reliability | Transient or repeated requests must not silently corrupt or multiply business effects. |
| NFR-DAT-01 | Integrity | Identity, monetary, and relationship constraints must be explicit and enforceable. |
| NFR-SEC-01 | Security | Inputs must be treated as untrusted; secrets must not be committed or logged. |
| NFR-AUD-01 | Auditability | Material processing decisions and lifecycle changes must be traceable. |
| NFR-PER-01 | Performance | Performance must be measured against defined scenarios before optimisation claims are made. |
| NFR-MNT-01 | Maintainability | Domain rules and infrastructure concerns must have clear boundaries. |
| NFR-TST-01 | Testability | Core rules must be testable independently and through integration tests. |
| NFR-OBS-01 | Observability | Operational outcomes and failures must be diagnosable from appropriate logs and metrics. |
| NFR-PORT-01 | Portability | A developer should be able to reproduce the local environment using documented steps. |
| NFR-REC-01 | Recoverability | The design must make safe retry and reprocessing behaviour explicit. |
| NFR-EXP-01 | Explainability | Important decisions, assumptions, and limitations must be documented. |

## 4. Constraints

- Use synthetic data only.
- No real financial integrations or payment execution.
- Do not imply settlement, authorisation, or production banking capabilities.
- Do not aggregate amounts across currencies without an explicit conversion basis.
- Use exact decimal types for monetary values.
- Preserve original input and provenance.
- Avoid introducing distributed infrastructure without a demonstrated requirement.
- Do not claim performance, availability, or production readiness without measured evidence.

## 5. Prioritisation

Initial delivery order:

1. Foundation and domain definitions.
2. Ingestion and raw input preservation.
3. Validation and normalisation.
4. Identity, idempotency, duplicate and conflict handling.
5. Reliable persistence and lifecycle history.
6. Reconciliation.
7. Reporting.
8. Operational hardening and demonstration.

This sequence is provisional and may be adjusted through an explicit decision when dependencies or test evidence require it.

## 6. Traceability

Business requirements map to capability groups as follows:

| Business requirements | Capability group |
|---|---|
| BR-01, BR-02, BR-03 | Ingestion and data quality |
| BR-04, BR-05 | Identity and processing integrity |
| BR-06, BR-07 | Persistence and rejection handling |
| BR-08, BR-11 | Lifecycle and traceability |
| BR-09 | Reconciliation |
| BR-10 | Reporting |
| BR-12 | Operations and observability |

Detailed requirement-to-test traceability will be added as test cases are designed. No test execution is implied by this foundation document.

## 7. Open requirement questions

The following require resolution during detailed design, not speculative assumptions now:

- Exact source transaction identity and source namespace rules.
- Supported transaction types and lifecycle states.
- Whether ingestion is synchronous, asynchronous, or a staged combination.
- Validation rule catalogue and versioning approach.
- Conflict resolution policy (detection is required; automatic resolution is not).
- Reconciliation matching keys, tolerances, and control-total rules.
- Retention and redaction policy for synthetic raw payloads and audit evidence.
- Quantified performance targets after a baseline workload is agreed.
