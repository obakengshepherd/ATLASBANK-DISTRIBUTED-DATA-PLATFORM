# Architecture

**Status:** Phase 0 foundation accepted; Phase 1 technical foundation baseline approved by the Product Owner on 27 November 2026. Implementation remains pending.

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

## 4. Approved technology baseline

| Concern | Approved choice | Rationale / status |
|---|---|---|
| Language | C# | Aligns with the project's backend engineering direction. |
| Runtime | .NET 10 LTS | Approved LTS baseline; use the latest supported servicing patch. |
| HTTP API | ASP.NET Core | Mature first-party .NET API stack. |
| Relational database | PostgreSQL 18.x | Approved supported major release; pin a current 18.x patch. |
| Database driver | Npgsql | PostgreSQL provider for .NET. |
| SQL access | Dapper plus explicit SQL | Keeps query behaviour visible and supports deliberate SQL practice. |
| Testing | xUnit | Approved unit and integration test framework. |
| Local environment | Docker Compose | Approved reproducible local PostgreSQL service. |
| Source control | Git and GitHub | Repository is the source of truth. |
| API documentation | OpenAPI/Swagger tooling | Useful for inspecting and demonstrating the API when the API exists. |

These are approved technical selections, not claims that dependencies are installed or the foundation is implemented. Pin mutually compatible package versions in the solution and record the actual versions. .NET 10 remains on its latest supported servicing patch; PostgreSQL uses a supported 18.x patch.

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

## 10. Approved repository organisation

The approved Phase 1 structure is:

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

These are four application projects within one layered monolith. Do not add speculative empty folders or additional deployable services. Infrastructure is the composition-independent implementation layer; Api is the HTTP/composition boundary.

## 11. Architecture decisions and status

| Decision | Current position | Status |
|---|---|---|
| Initial topology | Layered monolith | ACCEPTED INITIAL DIRECTION; four-project boundaries approved for Phase 1 |
| Primary database | PostgreSQL 18.x | APPROVED for local development and integration testing |
| Runtime | .NET 10 LTS / ASP.NET Core | APPROVED; use latest supported servicing patch |
| SQL access | Npgsql + Dapper | APPROVED; explicit parameterised SQL; no ORM in foundation |
| Automated tests | xUnit unit and integration tests | APPROVED |
| Messaging | No broker initially | Deferred; introduce only with evidence |
| Cloud deployment | Not in initial scope | Deferred |
| Microservices | Not justified initially | Not part of initial scope; revisit only with evidence and approval |

Material changes to topology, stack, security boundaries, or data-integrity strategy still require Product Owner approval. The technical foundation choices above are approved for Phase 1 only. This does not approve later-phase API contracts, transaction schema, processing model, identity/idempotency semantics, reconciliation policy, or production security controls. The status of each material decision is reflected in docs/DECISIONS.md.

## 12. Engineering activation and implementation gate

The Product Owner's 27 November 2026 directive authorises Phase 1 under the approved baseline. ChatGPT prepares one bounded implementation handoff. Claude performs one independent review of that handoff and, if no blocking issue exists, implements under the same assignment. Non-blocking issues are documented and do not create another review cycle. Claude stops only for a genuine blocker or material Product Owner decision. The Product Owner assembles and tests locally; ChatGPT performs one return/alignment checkpoint and integrates approved changes. See docs/AI-COLLABORATION.md and docs/AI-HANDOFF.md.

## 13. Open architecture decisions

The following decisions are intentionally deferred until the phase that requires them:

- Source identity and content-equivalence contract; idempotency key and uniqueness scope.
- Batch submission contract and partial-acceptance policy.
- Synchronous versus asynchronous processing boundary.
- Concurrency and atomic persistence strategy.
- Authentication and authorisation beyond the local health/readiness surface.
- Operational logging and metrics beyond the foundation diagnostics.
- Retention and deletion policy.

Migration tooling, project boundaries, local PostgreSQL, test framework, and the Phase 1 configuration/diagnostic baseline are approved in docs/DECISIONS.md.


## 14. Approved technical foundation baseline — 27 November 2026

The Product Owner has approved the following Phase 1 implementation baseline under the Engineering Activation & Workflow Directive:

- **Application platform:** C# / .NET 10 LTS / ASP.NET Core.
- **Database:** PostgreSQL 18.x, using a current supported patch release.
- **Data access:** Npgsql and Dapper with explicit parameterised SQL; no ORM in the initial foundation.
- **Migrations:** DbUp with the PostgreSQL provider and ordered SQL migration scripts.
- **Testing:** xUnit unit and integration test projects.
- **Local database:** Docker Compose; no cloud infrastructure.
- **Configuration:** environment variables for container/service configuration and .NET User Secrets for local application secrets. Only safe placeholders/examples may be committed.
- **Diagnostics:** a liveness endpoint and a database-readiness endpoint.

### Approved project boundaries

```
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
```

The four application projects form one layered monolith. Domain must not depend on Application, Infrastructure, or ASP.NET Core. Application owns use-case orchestration and depends on Domain abstractions. Infrastructure implements persistence and migration concerns. Api is the composition/HTTP boundary. Unit tests exercise domain/application behaviour; integration tests verify the real PostgreSQL connection and migration path using the local Compose service.

The precise project references, package versions, connection setup, migration runner, and test wiring are routine implementation details within this approved baseline.

### Phase 1 exclusions

The foundation must not create transaction-processing business tables or implement transaction ingestion, validation policies, idempotency, batch acceptance rules, lifecycle state transitions, retry behaviour, reconciliation, or reporting. A minimal technical schema and migration journal are permitted solely to verify migration execution. No broker, microservices, cloud, Kubernetes, Redis, EF Core, Testcontainers, or production deployment is included.

The approved baseline does not close later-phase domain decisions. Those remain governed by the requirements, domain model, and decision register.
