# Decision Register — AtlasBank

**Status:** Active decision record  
**Authority:** Product Owner  
**Last updated:** 2026-11-27

This register distinguishes accepted project decisions, accepted initial directions, proposals awaiting approval, and deferred choices. A proposal must not be treated as approved merely because it appears in an architecture document.

## Decision status definitions

- **ACCEPTED:** Explicitly approved by the Product Owner.
- **ACCEPTED INITIAL DIRECTION:** Approved as the starting direction; detailed design remains open.
- **PROPOSED:** Recommended but not approved for implementation.
- **DEFERRED:** Deliberately postponed; do not implement unless reconsidered.
- **SUPERSEDED:** Replaced by a later decision or governance instruction.

## Recorded decisions

| ID | Decision | Status | Rationale / boundary |
|---|---|---|---|
| AB-D-001 | AtlasBank is a synthetic financial data engineering portfolio platform, not a real banking or payment system. | ACCEPTED | Defines the business purpose and limits all claims and integrations. |
| AB-D-002 | Phase 0 foundation documentation is accepted as the implementation baseline. | ACCEPTED | Does not imply that every proposed technical selection is approved. |
| AB-D-003 | Use a layered monolith as the initial application architecture direction. | ACCEPTED INITIAL DIRECTION | Detailed project boundaries, API contracts, processing boundaries, and physical data design remain open for review. |
| AB-D-004 | Use PostgreSQL as the relational database. | ACCEPTED | Versioned by AB-D-010 for the Phase 1 baseline. |
| AB-D-005 | Use C# and .NET 10 LTS with ASP.NET Core. | ACCEPTED | Use the latest supported .NET 10 servicing patch; see AB-D-009. |
| AB-D-006 | Use Npgsql and Dapper with explicit parameterised SQL. | ACCEPTED | No ORM in the initial foundation; detailed data behaviour remains phase-scoped. |
| AB-D-007 | Use xUnit for automated tests. | ACCEPTED | Unit and integration test projects form the Phase 1 baseline. |
| AB-D-008 | Use Docker Compose for reproducible local PostgreSQL development. | ACCEPTED | Local-only development infrastructure; no cloud deployment implied. |
| AB-G-001 | Adopt the FNEOS three-stage workflow: ChatGPT architecture/documentation; Claude specialist engineering/code; Product Owner local assembly with ChatGPT alignment and integration. | ACCEPTED | Product Owner-approved operating model for AtlasBank. |
| AB-G-002 | ChatGPT owns project documentation and repository coordination. | ACCEPTED | Claude may inspect documentation but cannot modify it absent explicit Product Owner-approved ownership transfer. |
| AB-G-003 | Claude implements only explicitly assigned code files and returns complete files in chat with exact paths and verification details. | ACCEPTED | No unassigned edits or scope expansion. |
| AB-G-004 | No simultaneous file ownership; ChatGPT documentation and Claude assigned code work must not overlap. | ACCEPTED | Prevents conflicting edits and preserves clear responsibility. |
| AB-G-005 | Claude must not push directly to GitHub or commit to main. | ACCEPTED | Product Owner controls local assembly; ChatGPT coordinates integration. |
| AB-G-006 | Branch-based work is prohibited unless the Product Owner explicitly authorises it. | ACCEPTED | Branches and pull requests are not the default workflow. |
| AB-G-007 | A return/alignment checkpoint is mandatory after Claude work or local assembly and before the next assignment. | ACCEPTED | Requires current main inspection, assignment comparison, actual test-result review, and blocker identification. |
| AB-G-008 | Material changes require Product Owner approval and must be recorded in this register. | ACCEPTED | Applies to scope, architecture, stack, security, data integrity, major dependencies, and infrastructure commitments. |
| AB-G-009 | No force-push, history rewrite, or deletion of historical commits for routine corrective work. | ACCEPTED | Preserve repository history and use normal commits. |

## Open technical decisions

The following remain open for the phases that implement them:
- Source transaction identity and content-equivalence rules.
- Idempotency key and uniqueness scope.
- Concurrency and atomic persistence strategy.
- Batch partial-acceptance policy.
- Monetary precision/scale and rounding policy.
- Failure classification, retry boundaries, and recovery.
- Reconciliation matching, tolerances, and control-total policy.
- Authentication/authorisation scope for operational endpoints beyond basic health/readiness.
- Retention and deletion policy.

Routine package patch versions must be pinned by the implementation and kept within the approved .NET 10/PostgreSQL 18 baseline; this is not a new Product Owner decision.

## Decision change procedure

1. Identify the decision and evidence prompting reconsideration.
2. Describe the current accepted position and the proposed alternatives.
3. Explain business, architecture, security, data-integrity, cost, and maintenance implications as applicable.
4. Obtain explicit Product Owner approval for material changes.
5. Update this register and the relevant architecture/requirements documents.
6. Only then implement the approved change.

Historical or superseded arrangements may be documented for traceability, but must not be presented as current operating rules.


## Engineering activation approvals — 2026-11-27

The Product Owner's Engineering Activation & Workflow Directive of 27 November 2026 authorises Phase 1 and approves the following bounded technical baseline. These decisions apply to the reproducible development foundation; they do not approve later-phase business behaviour or production deployment.

| ID | Decision | Status | Rationale / boundary |
|---|---|---|---|
| AB-D-009 | Use .NET 10 LTS and ASP.NET Core for the initial application. | ACCEPTED | LTS servicing is preferred over the newly released .NET 11 STS track; keep the SDK/runtime on supported patches. |
| AB-D-010 | Use PostgreSQL 18 for local development and integration testing. | ACCEPTED | PostgreSQL 18 is a supported, established major release; pin a current 18.x patch image and update it deliberately. |
| AB-D-011 | Use Npgsql and Dapper with explicit, parameterised SQL. | ACCEPTED | Keeps PostgreSQL access explicit and aligned with the project's SQL/data-engineering objectives. No ORM is required for the foundation. |
| AB-D-012 | Use xUnit for automated tests. | ACCEPTED | Establishes the unit and integration test baseline. |
| AB-D-013 | Use Docker Compose for the local PostgreSQL service. | ACCEPTED | Provides a repeatable local database without cloud infrastructure. Docker Engine/Desktop is a developer prerequisite. |
| AB-D-014 | Use DbUp with its PostgreSQL provider for ordered SQL migrations. | ACCEPTED | Provides a migration journal and repeatable SQL deployment while retaining SQL as the source of schema changes. |
| AB-D-015 | Use a four-project layered monolith: Api, Application, Domain, Infrastructure, with separate UnitTests and IntegrationTests projects. | ACCEPTED | Makes the existing logical boundaries enforceable without introducing services or deployment complexity. |
| AB-D-016 | Use environment variables and .NET User Secrets for local configuration; commit only placeholders/examples. | ACCEPTED | Keeps credentials outside source control and supports reproducible local setup. |
| AB-D-017 | Provide liveness and database-readiness diagnostics. | ACCEPTED | Verifies process health separately from database connectivity; does not introduce business endpoints. |

### Explicit implementation boundaries

- No transaction-ingestion endpoint, domain entity model, business table, idempotency mechanism, reconciliation logic, retry engine, authentication product, or reporting capability is part of the Phase 1 foundation increment.
- A minimal technical schema/migration journal is permitted only to prove migration execution; it must not pre-empt the approved business data model.
- No EF Core, message broker, microservices, cloud resources, Kubernetes, Redis, Testcontainers, or production deployment tooling is approved for this increment.
- Package versions must be mutually compatible with .NET 10 and the selected PostgreSQL provider. Pin versions and record the actual versions used.
- PostgreSQL major version is 18; use a current supported 18.x patch. .NET remains on the latest supported .NET 10 servicing patch.

### Decisions deliberately left for later phases

Source identity details, content-equivalence/fingerprinting, batch partial-acceptance policy, monetary precision/scale policy, correction/reversal semantics, conflict resolution, retry classification, lifecycle state machine, reconciliation keys/tolerances, and operational authentication remain open. They must be resolved before the phase that implements them; they do not block the technical foundation.
