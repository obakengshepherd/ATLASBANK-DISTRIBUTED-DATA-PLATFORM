# Acceptance Criteria

**Status:** Foundation-level acceptance baseline. Detailed test cases will be added alongside implementation.

Acceptance criteria describe observable outcomes. They do not claim that the system currently meets them.

## 1. Capability acceptance criteria

| ID | Capability | Acceptance condition |
|---|---|---|
| AC-01 | Valid ingestion | A well-formed synthetic source submission is received and associated with its source and batch context. |
| AC-02 | Validation | Invalid records are rejected with clear, inspectable reason codes; valid records proceed according to the defined rules. |
| AC-03 | Provenance | The original received input remains traceable to the source, batch, and processing outcome. |
| AC-04 | Idempotency | Repeating an equivalent submission does not create unintended additional business effects. |
| AC-05 | Conflict detection | Reusing a source-scoped identity with materially different content is detected and is not silently accepted as an ordinary repeat. |
| AC-06 | Concurrency integrity | Concurrent equivalent submissions preserve the identity and idempotency invariants. |
| AC-07 | Atomic persistence | A failure during a set of related writes does not leave inconsistent partial state, or a documented recovery strategy restores consistency. |
| AC-08 | Lifecycle history | A transaction or input record has traceable processing attempts and meaningful lifecycle transitions. |
| AC-09 | Reconciliation | A reconciliation run identifies expected matched, unmatched, and discrepant cases from controlled synthetic input. |
| AC-10 | Currency-aware reporting | Monetary totals are grouped by currency and can be independently checked against the underlying records. |
| AC-11 | Operational investigation | A failure or rejected record can be investigated using recorded context and without exposing secrets. |
| AC-12 | Reproducibility | A developer can set up and run the project from a clean checkout using documented instructions. |

## 2. Domain-specific acceptance guardrails

- Ingestion must not be described as settlement.
- Validation must not be described as authorisation.
- A database insert must not be treated as proof of reconciliation.
- Duplicate detection alone is insufficient evidence of idempotent processing.
- A successful technical attempt must not automatically be represented as a successful business transaction.
- Different currencies must not be added together without an explicit, traceable conversion basis.
- Rejected records and conflicts must not disappear without an auditable outcome.

## 3. Planned verification scenarios

The following scenarios are candidates for automated tests and demonstration data. They are not execution results.

1. A batch containing only valid records.
2. A batch containing malformed and domain-invalid records.
3. A record with missing, malformed, or unsupported currency information.
4. Repeated submission of the same source identity with equivalent content.
5. Reuse of the same source identity with changed amount, currency, or other material content.
6. Two different sources using the same external transaction identifier.
7. Concurrent submission of equivalent records.
8. A simulated persistence failure during related writes.
9. Retry after a transient processing failure.
10. Source records with corresponding platform records.
11. Source records missing from the platform dataset.
12. Platform records absent from the source dataset.
13. Matching identities with discrepant amounts or currencies.
14. Reporting across multiple currencies and time periods.
15. Traceability from a source batch through validation, persistence, and reconciliation.

## 4. Demonstration workload

A useful demonstration may include:
- 1,000 synthetic source records.
- Repeated submissions.
- A controlled mixture of valid and invalid records.
- Conflicting identities.
- Concurrent requests.
- An interrupted or failed processing attempt.
- Source data with known reconciliation outcomes.

The exact dataset, expected counts, concurrency level, and performance targets must be defined before execution. No throughput or reliability figure is assumed in this document.

## 5. End-to-end demonstration narrative

The final demonstration should show a synthetic source submitting a batch; the platform preserving the original input; validation and normalisation; idempotency and conflict decisions; accepted persistence and rejection outcomes; lifecycle and processing evidence; reconciliation against source data; and currency-aware reporting.

A reviewer should be able to select a record and trace its path through the relevant stages. The demonstration should also show at least one failure or discrepancy and explain how it is represented.

## 6. Definition of done

A capability may be marked **IMPLEMENTED** when the code and relevant documentation exist.

It may be marked **VERIFIED** only when its applicable automated or repeatable checks have actually run and passed, with results available.

It may be marked **ACCEPTED** only after review against the relevant requirements and acceptance criteria.

For a phase to be complete:
- Scope and expected behaviour are documented.
- Relevant implementation is present.
- Appropriate tests exist and have been run.
- Results and known limitations are recorded.
- Documentation reflects actual behaviour.
- No unresolved critical integrity or security issue is hidden.
- Required Product Owner approval has been obtained.

## 7. Evidence recording

For each implemented phase, record:
- Requirement and acceptance criterion IDs addressed.
- Test names and actual execution results.
- Relevant commands and environment assumptions.
- Known defects, limitations, or deferred cases.
- Documentation updated.
- Review and acceptance status.

Never mark a criterion passed based solely on code inspection or an assumed test result.
