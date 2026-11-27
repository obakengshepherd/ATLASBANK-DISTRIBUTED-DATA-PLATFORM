# Implementation Roadmap

**Status:** Foundation accepted; implementation has not started.

The roadmap is organised around verifiable capability increments. A phase is not complete merely because its files or code have been created; the phase must pass its verification gate and be reviewed.

## Phase 0 — Foundation and domain design

**Objective:** Establish a shared understanding of the business purpose, scope, requirements, domain rules, architecture direction, and acceptance conditions.

Deliverables:
- Project charter.
- Business and functional requirements.
- Conceptual domain model and invariants.
- Architecture proposal and recorded decisions.
- Implementation roadmap.
- Acceptance criteria.

Verification gate:
- Documents agree on project boundaries and domain terminology.
- Open decisions are visible rather than silently assumed.
- Product Owner reviews and accepts the foundation.

**Status:** Accepted. The Product Owner accepted the foundation documentation as the implementation baseline. This acceptance does not automatically approve every proposed technology choice or close the architecture and detailed-design decisions that remain explicitly open.

## Phase 1 — Repository and technical foundation

**Objective:** After architecture review and Product Owner approval of the technical baseline, establish a clean, reproducible .NET and PostgreSQL development foundation.

Entry conditions:
- Phase 0 foundation remains accepted.
- Claude has completed an independent architecture review under a review-only assignment.
- ChatGPT has reconciled the review and presented material technical decisions to the Product Owner.
- The Product Owner has explicitly approved the implementation stack, solution boundaries, database approach, and any material infrastructure commitments.
- ChatGPT has recorded approved decisions and prepared a bounded implementation assignment.

Expected deliverables:
- Approved solution and project structure.
- Runtime and dependency configuration.
- Configuration and secret-handling approach.
- PostgreSQL local development environment, using Docker Compose if justified.
- Database migration approach.
- Basic health/diagnostic endpoint if appropriate.
- Unit and integration test projects.
- Developer setup instructions.

Verification gate:
- A clean checkout can be built using documented commands.
- Automated tests run successfully.
- Database connectivity and migration process are verified.
- No secrets are committed.

## Phase 2 — Transaction ingestion

**Objective:** Receive synthetic source batches and preserve the original records.

Expected deliverables:
- Source-system representation.
- Batch submission contract.
- Raw record capture and provenance.
- Receipt outcomes and basic query capability.
- Synthetic sample data.

Verification gate:
- Valid requests are received and traceable to source and batch.
- Malformed transport input is handled explicitly.
- Original record content can be inspected or retrieved under the defined access rules.

## Phase 3 — Validation and data quality

**Objective:** Apply explicit structural and domain validation and normalise accepted values.

Expected deliverables:
- Validation rule catalogue.
- Normalisation rules.
- Rejection representation and reason codes.
- Unit tests for valid, invalid, and boundary cases.

Verification gate:
- Invalid records are rejected with actionable reasons.
- Valid records are normalised consistently.
- Original input remains traceable.
- Validation is not confused with authorisation.

## Phase 4 — Idempotency and reliable persistence

**Objective:** Prevent unintended duplicate effects and persist accepted records safely.

Expected deliverables:
- Source-scoped transaction identity contract.
- Idempotency decision model.
- Duplicate/repeat and conflict handling.
- Database constraints and transaction boundaries.
- Concurrency tests and failure-path tests.

Verification gate:
- Equivalent repeat submissions do not create additional business effects.
- Conflicting identity reuse is detected and preserved.
- Concurrent submissions do not violate invariants.
- Related writes do not leave inconsistent partial state.

## Phase 5 — Processing lifecycle and observability

**Objective:** Make processing history and operational outcomes understandable.

Expected deliverables:
- Processing attempt records.
- Defined lifecycle states and transitions.
- Correlation identifiers and structured logs.
- Useful operational queries or endpoints.
- Explicit retry/failure classification.

Verification gate:
- A record's processing history can be followed.
- Failures can be investigated without relying on unstructured guesswork.
- Technical processing outcomes remain distinct from business outcomes.

## Phase 6 — Reconciliation

**Objective:** Compare platform data against independent synthetic source records or control totals.

Expected deliverables:
- Reconciliation input contract and scope.
- Matching and discrepancy rules.
- Reconciliation run and result representation.
- Matched, unmatched, and discrepant outputs.
- Tests with known expected outcomes.

Verification gate:
- Known matching and discrepancy scenarios produce expected results.
- Each result is traceable to a reconciliation run and its input scope.
- Reconciliation remains distinct from ingestion and persistence.

## Phase 7 — Reporting and analytical outputs

**Objective:** Provide trustworthy summaries and operational/management views.

Expected deliverables:
- Agreed KPI and metric definitions.
- Currency-aware totals.
- Time-period and source filters.
- Processing and reconciliation summaries.
- Documented sample reports or API responses.

Verification gate:
- Totals can be independently checked against source records.
- Different currencies are not silently combined.
- Metric definitions and limitations are clear.

## Phase 8 — Engineering hardening and demonstration

**Objective:** Improve reproducibility, reliability, test coverage, documentation, and presentation.

Expected deliverables:
- End-to-end automated scenarios.
- Repeatable synthetic data generation.
- Failure and recovery demonstrations.
- Performance baseline for an agreed workload.
- Security and configuration review.
- Final setup and demonstration guide.
- Architecture and limitations review.

Verification gate:
- A clean environment can reproduce the documented demonstration.
- Test results are recorded accurately.
- Performance statements are backed by measured evidence.
- Limitations and non-production status are explicit.

## Milestone tracking

| Milestone | Status | Evidence / exit condition |
|---|---|---|
| Repository inspection | Complete | Empty repository and default branch configuration confirmed before first commit |
| Foundation documentation | Accepted | Seven agreed documents committed and reviewed by the Product Owner |
| Foundation review | Accepted | Product Owner accepted the foundation documentation as the implementation baseline |
| Technical foundation | Not started | Approved solution, database setup, build and tests |
| Functional phases 2–7 | Not started | Phase-specific acceptance gates |
| Hardening and demonstration | Not started | Reproducible end-to-end evidence |

## Working rule

The next phase begins only when its prerequisites and approval gates are satisfied. For Phase 1, the architecture review and Product Owner technical approvals are mandatory entry gates. Under FNEOS, ChatGPT owns architecture and documentation; Claude implements only explicitly assigned code files; the Product Owner assembles and runs the code locally and supplies actual results; ChatGPT performs the return/alignment checkpoint and integrates approved changes into main. Claude does not push, commit to main, or modify ChatGPT-owned documentation. Branches are not used unless the Product Owner explicitly authorises them. If implementation uncovers a material requirement or architecture change, record the evidence and obtain the required decision before expanding scope.
