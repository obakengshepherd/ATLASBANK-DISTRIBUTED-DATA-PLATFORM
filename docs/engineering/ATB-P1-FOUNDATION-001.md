# ATB-P1-FOUNDATION-001 — Phase 1 Implementation Specification

**Phase:** 1 — Repository and Technical Foundation  
**Increment:** 1 — Reproducible development foundation  
**Status:** READY FOR CLAUDE REVIEW AND IMPLEMENTATION  
**Specification owner:** ChatGPT — Primary AI Architect  
**Implementation owner:** Claude, limited to the code/configuration scope below  
**Product Owner:** Local assembly, execution, verification, and acceptance

## 1. Repository baseline

Repository: https://github.com/obakengshepherd/ATLASBANK-DISTRIBUTED-DATA-PLATFORM  
Branch: `main`  
Verified base at specification preparation: `7ef21d296f4d3432dc67c8d318607b5f48dd1351`

Re-fetch `main` immediately before Claude begins. If the HEAD differs, inspect the intervening changes and update this assignment base before implementation. Do not create a branch; branch-based work is not authorised.

Approved references:
- `README.md`
- `docs/ARCHITECTURE.md`
- `docs/DECISIONS.md` (AB-D-009 through AB-D-017)
- `docs/REQUIREMENTS.md`
- `docs/IMPLEMENTATION_ROADMAP.md`
- `docs/ACCEPTANCE_CRITERIA.md`
- `docs/AI-COLLABORATION.md`
- `docs/AI-HANDOFF.md`
- This specification.

## 2. Objective

Create the minimum complete, reproducible .NET 10 LTS layered-monolith foundation with local PostgreSQL 18.x, DbUp migrations, secret-safe configuration, health/readiness diagnostics, and unit/integration test wiring.

The increment proves that the repository can build, start, connect to PostgreSQL, apply a migration, and execute tests from a clean checkout.

## 3. Approved architecture

Application projects:
- `src/AtlasBank.Api/`
- `src/AtlasBank.Application/`
- `src/AtlasBank.Domain/`
- `src/AtlasBank.Infrastructure/`

Test projects:
- `tests/AtlasBank.UnitTests/`
- `tests/AtlasBank.IntegrationTests/`

Database:
- `database/migrations/`

One deployable ASP.NET Core application. Domain has no dependency on Application, Infrastructure, or ASP.NET Core. Application depends on Domain abstractions. Infrastructure implements database and migration concerns. Api is the HTTP/composition root. Tests are separate unit and integration projects.

## 4. Exact scope

Claude may create or modify only files required for:
- .NET solution and the six approved projects.
- Project references and central/package dependency configuration.
- ASP.NET Core application bootstrap and dependency composition.
- PostgreSQL connection configuration using Npgsql.
- Dapper registration/basic infrastructure wiring.
- DbUp PostgreSQL migration runner and a minimal technical migration/journal.
- Docker Compose PostgreSQL service and safe environment examples.
- .NET User Secrets setup instructions/placeholders.
- Liveness and database-readiness endpoints.
- xUnit unit and integration test baseline.
- Ignore rules and developer setup/run/test instructions if needed.

Claude must return a complete repository-relative file inventory before the complete file contents. No ellipses, omitted files, or partial snippets for files being created/changed.

## 5. Explicit exclusions

Do not create or implement:
- Business entities/tables for source systems, batches, raw transactions, accepted transactions, idempotency, rejections, processing attempts, lifecycle, reconciliation, audit business events, or reporting.
- Transaction ingestion, validation rules, normalisation, idempotency, conflict handling, retries, reconciliation, or reporting.
- Business API endpoints.
- Authentication/authorisation product or user management.
- Message brokers, microservices, cloud resources, Kubernetes, Redis, EF Core, Testcontainers, or production deployment.
- Speculative abstractions, empty projects, or unnecessary package dependencies.

A minimal technical migration journal/schema is allowed solely to prove DbUp execution. Do not pre-empt the future business schema.

## 6. Technical requirements

### Runtime and package management
- Target .NET 10 (latest supported servicing patch available at implementation time).
- Pin mutually compatible NuGet package versions; do not use floating versions.
- Record actual selected package versions in the project files/central package file and report them in the handoff.
- Use nullable reference types and implicit usings consistently.
- Enable deterministic, warning-conscious builds; do not suppress warnings without explanation.
- Keep the solution straightforward and avoid unnecessary project/package layering.

### Project dependency direction
- Domain: no project reference to Application, Infrastructure, or Api.
- Application: may reference Domain; no direct ASP.NET Core or concrete database dependency.
- Infrastructure: references Application/Domain as required to implement abstractions; owns Npgsql, Dapper, and DbUp integration.
- Api: composition root; references Application and Infrastructure.
- UnitTests: reference the projects whose isolated behaviour they test.
- IntegrationTests: exercise the actual API/infrastructure/database path appropriate to the foundation.

If a more precise reference arrangement is needed, explain it and remain within these boundaries.

### PostgreSQL and Docker Compose
- Use PostgreSQL 18.x, pinned to a current supported patch tag rather than an unbounded major-only tag.
- Provide a named volume for local persistence and a health check using a PostgreSQL-native readiness command.
- Bind only what is needed for local development; document the host port.
- Use non-production local credentials supplied through environment variables or a local untracked env file. Commit only safe placeholders.
- Do not hard-code real secrets or claim the local credentials are production secure.
- Ensure the application can connect using a documented connection-string configuration key.

### Configuration and secrets
- Use .NET configuration conventions and environment variables for container/service configuration.
- Use .NET User Secrets for local application secrets.
- Provide safe example values/placeholders only.
- Ensure local secret files and env files are ignored by Git.
- Never print connection strings or credentials in logs, test output, exception messages, or handoff examples.

### Migrations
- Use DbUp with the PostgreSQL provider and ordered SQL migration scripts.
- Make migration execution explicit and observable at application startup or through a documented command; choose the simpler reliable approach and explain it.
- A second execution must be a no-op when no new migration exists.
- Keep migration scripts as repository files under `database/migrations/`.
- Do not add business tables. A minimal technical journal/schema is sufficient.
- Fail clearly on migration errors; do not report readiness as healthy if required startup migration/connectivity has failed.

### Diagnostics
- Liveness indicates the process is running and must not depend on database connectivity.
- Readiness verifies PostgreSQL connectivity and reports unhealthy/unready if unavailable.
- Do not expose secrets, connection details, or internal stack traces through health responses.
- Use conventional HTTP status codes and document paths.

### Testing
- Unit tests must run without requiring PostgreSQL.
- Integration tests must use the real PostgreSQL service started through the approved local Compose setup.
- Do not use Testcontainers.
- Tests must not depend on developer-specific machine state or committed secrets.
- Include at least:
  - A small unit test proving the unit test project is wired and discoverable.
  - An integration test proving PostgreSQL connectivity.
  - A migration test proving the migration can be applied and that a second run is safe/no-op.
  - A readiness behaviour test for a reachable database.
- If an unavailable-database readiness test is not reliable in the selected setup, document the exact manual verification procedure rather than writing a flaky test.

## 7. Required developer experience

Document exact commands for:
1. Confirming .NET SDK and Docker versions.
2. Restoring and building the solution from repository root.
3. Starting PostgreSQL with Docker Compose and checking health.
4. Initialising .NET User Secrets and setting the connection string without committing it.
5. Starting the API.
6. Checking liveness and readiness.
7. Applying/rerunning migrations.
8. Running unit tests.
9. Running integration tests.
10. Stopping the Compose service while optionally retaining the named volume.

Commands must match the files actually returned. Do not claim they were executed unless Claude actually ran them in an environment with the required tools. The Product Owner will independently run them locally.

## 8. Acceptance criteria

| ID | Acceptance condition |
|---|---|
| P1-AC-01 | Solution contains exactly the four approved application projects and two test projects unless a justified adjustment is reported and approved. |
| P1-AC-02 | Project dependency direction respects the approved layered boundaries. |
| P1-AC-03 | All projects target .NET 10 and package versions are pinned and compatible. |
| P1-AC-04 | Clean restore and build commands are documented and expected to run from repository root. |
| P1-AC-05 | Docker Compose starts a pinned PostgreSQL 18.x service with a persistent named volume and health check. |
| P1-AC-06 | Application connection settings use environment/User Secrets and no credentials are committed. |
| P1-AC-07 | DbUp applies the technical migration and a second run is safe/no-op. |
| P1-AC-08 | Liveness works independently of PostgreSQL. |
| P1-AC-09 | Readiness accurately reflects PostgreSQL reachability without leaking sensitive details. |
| P1-AC-10 | Unit tests are independently discoverable and runnable. |
| P1-AC-11 | Integration tests exercise the real local PostgreSQL service. |
| P1-AC-12 | No business transaction schema or out-of-scope capability is introduced. |
| P1-AC-13 | Setup, run, migration, health, and test instructions match the returned implementation. |

## 9. Claude review-and-build workflow

1. Re-fetch and inspect current `main` and the approved references.
2. Perform one independent review of this specification.
3. Classify findings:
   - NON-BLOCKING: document the finding and continue within this specification.
   - BLOCKING: stop the affected work and explain the exact contradiction or missing decision.
   - PRODUCT OWNER DECISION: stop only where a material approval is genuinely required.
4. If no blocker exists, implement this same assignment without waiting for another review round.
5. Return complete files, rationale, package versions, exact commands, test evidence actually obtained, limitations, and any deviations.
6. Do not modify documentation owned by ChatGPT unless the Product Owner explicitly transfers ownership.
7. Do not commit, push, merge, create branches, or expand scope.

## 10. Product Owner assembly and verification

The Product Owner places returned files into the local repository using VS Code, then supplies actual command output and test results to ChatGPT.

Minimum local evidence:
- .NET SDK and Docker versions.
- Restore/build output.
- Compose health status.
- API startup output with secrets redacted.
- Liveness and readiness HTTP results.
- DbUp first-run and second-run results.
- Unit and integration test summaries.
- Git status confirming secret files are untracked/ignored.

ChatGPT then performs the mandatory return/alignment checkpoint against the current repository and this specification. Phase 1 is not VERIFIED or ACCEPTED until evidence is reviewed and the Product Owner accepts it.

## 11. Stop conditions

Stop and report rather than silently changing:
- .NET 10 or PostgreSQL 18 baseline cannot be met.
- A required package forces a material stack or architecture change.
- A project dependency violates the approved boundaries.
- The proposed migration introduces business schema.
- Safe secret handling cannot be achieved.
- A requirement implies broker, cloud, microservice, ORM, Testcontainers, or production deployment scope.
- A material decision is needed that is not already approved.

## 12. Required return format

1. Review findings and classification.
2. Implementation summary.
3. Exact file inventory (created/modified).
4. Complete contents of every assigned file, grouped by exact path.
5. Project dependency map.
6. Pinned package/runtime/database versions.
7. Exact setup, migration, run, and test commands.
8. Actual test/build results and execution environment (or explicitly state not run).
9. Known limitations and non-blocking findings.
10. Confirmation that no excluded business functionality or documentation was changed.
