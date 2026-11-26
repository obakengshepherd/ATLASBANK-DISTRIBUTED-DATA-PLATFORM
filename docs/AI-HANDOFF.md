# AI Handoff — AtlasBank

**Last updated:** 2026-11-26  
**Maintainer:** ChatGPT — Primary AI  
**Repository:** https://github.com/obakengshepherd/ATLASBANK-DISTRIBUTED-DATA-PLATFORM  
**Branch:** `main)

This is the living project coordination record. It describes verified repository state and authorised next actions; it is not a substitute for the requirements, architecture, or decision records.

## 1. Current project status

| Item | Actual status | Evidence / notes |
|---|---|---|
| Project | Active | AtlasBank — Distributed Data Platform |
| Phase 0 — Foundation | ACCEPTED | Product Owner explicitly accepted the foundation documentation as the implementation baseline. |
| Phase 1 — Repository & Technical Foundation | NOT STARTED | No solution, application code, database schema, API, or infrastructure has been created. |
| Current activity | IN PROGRESS | Establishing AI collaboration governance and preparing the first specialist architecture review. |
| Application implementation | NOT STARTED | No implementation work is authorised by the current review assignment. |
| Database schema / migrations | NOT STARTED | No physical schema or migration scripts exist. |
| Automated tests | NOT STARTED | No test projects or test execution evidence exist. |
| Deployment | NOT STARTED | No deployment infrastructure is in scope at this point. |

## 2. Repository baseline

- Latest verified `main` commit immediately before this handoff refresh: `35062ff5f707e025b42e20cf62b5f171e956269d`. The commit that updates this handoff becomes the new `main` HEAD; always fetch `main` again before issuing an assignment.
- The verified tree now contains nine files: `README.md` and eight documents under `docs/`, including the two AI governance records.
- There were no open pull requests at the time of inspection.
- `docs/AI-COLLABORATION.md` and `docs/AI-HANDOFF.md` have now been created and committed to `main`.
- The README's stale project-status paragraph has been corrected to state that Phase 0 is accepted and Phase 1 has not started.
- Re-fetch `main` before any subsequent work. The commit SHA in this section must be refreshed after the governance files are committed.

## 3. Accepted foundation and preserved decisions

The Product Owner accepted the Phase 0 foundation as the implementation baseline.

Preserve these project boundaries and domain principles:
- Synthetic financial data only; no real banking or payment integrations.
- Ingestion is not settlement.
- Validation is not authorisation.
- Persistence is not reconciliation.
- Duplicate detection is not idempotency.
- Technical processing success is not necessarily business success.
- Monetary values require exact decimal representation and explicit currency.
- Do not combine currencies without an explicit, traceable conversion basis.
- Preserve provenance, original input, rejection reasons, and material processing history.
- Avoid premature microservices, brokers, cloud infrastructure, and unnecessary complexity.

The accepted documents remain:
- `README.md`
- `docs/PROJECT_CHARTER.md`
- `docs/REQUIREMENTS.md`
- `docs/DOMAIN_MODEL.md`
- `docs/ARCHITECTURE.md`
- `docs/IMPLEMENTATION_ROADMAP.md`
- `docs/ACCEPTANCE_CRITERIA.md`

## 4. Proposed architecture and technology — not final approval

The current proposal in `docs/ARCHITECTURE.md` is:
- Layered monolith.
- C# and .NET 10 LTS.
- ASP.NET Core.
- PostgreSQL.
- Npgsql.
- Dapper with explicit SQL.
- Unit and integration testing; xUnit is proposed.
- Docker Compose if it materially improves local reproducibility.

These are proposals within the accepted foundation, not all final Product Owner approvals. They require a technical review against requirements and current supported tooling. Do not install dependencies, create a solution, create database objects, or replace the proposal before the appropriate review and Product Owner decisions.

Open technical/design questions include:
- Layered-monolith boundaries and whether the proposed project split is proportionate.
- Exact .NET solution and project structure.
- PostgreSQL fit and data type / constraint considerations.
- Npgsql and Dapper integration and transaction handling.
- Source transaction identity and content-equivalence semantics.
- Idempotency, duplicate repeat, and conflicting identity handling.
- Concurrency safety and atomic persistence.
- Failure classification, retry boundaries, and recovery.
- Reconciliation inputs, matching, and independent result boundaries.
- Unit, integration, and concurrency test strategy.
- Local environment, migrations, configuration, and reproducibility.
- Authentication/authorisation scope for any operational endpoints.

## 5. First specialist assignment — prepared, not yet executed

**Task ID:** `ATLASBANK-REV-001`  
**Assignment mode:** REVIEW ONLY  
**Assigned specialist:** Claude  
**Assignment status:** PREPARED — awaiting submission to Claude; no Claude execution or review findings have been received  
**Branch:** Not applicable; review-only work authorises no file changes.  
**Base commit:** Must be refreshed from the current `main` HEAD immediately before the review prompt is issued. The current handoff commit is not a fixed assignment base; record the fetched SHA in the prompt.  
**Files owned for modification:** None.  
**Files to inspect:** `README.md`, `docs/PROJECT_CHARTER.md`, `docs/REQUIREMENTS.md`, `docs/DOMAIN_MODEL.md`, `docs/ARCHITECTURE.md`, `docs/IMPLEMENTATION_ROADMAP.md`, `docs/ACCEPTANCE_CRITERIA.md`, `docs/AI-COLLABORATION.md`, and `docs/AI-HANDOFF.md`.  
**Files excluded from modification:** All repository files. No file changes are authorised.

### Review scope

Claude is asked to assess:
1. Layered-monolith boundaries and responsibilities.
2. Proposed .NET solution structure.
3. PostgreSQL suitability.
4. Npgsql and Dapper usage.
5. Financial data integrity.
6. Transaction identity and idempotency semantics.
7. Duplicate submission versus conflicting identity.
8. Concurrency and transaction safety.
9. Failure and retry handling.
10. Reconciliation boundaries.
11. Testing strategy.
12. Local development and reproducibility.

### Required finding classifications

Each finding must be classified as one of:
- **ACCEPTABLE**
- **ACCEPTABLE WITH CONDITIONS**
- **CHANGE RECOMMENDED**
- **PRODUCT OWNER DECISION REQUIRED**

Claude must cite the relevant repository document/requirement, explain the reasoning and impact, distinguish evidence from assumptions, and propose the smallest practical correction where applicable. Material decisions must be presented as options with trade-offs, not treated as approved.

### Explicit restrictions

- No application implementation.
- No database schema, table, index, or migration creation.
- No solution or project creation.
- No dependency installation or stack changes.
- No repository file modifications or commits.
- No direct changes to `main`.
- Do not turn recommendations into approved decisions.

### Expected deliverable

A structured review report containing:
- Executive summary.
- Findings grouped by topic and severity/priority.
- Required classification for every finding.
- Requirement/document references.
- Technical rationale, risks, and trade-offs.
- Proposed corrections, if any.
- Product Owner decisions required.
- A recommended Phase 1 design sequence.
- Explicit statement confirming no files were modified.

## 6. Assignment ownership and activity

| Work item | Assigned AI | Branch | File ownership | Status |
|---|---|---|---|---|
| Governance documentation initialisation | ChatGPT | `main` | `README.md`, `docs/AI-COLLABORATION.md`, `docs/AI-HANDOFF.md` | IMPLEMENTED; remote verification complete |
| `ATLASBANK-REV-001` architecture review | Claude | N/A — review only | None | PREPARED; not yet submitted |
| Phase 1 technical foundation | Not assigned | Not created | None | NOT STARTED |

No Claude branch exists for the review-only assignment, and Claude has not yet been contacted through this handoff. No simultaneous editing is authorised.

## 7. Completed work

- Repository and latest `main` state inspected.
- Accepted foundation and current Phase 0 / Phase 1 status confirmed.
- No open pull requests found during inspection.
- Missing AI governance documents identified.
- Stale README status paragraph corrected.
- Permanent collaboration agreement prepared.
- First Claude specialist review-only assignment prepared.

## 8. Pending decisions and approvals

The Product Owner has not yet approved the proposed technical stack as a final implementation baseline. In particular, the following remain open:
- Final layered-monolith boundaries and solution structure.
- Final .NET runtime/tooling baseline after current support verification.
- PostgreSQL adoption for the first implementation.
- Npgsql and Dapper as the data-access approach.
- Migration tooling.
- Local development strategy, including whether Docker Compose is warranted.
- Testing framework and integration-test approach.
- Transaction identity, idempotency, conflict, and concurrency design.
- Failure and retry policy.
- Reconciliation matching and control rules.

The first Claude review is advisory. ChatGPT must evaluate its evidence, separate routine clarifications from material decisions, and present material decisions to the Product Owner before recording them as approved.

## 9. Next authorised action

1. Governance documentation is committed. Re-fetch `main` immediately before issuing the review prompt and use that exact SHA as the review base.
2. Provide the Product Owner with the exact review-only Claude assignment prompt, based on the fetched `main` state.
4. Await the Claude review report.
5. ChatGPT evaluates the report and presents material decisions for Product Owner approval.
6. Only after the review and approval gates are satisfied, prepare the Phase 1 implementation plan and explicitly assign an engineering increment.

**Do not begin substantial Phase 1 implementation as part of this initialisation task.**

## 10. Verification and acceptance evidence

At this point:
- Repository content inspection: performed.
- GitHub branch/tree/commit inspection: performed.
- Open pull request inspection: performed; none found.
- README status correction: committed and remotely verified.
- Governance files: created, committed to `main`, and remotely re-fetched successfully.
- Build/tests: not applicable; no application code exists.
- Foundation acceptance: confirmed by Product Owner.
- Phase 1 acceptance: not yet applicable.
