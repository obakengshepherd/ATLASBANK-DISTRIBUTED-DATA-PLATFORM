# Architecture

**Status:** The Product Owner has accepted this document as part of the Phase 0 foundation baseline. The layered-monolith direction is the accepted initial architectural direction. Detailed boundaries and technology selections remain proposals unless explicitly marked approved in docs/DECISIONS.md.

## 1. Architectural drivers

The design must support reliable ingestion, explicit data-integrity rules, traceability, safe retries, reconciliation, currency-aware reporting, and automated verification. It must remain understandable to a small developer team and reproducible on a local machine.

Correctness and clarity take priority over premature distribution or infrastructure breadth.

## 2. Recommended initial shape

**Recommendation: a layered monolith backed by PostgreSQL.**

A single ASP.NET Core application can expose the initial API and coordinate application use cases while keeping domain rules independent from HTTP and database implementation details.

Conceptual flow:

    Synthetic source
          |
          v
    ASP.NET Core API
          |
          v
    Application use cases
          |
          v
    Domain rules and policies
          |
          v
    Infrastructure / data access
          |
          v
       PostgreSQL

Cross-cutting concerns such as structured logging, configuration, diagnostics, and authentication/authorisation boundaries are applied where justified. They should not obscure the core transaction flow.

## 3. Logical responsibilities

### API / entry boundary
- Accept requests from synthetic clients.
- Apply transport-level checks and request limits.
- Translate requests into application commands.
- Return explicit, documented outcomes.
- Avoid embedding domain decisions in controllers or endpoint handlers.

### Application
- Orchestrate ingestion, validation, processing, persistence, reconciliation, and reporting use cases.
- Define transaction boundaries and coordinate domain policies.
- Keep use cases explicit and testable.
- Translate domain outcomes into application results.

### Domain
- Own core concepts, identity rules, validation policies, idempotency semantics, lifecycle rules, and reconciliation decisions.
- Avoid dependency on ASP.NET Core, database libraries, or infrastructure concerns.
- Express invariants in a way that can be tested.

### Infrastructure / data access
- Implement PostgreSQL persistence and SQL queries.
- Manage database transactions and concurrency controls.
- Provide migrations and database connectivity.
- Implement technical integrations behind application-facing abstractions only where a real boundary is useful.

### Reporting
Reporting may initially be implemented as application queries and SQL projections rather than a separate service or data warehouse. The reporting model must preserve currency and time-period semantics.

## 4. Technology proposal

| Concern | Proposed choice | Rationale / status |
|---|---|---|
| Language | C# | Aligns with the project's backend engineering direction. |
| Runtime | .NET 10 LTS | Current LTS baseline proposed for a new project. |
| HTTP API | ASP.NET Core | Mature first-party .NET API stack. |
| Relational database | PostgreSQL | Strong transactional and relational capabilities. |
| Database driver | Npgsql | PostgreSQL provider for .NET. |
| SQL access | Dapper plus explicit SQL | Keeps query behaviour visible and supports deliberate SQL practice. |
| Testing | xUnit (proposed) | Established .NET testing framework. |
| Local environment | Docker Compose if useful | Reproducible local database setup without requiring cloud infrastructure. |
| Source control | Git and GitHub | Repository is the source of truth. |
| API documentation | OpenAPI/Swagger tooling | Useful for inspecting and demonstrating the API when the API exists. |

This is a proposal, not a record of installed dependencies or implemented configuration. Dependency versions and support status must be checked at implementation time.

Dapper is preferred initially to keep SQL and relational behaviour explicit. An ORM should not be added by default; reconsider only if the model and development needs justify it.

## 5. Why not microservices initially?

The current requirements do not demonstrate a need for independently deployed services, a distributed transaction strategy, service discovery, or a message broker. Splitting the first version would add operational and consistency complexity before the core domain rules are proven.

A modular monolith can preserve internal boundaries. An asynchronous worker or broker may be considered later if measured throughput, isolation, back-pressure, or independent scaling requirements make it necessary.

## 6. Data integrity and transaction strategy

Detailed database design is not yet approved. The implementation must nevertheless preserve these principles:

- Use database constraints as a final line of defence for identity and relationship invariants.
- Define idempotency keys and uniqueness scope explicitly.
- Use transactions for related writes that must succeed or fail together.
- Design concurrency handling rather than relying on a check-then-insert sequence alone.
- Preserve original inputs separately from normalised/accepted representations where required.
- Use exact numeric/decimal storage for monetary values.
- Store currency explicitly with monetary amounts.
- Make retry behaviour safe and observable.
- Avoid silently overwriting conflicting source data.

The exact tables, indexes, isolation levels, lock strategy, and transaction boundaries are deferred to detailed data design.

## 7. Processing and failure handling

The design must distinguish:
- Request receipt from completed processing.
- Validation failure from infrastructure failure.
- A repeat submission from a conflicting identity.
- A technical attempt result from a business transaction outcome.
- Persistence from reconciliation.

Failure categories, retryability, and retry limits must be defined before production-like retry behaviour is implemented. The first version should prefer explicit, bounded behaviour over hidden automatic retries.

## 8. Security baseline

Even with synthetic data:
- Treat all incoming data as untrusted.
- Validate and constrain request sizes and field values.
- Keep credentials and connection strings out of source control.
- Use configuration suitable for local development and document secret handling.
- Avoid logging credentials or unnecessary payload contents.
- Parameterise SQL; do not concatenate untrusted values into queries.
- Apply least privilege to database credentials where practical.
- Define authentication and authorisation requirements before exposing sensitive operational actions.

This project does not claim compliance with banking security or regulatory standards.

## 9. Observability

The system should provide structured diagnostic context for batches, records, processing attempts, and reconciliation runs. Logs should support correlation across stages without exposing secrets. Metrics and health checks should be introduced when their operational purpose is clear.

## 10. Proposed repository organisation

The repository is currently documentation-only. The following is a candidate implementation structure, not a directory tree that already exists:

    src/
      AtlasBank.Api/
      AtlasBank.Application/
      AtlasBank.Domain/
      AtlasBank.Infrastructure/
    tests/
      AtlasBank.UnitTests/
      AtlasBank.IntegrationTests/
    database/
      migrations/
      seed/
    infrastructure/
      local/

The exact project and folder structure will be confirmed during the technical foundation phase. Avoid creating empty or speculative folders.

## 11. Architecture decisions and status

| Decision | Current position | Status |
|---|---|---|
| Initial topology | Layered monolith | Proposed direction; detailed boundaries to be confirmed |
| Primary database | PostgreSQL | Proposed; subject to technical review/approval |
| Runtime | .NET 10 LTS | Proposed; verify support and approve implementation baseline |
| SQL access | Npgsql + Dapper | Proposed; subject to technical review/approval |
| Automated tests | Unit and integration tests | Required; framework proposed |
| Messaging | No broker initially | Deferred; introduce only with evidence |
| Cloud deployment | Not in initial scope | Deferred |
| Microservices | Not justified initially | Not part of initial scope; revisit only with evidence and approval |

Material changes to topology, stack, security boundaries, or data-integrity strategy require Product Owner approval. The Product Owner's acceptance of the foundation documentation confirms this document as the design baseline for further review; it does not mean every proposal in this document has been finally approved. In particular, acceptance of the layered-monolith direction does not approve the final solution/project boundaries, API contract, database design, processing model, or security implementation. The technology choices in Section 4 remain proposed pending specialist review and explicit Product Owner approval. The status of each material decision must be reflected in docs/DECISIONS.md.

## 12. FNEOS architecture review and implementation gate

Before Phase 1 technical implementation begins, ChatGPT will prepare a bounded architecture and technical-foundation review assignment for Claude. Claude will independently assess the accepted requirements and this architecture proposal, identify omissions and technical risks, and return findings without modifying repository files. ChatGPT will reconcile the findings and present material choices to the Product Owner. Only approved decisions will be recorded as accepted in docs/DECISIONS.md. After approval, ChatGPT will prepare the technical implementation plan and assign a specific code-only increment to Claude. No solution creation, dependency installation, database setup, or application implementation is authorised merely by the Phase 0 foundation acceptance.

## 13. Open architecture decisions

- Exact solution and project boundaries.
- API contract and batch submission semantics.
- Synchronous versus asynchronous processing boundary.
- Source identity and idempotency storage design.
- Concurrency and transaction strategy.
- Migration tooling and database lifecycle.
- Authentication and authorisation for operational endpoints.
- Logging, metrics, and health-check scope.
- Retention and data deletion strategy.
