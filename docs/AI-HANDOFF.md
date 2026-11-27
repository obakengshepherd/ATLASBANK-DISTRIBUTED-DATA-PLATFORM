# AI Handoff — AtlasBank

**Last updated:** 2026-11-27  
**Maintainer:** ChatGPT — Primary AI  
**Repository:** https://github.com/obakengshepherd/ATLASBANK-DISTRIBUTED-DATA-PLATFORM  
**Branch:** `main`

This is the living coordination record. It reports verified repository state, active ownership, actual evidence, and the next authorised action.

## 1. Current verified repository state

| Item | Status | Evidence |
|---|---|---|
| Phase 0 — Foundation and domain design | ACCEPTED | Product Owner accepted the foundation documentation. |
| Phase 1 — Repository and technical foundation | APPROVED — IMPLEMENTATION NOT STARTED | Product Owner approved the technical baseline on 27 November 2026; no code or infrastructure is yet present. |
| Initial architecture | ACCEPTED INITIAL DIRECTION | Layered monolith. |
| Phase 1 technical selections | ACCEPTED | Recorded as AB-D-009 through AB-D-017 in docs/DECISIONS.md. |
| Latest verified main | VERIFIED | See the current `main` HEAD; re-fetch immediately before issuing the assignment. |
| Application code / database migrations / test projects | NOT STARTED | Repository tree still contains documentation only. |
| Claude implementation assignment | PREPARED — NOT YET ISSUED | No Claude implementation has been received. |
| Local assembly / execution | NOT STARTED | No local build, test, or database evidence supplied. |

## 2. Latest main and history

Main SHA immediately before this handoff revision:

`193e1c8e76c30c346c56208ed8f5b00f822ef996`

Re-fetch `main` immediately before issuing Claude's assignment; that fetched SHA becomes the assignment base.

Recent documentation commits:
- `09083bbf57762e732fb2c8a1efa5ea0783469e24` — `docs(atlasbank): approve Phase 1 technical foundation`.
- `945687fbb7f67ed55062128b806690531a03a691` — `docs(atlasbank): synchronize engineering activation baseline`.
- `c289327d011634753fcfdca4120d93d4b15ae6cf` — `fix(repo): preserve docs directory during activation sync`.
- `193e1c8e76c30c346c56208ed8f5b00f822ef996` — `docs(atlasbank): finalize technical activation handoff`.

The intermediate tree-construction issue was corrected by restoring the proper root tree in the follow-up commit. The current verified tree contains README.md and docs/ with the original documentation layout; no duplicate root-level documentation copies remain. The preceding commit remains in history and was not rewritten. No application implementation has been created.

## 3. Preserved project foundation

- Synthetic financial transaction data engineering portfolio system; not a real banking or payment platform.
- Phase 0 requirements, domain model, and acceptance criteria remain authoritative and preserved.
- Layered monolith as initial architecture.
- Domain guardrails remain unchanged: ingestion is not settlement; validation is not authorisation; persistence is not reconciliation; duplicate detection is not idempotency; technical success is not necessarily business success; money uses exact decimal representation and explicit currency; no implicit cross-currency aggregation.
- Preserve source provenance, original input, rejection reasons, and material processing history.
- Phase 1 must not implement transaction ingestion, validation policies, idempotency, batch acceptance, lifecycle rules, retries, reconciliation, or reporting.

## 4. Approved Phase 1 technical baseline

Approved decisions AB-D-009 through AB-D-017:

- C# / .NET 10 LTS / ASP.NET Core.
- PostgreSQL 18.x, pinned to a current supported patch.
- Npgsql and Dapper with explicit parameterised SQL; no ORM.
- DbUp with PostgreSQL provider and ordered SQL migration scripts.
- xUnit unit and integration test projects.
- Docker Compose for local PostgreSQL only.
- Environment variables and .NET User Secrets; never commit credentials.
- Liveness and database-readiness diagnostics.
- Four application projects: Api, Application, Domain, Infrastructure.
- Separate UnitTests and IntegrationTests projects.
- SQL migrations under `database/migrations/`.

No cloud, broker, microservices, Kubernetes, Redis, EF Core, Testcontainers, or production deployment is approved for this increment.

## 5. FNEOS ownership and workflow

- ChatGPT owns architecture, requirements, specifications, all documentation, task planning, GitHub main governance, return/alignment review, and integration.
- Claude owns only explicitly assigned code files during an active implementation assignment. Claude must return complete files with exact repository-relative paths and the required implementation/testing instructions.
- Product Owner controls VS Code local assembly and execution.
- Claude must not edit documentation, push, commit, merge, or create branches.
- No branch-based work is authorised.
- One independent review of the handoff only. Non-blocking findings are documented and implementation proceeds. Stop only for a genuine blocker or a material Product Owner decision.
- After local work, ChatGPT performs one return/alignment checkpoint before the next increment.

## 6. Technical review status

The Claude architecture review ATB-ARCH-REV-001 was reconciled against the actual repository documents. It was useful as a risk checklist but was not repository-verified by Claude. The repository-level reconciliation is complete; no material blocker prevents Phase 1.

Issues reserved for later phases include exact source identity/content equivalence, batch partial-acceptance policy, monetary precision/scale policy, correction/reversal semantics, retry classification, lifecycle state machine, reconciliation keys/tolerances, and operational authentication. These do not block the technical foundation.

## 7. Active / next engineering assignment

**Task ID:** ATB-P1-FOUNDATION-001  
**Phase:** 1 — Repository and Technical Foundation  
**Increment:** 1 — Reproducible development foundation  
**Base main SHA:** Re-fetch latest `main` immediately before issue; do not use a stale SHA.  
**Status:** Prepared; not yet issued to Claude  
**Claude ownership:** Only the exact code/configuration files listed in the assignment prompt.  
**Documentation ownership:** ChatGPT retains all documentation.

The assignment establishes the .NET solution/projects, package configuration, local PostgreSQL Compose service, DbUp migration execution, secret-safe configuration, health/readiness endpoints, unit/integration test baseline, and clean-checkout instructions. It must not implement business transaction features.

## 8. Verification evidence required from Product Owner

After Claude returns complete files, assemble them locally and run the exact commands supplied in the handoff. At minimum, evidence must establish:

- .NET SDK version and Docker version.
- Restore and clean build succeed from the repository root.
- PostgreSQL Compose service starts and reports healthy.
- Application starts with local configuration and User Secrets.
- Liveness endpoint responds successfully.
- Database-readiness endpoint reports healthy only when PostgreSQL is reachable.
- DbUp applies the technical migration successfully and a second run is a no-op.
- Unit and integration tests pass against the actual local PostgreSQL service.
- No credentials or connection strings containing secrets are tracked by Git.

No test or runtime result is claimed before the Product Owner supplies it.

## 9. Current progress

**Current phase:** Phase 1 — Repository and Technical Foundation  
**Progress:** 0% — implementation has not started.  
**Phase status:** APPROVED — READY FOR IMPLEMENTATION.  
**Next action:** Issue ATB-P1-FOUNDATION-001 to Claude.  
