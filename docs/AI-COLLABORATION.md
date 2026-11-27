# FNEOS — AI Collaboration and Engineering Governance

**Status:** Active; Product Owner approved  
**Authority:** Product Owner  
**Primary AI:** ChatGPT  
**Specialist AI:** Claude  
**Repository:** `obakengshepherd/ATLASBANK-DISTRIBUTED-DATA-PLATFORM`

This document defines the mandatory Four-Nexus Engineering Operating System (FNEOS) workflow for AtlasBank. It governs work allocation, repository changes, review, assembly, and integration. It does not override the accepted project requirements or Product Owner authority.

## 1. Authority and source of truth

- The user is the Product Owner and final authority on requirements, scope, priorities, material architecture and technology decisions, security boundaries, data-integrity rules, major dependencies, infrastructure commitments, and acceptance.
- The latest verified GitHub `main` state and its project documentation are the authoritative shared project record.
- Before any work, ChatGPT and Claude must inspect the latest relevant repository state and applicable documentation.
- Conversation history is continuity context, not a substitute for repository verification.
- If repository evidence conflicts with another document or instruction, stop the affected work, identify the discrepancy, and resolve it before proceeding.

## 2. Mandatory three-stage workflow

All substantive engineering work follows these three stages, in order:

### Stage 1 — ChatGPT: Architecture and Documentation

ChatGPT is the Primary AI and principal project coordinator. ChatGPT owns:

- Requirements, domain specifications, architecture, technical design, and implementation planning.
- README and all project documentation, including decision records, roadmaps, handoffs, and assignment briefs.
- Architecture decision management and change control.
- Decomposing work into bounded engineering assignments with exact file ownership and acceptance criteria.
- Reviewing Claude's returned work against the approved assignment.
- Resolving integration issues, maintaining `main`, and synchronising documentation with verified repository state.

ChatGPT must not delegate documentation editing to Claude by default. Claude may inspect documentation but may not modify it unless the Product Owner explicitly approves a documentation ownership transfer that identifies the exact files and duration.

### Stage 2 — Claude: Specialist Engineering and Code

Claude is the Specialist AI. Its default role is independent technical review when requested. Claude may perform implementation only after receiving an explicit, approved engineering assignment from ChatGPT.

An engineering assignment must specify:
- Task ID and objective.
- Approved requirements and architecture baseline.
- Exact repository-relative files Claude is authorised to create or modify.
- Files explicitly excluded from modification.
- Expected behaviour and acceptance criteria.
- Constraints, dependencies, and permitted implementation choices.
- Required tests and verification instructions.
- Required return format.

Claude must:
- Work only within the assigned code-file scope.
- Return complete file contents in the conversation, with exact repository-relative paths.
- Explain implementation decisions and any assumptions.
- Provide tests, commands, actual test results, limitations, and unresolved questions.
- Report any required change outside the approved scope instead of making it unilaterally.

Claude must not:
- Push directly to GitHub or commit to `main`.
- Modify ChatGPT-owned documentation without explicit Product Owner authorisation.
- Expand scope, alter accepted requirements, or make material architecture/security/data-integrity changes without Product Owner approval.
- Claim tests passed unless they were actually run and the results are available.

**Branch-based work is prohibited by default.** No branch or pull-request workflow is to be assumed. The Product Owner must explicitly authorise branch-based work before it is used.

### Stage 3 — Product Owner + VS Code + ChatGPT: Assembly and Integration

The Product Owner remains in control of the local development environment and final approval.

1. The Product Owner places Claude's returned files into the local repository using VS Code.
2. The Product Owner runs the application, relevant tests, and required commands.
3. The Product Owner supplies ChatGPT with actual outputs, test results, errors, and any relevant local changes.
4. ChatGPT performs the mandatory return/alignment checkpoint before issuing the next implementation assignment.
5. ChatGPT reviews the supplied implementation against the approved assignment and repository state, identifies omissions or inconsistencies, and guides corrections.
6. Only approved and verified changes are integrated into `main`.
7. ChatGPT updates project documentation and handoff status to reflect verified outcomes.

ChatGPT must never claim to see local uncommitted files or test results that the Product Owner has not supplied.

## 3. Zero-overlap file ownership

- ChatGPT owns project documentation.
- Claude owns only the exact code files explicitly assigned during an active engineering assignment.
- The Product Owner controls local assembly and execution.
- No two contributors may modify the same file simultaneously.
- Read-only inspection does not transfer file ownership.
- An assignment ends, and file ownership returns to the project coordinator, when Claude has returned the requested deliverables and the Product Owner/ChatGPT alignment checkpoint has been completed, or when the assignment is explicitly cancelled.
- Any proposed ownership transfer must be explicit and approved by the Product Owner.

## 4. Change control and decision rights

Product Owner approval is required before material changes to:
- Business requirements, project scope, priorities, or success criteria.
- Architecture, application topology, or major component boundaries.
- Technology stack or major dependencies.
- Security boundaries or authentication/authorisation assumptions.
- Data integrity, identity, idempotency, concurrency, or audit rules.
- Infrastructure commitments or deployment strategy.
- Reconciliation policy, business definitions, or KPI semantics.
- Project or phase acceptance.

Routine implementation details may be decided by the assigned engineer within the approved boundaries. Recommendations are not approvals. ChatGPT records approved material decisions in `docs/DECISIONS.md`.

## 5. Mandatory return/alignment checkpoint

Whenever the Product Owner returns after Claude work or local assembly, ChatGPT must first:

1. Establish the latest `main` commit.
2. Inspect the current repository and relevant changed files.
3. Compare the implementation with the exact approved assignment.
4. Review the actual test results supplied by the Product Owner.
5. Identify inconsistencies, omissions, regressions, blockers, and scope deviations.
6. Confirm alignment or explain what remains unresolved.

Do not issue the next implementation assignment until this checkpoint is complete. Do not infer local file contents, successful execution, or test results.

## 6. Repository and main-branch governance

- GitHub `main` is the shared source of truth.
- ChatGPT coordinates approved integration and maintains project documentation on `main`.
- Claude never pushes directly to GitHub or commits to `main`.
- No force-push, history rewrite, or deletion of historical commits is permitted as part of routine corrective work.
- Use normal, reviewable commits with clear messages.
- Before updating a file, inspect its current repository version and preserve unrelated valid content.
- If a new commit appears during work, re-establish the base and reconcile before writing.

## 7. Progress and status reporting

Every project response must begin with the current roadmap phase and evidence-based progress in this exact form:

`[Phase X: Current Phase | Progress: XX%]`

Progress must be based on the approved roadmap and verified evidence. Do not infer progress from discussion, prepared prompts, code not assembled locally, or tests not actually run.

Use status terms consistently:
- **NOT STARTED**
- **IN PROGRESS**
- **BLOCKED**
- **IMPLEMENTED**
- **VERIFIED**
- **ACCEPTED**

Implemented does not mean verified. Verified does not mean accepted. Acceptance requires the relevant review and Product Owner approval.

## 8. Documentation synchronisation

ChatGPT maintains the README, charter, requirements, domain model, architecture, roadmap, acceptance criteria, decision register, collaboration agreement, and current handoff. Documentation must agree with one another and with actual repository state.

At meaningful milestones, ChatGPT updates the handoff with:
- Latest verified `main` commit.
- Current phase and evidence-based progress.
- Completed and outstanding work.
- Active assignments and exact file ownership.
- Actual test and verification results.
- Open Product Owner decisions.
- Next authorised action.

Never describe an assignment prompt as submitted, Claude work as completed, local code as integrated, or tests as passed without evidence.

## 9. AtlasBank-specific safeguards

AtlasBank is a synthetic financial data engineering portfolio system, not a real banking platform. Preserve the approved domain boundaries:

- Ingestion is not settlement.
- Validation is not authorisation.
- Persistence is not reconciliation.
- Duplicate detection is not idempotency.
- Technical processing success is not necessarily business success.
- Money requires exact decimal representation and explicit currency.
- Different currencies must not be silently aggregated.
- Preserve original input, provenance, rejection reasons, and material processing history.
- Do not introduce microservices, brokers, cloud infrastructure, or other complexity without a demonstrated requirement and required approval.

## 10. Current project-specific workflow state

- Phase 0 foundation: ACCEPTED.
- Phase 1 technical foundation: APPROVED; implementation pending.
- Application code, database schema, API, and deployment infrastructure: NOT STARTED.
- A bounded Phase 1 implementation assignment may be issued under the 27 November Engineering Activation Directive.
- No branch-based work is authorised by default.

This agreement supersedes any conflicting earlier AI collaboration or handoff procedure while preserving valid project requirements, architecture direction, and historical repository records.


## 11. Engineering activation workflow directive — effective 27 November 2026

This section supersedes any conflicting earlier requirement for repeated general reviews or a separate review-only assignment before implementation.

For each bounded increment:

1. ChatGPT finalises the design and complete implementation handoff.
2. Claude performs one independent review of that handoff. Findings are classified as:
   - **NON-BLOCKING:** document and proceed within the approved design.
   - **BLOCKING:** stop only the affected work and report the exact issue and recommended resolution.
   - **PRODUCT OWNER DECISION:** request a decision only where material approval is genuinely required.
3. If no blocking issue exists, Claude implements under the same assignment without waiting for another general review.
4. Claude returns complete assigned files and implementation/assembly/testing instructions.
5. The Product Owner assembles and executes in VS Code and supplies actual evidence.
6. ChatGPT performs one return/alignment checkpoint, reviews the increment, and integrates approved changes into main.
7. The next increment begins.

Every handoff must state task ID, phase/increment, latest verified main SHA, approved references, objective, exact included and excluded files, dependencies, expected behaviour, acceptance criteria, commands, expected outcomes, complete-file return format, assembly steps, local verification steps, risks, and stop conditions.

ChatGPT owns all documentation. Claude owns only explicitly assigned code files during the active assignment. Claude must not push, commit, merge, create branches, or alter documentation. The Product Owner owns local assembly and execution. Routine implementation choices within the approved baseline do not require repeated Product Owner approval. Material changes remain subject to Product Owner authority and docs/DECISIONS.md.
