# AtlasBank — Distributed Data Platform

**Status:** Phase 0 accepted; Phase 1 technical baseline approved; implementation not yet started  
**Project type:** Synthetic financial data engineering portfolio system  
**Primary specialisation:** Data Engineering

AtlasBank is a portfolio project for designing and building a reliable financial data platform. It will accept synthetic transaction records from external sources, preserve and validate incoming data, apply controlled processing and idempotency rules, persist accepted records, support reconciliation, and produce currency-aware operational and analytical outputs.

AtlasBank is not a real banking platform. It will not connect to real financial institutions, process real payments, or represent the internal systems or operating procedures of any bank.

## Why this project exists

The project demonstrates practical data engineering and backend engineering capability through a coherent financial-data workflow. Its emphasis is on correctness, traceability, data integrity, recoverability, and explainable engineering decisions—not on infrastructure complexity for its own sake.

## Intended capabilities

- Ingest synthetic financial transaction records and batches.
- Preserve incoming records and their source context.
- Validate and normalise transaction data.
- Distinguish repeat submissions, duplicate records, and conflicting identities.
- Apply idempotent processing and reliable persistence.
- Record rejection reasons, processing attempts, and lifecycle history.
- Reconcile platform records against synthetic source records.
- Produce reports that keep currencies separate unless an explicit conversion basis is defined.
- Expose useful operational status, failure, and audit information.

These are intended capabilities, not claims that the functionality has already been implemented.

## Domain guardrails

- Ingestion is not settlement.
- Validation is not authorisation.
- Persistence is not reconciliation.
- Duplicate detection is not the same as idempotency.
- A technically successful processing attempt does not necessarily mean a business transaction succeeded.
- Monetary values use exact decimal representations; binary floating-point is not suitable for authoritative money amounts.
- Amounts in different currencies must not be summed as though they were one currency.
- Original input and the reasons for processing decisions must remain traceable.

## Approved technical foundation

The Product Owner has approved C#/.NET 10 LTS, ASP.NET Core, PostgreSQL 18.x, Npgsql, Dapper with explicit parameterised SQL, DbUp PostgreSQL migrations, xUnit, and Docker Compose for local development. The application is a four-project layered monolith with separate unit and integration test projects. See [docs/DECISIONS.md](docs/DECISIONS.md) and [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) for the approved baseline and boundaries.

No broker, microservice topology, cloud platform, Kubernetes, Redis, EF Core, or production deployment is part of the Phase 1 foundation.

## Architecture direction

The initial design is a layered monolith with clear domain, application, and infrastructure boundaries, backed by PostgreSQL. This keeps the first implementation understandable and testable while leaving room to introduce asynchronous processing or independently deployed components only if demonstrated requirements justify them.

## Documentation map

| Document | Purpose |
|---|---|
| [Project Charter](docs/PROJECT_CHARTER.md) | Purpose, stakeholders, scope, governance, and success |
| [Requirements](docs/REQUIREMENTS.md) | Business, functional, non-functional, and constraint requirements |
| [Domain Model](docs/DOMAIN_MODEL.md) | Domain language, conceptual entities, rules, and invariants |
| [Architecture](docs/ARCHITECTURE.md) | Approved technical foundation, boundaries, technology, and key decisions |
| [Implementation Roadmap](docs/IMPLEMENTATION_ROADMAP.md) | Phases, deliverables, dependencies, and verification gates |
| [Acceptance Criteria](docs/ACCEPTANCE_CRITERIA.md) | Observable conditions for accepting project capabilities |
| [AI Collaboration](docs/AI-COLLABORATION.md) | FNEOS roles, three-stage workflow, ownership, and review rules |
| [Decision Register](docs/DECISIONS.md) | Accepted decisions, proposals, approvals, and superseded arrangements |
| [AI Handoff](docs/AI-HANDOFF.md) | Current verified coordination state, assignments, and next authorised action |

## Project status

The Product Owner has accepted Phase 0 and approved the Phase 1 technical baseline. **Phase 1 — Repository and Technical Foundation — is authorised; implementation has not yet started.** The repository currently contains the project documentation only. No application code, database schema, API implementation, migrations, or local infrastructure has been implemented or verified.

See [docs/IMPLEMENTATION_ROADMAP.md](docs/IMPLEMENTATION_ROADMAP.md) for milestone status and the next planned step.

## Working principles

1. Prefer correctness and clarity over unnecessary complexity.
2. Preserve data provenance and make processing decisions explainable.
3. Design for failure, retries, and safe reprocessing.
4. Make important integrity rules enforceable and testable.
5. Keep documentation aligned with the actual repository state.
6. Never describe planned or unverified capabilities as implemented.
7. Follow the mandatory three-stage FNEOS workflow documented in [AI Collaboration](docs/AI-COLLABORATION.md).
