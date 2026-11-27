# AI Handoff — AtlasBank

**Last updated:** 2026-11-27  
**Maintainer:** ChatGPT — Primary AI  
**Repository:** https://github.com/obakengshepherd/ATLASBANK-DISTRIBUTED-DATA-PLATFORM  
**Branch:** `main`

This is the living coordination record. It reports verified repository state and the next authorised action. It does not substitute for the project requirements, architecture, roadmap, or decision register.

## 1. Current verified status

| Item | Status | Evidence / notes |
|---|---|---|
| Phase 0 — Foundation and domain design | ACCEPTED | Product Owner accepted the foundation documentation as the implementation baseline. |
| Phase 1 — Repository and technical foundation | NOT STARTED | No application code, database schema, API implementation, migrations, or deployment infrastructure. |
| Architecture direction | ACCEPTED INITIAL DIRECTION | Layered monolith is the accepted initial direction; detailed boundaries remain subject to review. |
| Technology stack | PROPOSED | C#/.NET 10 LTS, ASP.NET Core, PostgreSQL, Npgsql, Dapper, xUnit, and optional Docker Compose remain proposals pending review and Product Owner approval. |
| Claude engineering assignment | NONE ACTIVE | No implementation assignment is active. |
| Local assembly and test execution | NOT STARTED | No Claude implementation has been assembled or tested locally. |

## 2. Repository baseline and corrective commits

Historical cutoff: **25 November 2026**. The accepted historical baseline is:

`7bce0a7781d2ae31bd31cbca4a51e999de1985b3`

The cutoff baseline is an ancestor of the current branch. The valid 26 November project-status clarifications were retained; superseded collaboration arrangements were replaced through normal corrective commits. No history was rewritten or deleted.

Corrective commits:
- Project-status and architecture alignment: `454ce1c89ee614d8b42ed63648062a9e31ddab54` — `docs(atlasbank): reconcile accepted foundation and Phase 1 status`.
- FNEOS governance alignment: `bb1781a2f079683ff0b53989d545ab95d2eb3042` — `docs(governance): align collaboration with FNEOS`.
- Verification follow-up: this handoff and the collaboration guide received a small documentation correction after the governance commit; see the latest `main` history for the follow-up commit.

The latest `main` SHA must be re-fetched before any subsequent assignment or repository operation. The governance alignment commit above is part of the verified history; the latest handoff correction is recorded in the subsequent follow-up commit.

## 3. Preserved accepted project foundation

Preserve the following:
- Synthetic financial transaction data platform; not a real banking or payment platform.
- Synthetic source ingestion, validation, normalisation, idempotency, conflict detection, persistence, processing history, reconciliation, currency-aware reporting, and observability.
- Layered monolith as the accepted initial architecture direction.
- PostgreSQL and C#/.NET 10 LTS, ASP.NET Core, Npgsql, Dapper, xUnit, and Docker Compose where useful as proposed technologies, not final approved implementation selections.
- Domain guardrails: ingestion is not settlement; validation is not authorisation; persistence is not reconciliation; duplicate detection is not idempotency; technical success is not necessarily business success; no implicit cross-currency aggregation.
- Original input, provenance, rejection reasons, and material processing history must remain traceable.

The foundation documents remain:
- `README.md`
- `docs/PROJECT_CHARTER.md`
- `docs/REQUIREMENTS.md`
- `docs/DOMAIN_MODEL.md`
- `docs/ARCHITECTURE.md`
- `docs/IMPLEMENTATION_ROADMAP.md`
- `docs/ACCEPTANCE_CRITERIA.md`

## 4. FNEOS operating model

The Product Owner approved the mandatory three-stage workflow:

1. **ChatGPT — Architecture & Documentation:** owns architecture, requirements, specifications, project documentation, decision control, assignments, main-branch governance, review, and integration.
2. **Claude — Specialist Engineering & Code:** independent review by default; code implementation only under an explicit approved assignment, returning complete files in chat with exact paths, decisions, tests, and execution instructions.
3. **Product Owner + VS Code + ChatGPT — Assembly & Integration:** Product Owner places files locally, runs the application/tests, and supplies actual results; ChatGPT performs the return/alignment checkpoint and integrates approved changes.

Zero-overlap applies: ChatGPT owns documentation; Claude owns only explicitly assigned code files during an active engineering assignment. Claude must not push or commit to main. Branch-based work is prohibited unless the Product Owner explicitly authorises it. See `docs/AI-COLLABORATION.md` for the complete rules.

## 5. Mandatory return/alignment checkpoint

Whenever the Product Owner returns after Claude work or local assembly, ChatGPT must first:
1. Establish latest main commit.
2. Inspect repository and relevant changes.
3. Compare the implementation against the approved assignment.
4. Review actual test results supplied by the Product Owner.
5. Identify omissions, inconsistencies, blockers, and scope deviations.
6. Confirm alignment or state what remains unresolved.

No next implementation assignment is issued before this checkpoint. ChatGPT must not claim access to local uncommitted files or test outputs that have not been supplied.

## 6. Current assignments and ownership

| Work item | Owner | Files owned | Status |
|---|---|---|---|
| FNEOS documentation correction | ChatGPT | README and project/governance documentation | In progress until remote verification is complete |
| Claude architecture review | Not active | None | Not yet assigned under the reset workflow |
| Phase 1 technical implementation | Not assigned | None | NOT STARTED |

There is no active Claude assignment and no branch-based task authorised. The earlier `ATLASBANK-REV-001` prepared review prompt and prior branch/PR collaboration procedure are superseded by the Product Owner's 25 November historical cutoff and subsequent FNEOS governance approval. A new review assignment must be prepared under FNEOS and explicitly issued; do not imply Claude has already been contacted.

## 7. Technical review gates before Phase 1

Before implementation:
- ChatGPT prepares a bounded review-only architecture assignment.
- Claude independently reviews the architecture and technical proposal without modifying repository files.
- ChatGPT evaluates the findings and identifies material decisions.
- Product Owner explicitly approves or rejects material technical choices.
- ChatGPT records approved decisions in `docs/DECISIONS.md` and prepares a bounded code-only implementation assignment.

No solution creation, dependency installation, database setup, schema creation, API implementation, or deployment work is authorised before these gates are satisfied.

## 8. Documentation and repository status

The corrective documentation plan is limited to:
- Align README, charter, architecture, and roadmap with accepted project state and FNEOS gates.
- Replace superseded AI collaboration and handoff instructions.
- Establish the decision register.
- Preserve requirements, domain model, and acceptance criteria unchanged.

No application implementation is part of this work. Build and test execution are not applicable while the repository remains documentation-only.

## 9. Next authorised action

After the corrective documentation commit is verified:
1. Re-fetch latest `main` and inspect all corrected documents.
2. Confirm the decision register and handoff accurately distinguish accepted decisions, proposals, and open decisions.
3. Prepare a fresh Claude review-only assignment under FNEOS, with the current main SHA and no file modification authority.
4. Await the Product Owner's direction before issuing that assignment.
5. Do not begin Phase 1 implementation until architecture review and required Product Owner decisions are complete.

**Current phase:** Phase 1 — Repository and Technical Foundation  
**Progress:** 0% — the technical foundation has not started.  
**Next phase action:** Complete FNEOS architecture-review and approval gates; no implementation yet.
