# Project Charter

**Project:** AtlasBank — Distributed Data Platform  
**Project classification:** Synthetic financial data engineering portfolio system  
**Primary specialisation:** Data Engineering  
**Status:** Foundation proposal

## 1. Problem statement

Financial data workflows must handle records arriving from different sources while preserving their origin, validating their content, preventing unintended repeated effects, recording failures, and producing trustworthy reconciled outputs. A useful engineering demonstration must show how these concerns fit together rather than presenting isolated CRUD endpoints or dashboards.

AtlasBank provides a controlled synthetic environment in which these concerns can be designed, implemented, tested, and explained.

## 2. Purpose

Design and build a reliable platform for ingesting synthetic financial transaction records, applying validation and processing controls, persisting traceable data, reconciling platform records with source records, and producing currency-aware reporting.

The project is intended to demonstrate practical software and data engineering judgement. It is not a real banking product or a representation of any financial institution's internal systems.

## 3. Objectives

1. Establish a clear financial-data domain model and vocabulary.
2. Accept records from synthetic external source systems.
3. Preserve original inputs and provenance.
4. Validate and normalise incoming data consistently.
5. Implement safe idempotent processing and conflict detection.
6. Persist accepted data reliably and retain rejection and processing history.
7. Support reconciliation independently of ingestion and persistence.
8. Produce accurate reports that respect currency boundaries.
9. Make operational outcomes and failures observable and explainable.
10. Demonstrate correctness through automated tests and reproducible scenarios.

## 4. Stakeholders and users

The following are synthetic stakeholder personas used to shape requirements. They do not imply access to, or knowledge of, actual bank processes.

| Persona | Interest |
|---|---|
| Data Operations | Batch intake, validation outcomes, rejected records, processing status |
| Finance Operations | Reconciliation results and trustworthy financial totals |
| Risk & Compliance | Traceability, provenance, and evidence of processing decisions |
| Data Engineering | Data integrity, ingestion reliability, recoverability, and pipeline behaviour |
| Management | Clear operational and business-level summaries |
| Technical Operations | Health, failures, diagnostics, and supportability |

## 5. Scope

### In scope

- Synthetic source systems and transaction data.
- Batch and record ingestion.
- Raw input preservation and provenance.
- Validation and normalisation.
- Idempotency, repeat submission handling, duplicate detection, and conflict detection.
- Rejection handling with actionable reasons.
- Reliable persistence and transaction lifecycle history.
- Reconciliation against synthetic source data.
- Currency-aware operational and analytical reporting.
- Processing history, auditability, and operational observability.
- Automated tests and reproducible local demonstration.

### Out of scope

- Real bank, payment network, or third-party financial integrations.
- Actual payment initiation, authorisation, clearing, or settlement.
- Real customer or account data.
- Credit decisioning, fraud scoring, or production risk models.
- FX trading or implicit currency conversion.
- Production banking controls, certifications, or regulatory claims.
- Microservices, message brokers, Kubernetes, or cloud deployment as initial requirements.
- Claims that the system is production-ready or has processed real financial activity.

## 6. Success definition

The project is successful when the implemented system can demonstrate an end-to-end, repeatable workflow in which synthetic records are received, preserved, validated, processed under explicit idempotency rules, persisted or rejected with reasons, reconciled against source data, and reported without currency ambiguity.

Success also requires evidence: automated tests, documented domain and architecture decisions, reproducible setup instructions, and a demonstration whose results can be independently checked.

## 7. Constraints and assumptions

- All financial data is synthetic and clearly labelled as such.
- The initial system is a single deployable application unless evidence justifies otherwise.
- PostgreSQL is the proposed relational store.
- Money is represented using exact decimal types and an explicit currency code.
- Source identity and transaction identity must be defined before idempotency can be implemented safely.
- No FX conversion is implied by currency-aware reporting.
- The technology proposal is subject to the architecture decision review.

## 8. Governance and decision rights

The user is the Product Owner and final decision-maker for scope, priorities, and material project decisions. The AI collaborator supports discovery, architecture, implementation, testing, debugging, and documentation.

Routine implementation choices may be made within approved requirements and architecture. Approval is required before material changes to scope, architecture, stack, security boundaries, data-integrity rules, major dependencies, or infrastructure commitments.

The repository is the project source of truth. Decisions and status must be recorded there when they materially affect future work.

## 9. Delivery approach

Work follows the lifecycle:

**DISCOVER → DESIGN → BUILD → VERIFY → REVIEW → RECORD**

Implementation proceeds in small, reviewable increments. A capability is not considered complete merely because code exists; it must meet its acceptance conditions and have appropriate verification evidence.

## 10. Current milestone

**Foundation Documentation**

- Repository inspection: complete.
- Foundation documentation: being established.
- Application code: not started.
- Database schema: not started.
- API implementation: not started.
- Infrastructure: not started.
- Foundation review and acceptance: pending.
