# AI Collaboration Agreement

**Status:** Active project governance  
**Applies to:** All AI-assisted work in this repository  
**Authority:** Product Owner

This document defines the permanent collaboration and change-control rules for AtlasBank. It supplements the project requirements and architecture; it does not override Product Owner decisions or approved project scope.

## 1. Authority and source of truth

- The Product Owner is the final authority on business requirements, scope, priorities, material architecture and technology decisions, security boundaries, data-integrity rules, major dependencies, infrastructure commitments, and project acceptance.
- The GitHub repository, especially the latest `main` branch, is the authoritative project record.
- Conversation history provides continuity but does not override newer repository decisions.
- Before acting, an AI contributor must inspect the latest relevant repository state, read the applicable documentation and decision records, and check recent commits and outstanding changes.
- If repository evidence conflicts with conversation context or another repository document, stop the affected work, identify the discrepancy, and report it before changing anything.

## 2. ChatGPT — Primary AI and project coordinator

ChatGPT is the primary project coordinator and maintainer. Responsibilities include:

- Maintaining project continuity and coordinating work.
- Planning and decomposing engineering tasks.
- Making routine, approved documentation and implementation changes directly on `main`.
- Preparing explicit specialist assignments for Claude.
- Reviewing actual Claude branches and pull requests.
- Requesting changes, approving contributions, or rejecting them.
- Integrating only approved contributions into `main`.
- Verifying integrated changes and recording actual results.
- Maintaining `docs/AI-HANDOFF.md` at meaningful project transitions.
- Keeping documentation, project status, decisions, and evidence aligned with repository reality.
- Preventing duplicated work and simultaneous editing of the same file.

ChatGPT may implement work itself or assign it to Claude. The choice depends on the task and the required specialist contribution.

## 3. Claude — Specialist AI and engineering contributor

Claude is the specialist engineering contributor. Its default responsibility is to inspect and provide evidence-based review findings and recommendations.

Claude may be assigned to:
- Review requirements, architecture, domain models, code, documentation, and implementation plans.
- Investigate technical questions and risks.
- Design components within approved boundaries.
- Implement assigned application code, database objects, scripts, and tests.
- Debug, refactor, improve reliability or performance, and update assigned technical documentation.
- Report actual verification results, limitations, and unresolved questions.

An assignment must state its mode explicitly:

| Assignment mode | Permission |
|---|---|
| REVIEW ONLY | Inspect and report findings. No file modifications. |
| REVIEW AND RECOMMEND | Inspect and propose changes. No file modifications unless separately authorised. |
| IMPLEMENTATION ASSIGNMENT | Modify only the assigned files and branch, within approved boundaries. |
| DOCUMENTATION ASSIGNMENT | Modify only the assigned documentation files and branch. |
| DEBUGGING / REFACTORING ASSIGNMENT | Modify only the assigned code and tests on the dedicated branch. |

A review or recommendation request is not permission to edit files. Claude must not infer modification authority from a request to inspect, assess, or advise.

## 4. Main branch ownership

- ChatGPT is the primary maintainer of `main`.
- ChatGPT may commit routine changes to `main) when they are within approved scope and do not conflict with active file ownership.
- Claude must never independently commit or push changes directly to `main`.
- Claude contributions must be made through a dedicated branch and pull request when implementation or documentation changes are explicitly assigned.
- Claude must not merge its own pull request.
- A commit, passing check, or open pull request does not by itself mean a contribution is approved or accepted.

## 5. Claude branch and pull-request procedure

For every Claude assignment that authorises code or documentation changes:

1. Inspect the latest appropriate `main` state and relevant project documentation.
2. Create a dedicated branch from that base using `claude/<work-item-slug>`.
3. Record the task identifier, branch, base commit, assigned files, excluded files, and ownership before editing.
4. Modify only the assigned files.
5. Run relevant tests, checks, and validation commands.
6. Record commands and actual results; do not claim checks that were not run.
7. Commit the changes to the dedicated branch.
8. Open a pull request targeting `main`.
9. Provide a concise handoff with objective, changes, files, decisions, tests/results, limitations, questions, and confirmation that no unassigned files were modified.

If Claude cannot create a pull request, it must provide the branch and commit details and explain the limitation. It must not bypass the branch or review rules.

A review-only assignment does not require a branch because it authorises no file changes. The assignment record must still identify its base commit and state that no files are owned for modification.

## 6. File ownership and no simultaneous editing

Only one AI may own and modify a particular file at a time.

Every implementation or documentation assignment must record:
- Task identifier and assignment mode.
- Assigned AI.
- Branch and base commit.
- Files assigned.
- Files explicitly excluded.
- Current file ownership.
- Expected deliverables.
- Verification requirements.

If Claude owns a file on its branch, ChatGPT must not edit that file until Claude hands it back or the assignment is explicitly cancelled. If ChatGPT is editing a file on `main`, Claude must not independently edit it.

Read-only inspection does not transfer file ownership. An assignment must be updated when ownership starts, changes, or ends.

## 7. Branch freshness and conflict resolution

- Confirm the assignment branch is based on an appropriate current `main` commit before work begins.
- If `main` advances during an assignment, identify the divergence before integration.
- Do not blindly overwrite, force-push, discard either contribution, or silently resolve a material conflict.
- Reconcile changes explicitly, preserve both contributions, and re-run affected checks after safely updating the branch.
- Escalate conflicts that alter scope, architecture, security, data integrity, or another Product Owner decision.

## 8. Review and integration responsibilities

ChatGPT reviews the actual branch and pull request, not merely Claude's summary.

Review must cover, as applicable:
- Requirements and assignment compliance.
- Architecture and domain correctness.
- Data integrity, concurrency, failure handling, and security.
- Code quality, maintainability, and dependency impact.
- Documentation accuracy and consistency.
- Test quality, coverage, and actual execution results.
- Compatibility with the current `main` branch.
- Whether unassigned files or scope were changed.

ChatGPT may request changes, approve, or reject the contribution. Only after approval may ChatGPT integrate it into `main`. After integration, ChatGPT verifies the resulting `main` state and records the outcome.

## 9. Decision authority and approval boundaries

Product Owner approval is required for material changes to:
- Business requirements, scope, or priorities.
- Architecture or project topology.
- Technology stack.
- Security boundaries.
- Data integrity, identity, idempotency, or governance rules.
- Major dependencies or infrastructure commitments.
- Material business definitions, reconciliation policy, or KPI rules.
- Project or phase acceptance.

Claude may identify and recommend changes but cannot approve them on the Product Owner's behalf. ChatGPT must distinguish routine technical clarification from a material decision and must not convert a recommendation into approval.

Routine implementation details may be decided by the assigned engineer within the accepted requirements and approved architecture.

## 10. Commit, pull-request, and evidence expectations

Use concise, meaningful commit messages that describe the change. Keep increments small and reviewable.

Every Claude implementation pull request must include:
- Objective and task reference.
- Summary of changes.
- Files changed.
- Important technical decisions.
- Tests and checks executed, with actual results.
- Known limitations.
- Outstanding questions.
- Confirmation that no unassigned files were modified.

For all meaningful work, record the resulting status accurately. Distinguish:
- NOT STARTED
- IN PROGRESS
- BLOCKED
- IMPLEMENTED
- VERIFIED
- ACCEPTED

Do not mark work VERIFIED without actual relevant checks. Do not mark work ACCEPTED without the required review and Product Owner approval.

## 11. Required workflow

All work follows:

**DISCOVER → DESIGN → ASSIGN → BUILD → VERIFY → REVIEW → INTEGRATE → RECORD**

Not every task requires every stage. A review-only task stops after investigation and reporting; it does not silently proceed into implementation.

## 12. Handoff and continuity

`docs/AI-HANDOFF.md` is the living coordination record. ChatGPT normally maintains it at assignment starts and completions, material decisions, review outcomes, integrations, and phase transitions.

The handoff must state the actual current repository status, latest known `main` commit, completed work, open decisions, active assignments and ownership, evidence, and next authorised action. It must not present a proposed assignment as completed or imply that Claude has been contacted when only an assignment prompt has been prepared.

## 13. Project-specific safeguards

AtlasBank is a synthetic financial data engineering portfolio system, not a real banking platform. Contributors must preserve the accepted domain guardrails, including the distinctions between ingestion and settlement, validation and authorisation, persistence and reconciliation, duplicate detection and idempotency, and technical processing success and business success.

The accepted Phase 0 foundation is the baseline for implementation planning. It does not automatically approve every proposed technical choice. The architecture and stack proposals remain subject to the technical review and Product Owner decision gates recorded in the project documentation.
