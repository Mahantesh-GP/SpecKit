# Todo SDD Pilot — Scrum + ADO + Spec Kit Execution Handbook

**Date:** 25 Aug 2026  
**Objective:** Fit SDD into the existing Scrum/ADO development process without replacing BA, QA, UAT or ADO.

## Manager guardrails

- Keep the normal Scrum/ADO process.
- BA requirements and QA/UAT continue normally.
- Introduce SDD mainly in the development portion initially.
- Keep the pilot light: simple Todo application, meaningful but bounded stories.
- Think like a multi-developer team, not one developer in isolation.
- Do not try to discover every possible project-specific issue before adoption.
- Real project pilots will provide deeper learning.
- Do not force SDD on every small story.

## End-to-end flow

```text
Idea / Requirement
→ BA/PO User Story in ADO
→ Refinement / Grooming
→ Acceptance Criteria + dependencies + questions
→ Estimation
→ Sprint Planning
→ Assignment
→ SDD-selected development
→ PR / Review
→ QA
→ UAT
→ Done
```

## Current backlog

| Story | Treatment |
|---|---|
| Create and View Todos | Done; baseline |
| Complete a Todo | Brownfield SDD |
| Delete a Todo | Brownfield SDD |
| Flask REST API Enabler | Technical enabler |
| Improve Todo UX & Pagination | Strong SDD candidate |
| Due Date & Overdue Tracking | Strong SDD/refinement candidate |
| Todo Expiration | Strong SDD/refinement candidate |
| Small UI/Text Improvement | Normal development comparison |

## Roles

- **BA/PO:** owns business requirement, Acceptance Criteria and business clarification.
- **Tech Lead/Architect:** reviews dependencies, architecture impact, plan/analyze output and prevents over-engineering.
- **Developer:** executes selected SDD lifecycle, code/tests/PR, does not invent missing business behavior.
- **QA:** reviews testability during refinement and validates the same ADO Acceptance Criteria.
- **Scrum Master/Team Lead:** facilitates refinement/planning/flow.
- **EA/SDD Mentor:** provides guidance and captures real-world learning; does not own project requirements.

## Refinement checklist

1. Business outcome clear?
2. Acceptance Criteria testable?
3. Scope / out-of-scope clear?
4. Dependencies identified?
5. Existing behavior to preserve identified?
6. Data/API/UI impact understood enough to estimate?
7. Material error/edge behavior identified?
8. QA can test independently?
9. Story is small enough?
10. SDD selection decision made?

## Definition of Ready

- Business outcome clear.
- Testable Acceptance Criteria.
- Major dependencies known.
- Material business questions answered or explicitly pending.
- Story is estimable and sprint-sized.
- Owner/skill needs understood.
- SDD selection recorded for pilot.

## SDD selection heuristic

Use normal development when the change is localized, clear, low risk and has no meaningful contract/data/cross-component impact.

Full SDD is more valuable when the story has:
- business ambiguity,
- API/data/UI/domain impact,
- brownfield regression risk,
- multiple developers/dependencies,
- significant validation/security implications,
- likely requirement evolution.

For pilot learning, `Complete a Todo` and `Delete a Todo` may still use SDD even though they are small. Add one clearly small normal-development story later for comparison.

## ADO state rule

**Do not change the team's state machine.** Use the existing ADO process.

Suggested SDD checkpoints are comments/tags, not new states:

```text
SDD-Selected
Spec-Ready
Clarification-Closed
Plan-Ready
Tasks-Ready
Analyze-Clean
Implementation-Complete
Converged
Ready-for-QA
```

## Multi-developer assignment

- Assign one primary owner per User Story.
- Create child ADO Tasks only when the team normally does so or parallel ownership is needed.
- Do not mirror every `tasks.md` item into ADO.
- One bounded feature uses one shared `spec.md`, `plan.md`, `tasks.md`.

Example:
- Developer A → Complete Todo + backend/API work
- Developer B → Delete Todo / frontend or pagination work
- QA → Acceptance Criteria
- Tech Lead → spec/plan/analyze/converge review

## Current user-story drafts

### Complete a Todo
As a Todo user, I want to mark a Todo as completed, so that I can distinguish finished work from pending work.

Acceptance Criteria:
1. Existing incomplete Todo can be marked Completed.
2. Completed state is persisted.
3. Completed Todo is visibly identified.
4. Other Todos are not modified.

### Delete a Todo
As a Todo user, I want to delete a Todo I no longer need, so that obsolete items do not remain in my list.

Acceptance Criteria:
1. Existing Todo can be deleted.
2. Deleted Todo disappears from the list.
3. Other Todos remain unchanged.
4. Deleted Todo does not return after restart.

### Improve Todo List UX & Pagination
As a Todo user, I want the Todo list to be clear and easy to navigate, so that I can efficiently manage a larger number of Todo items.

Acceptance Criteria:
1. Clean, consistent layout.
2. CRUD actions easy to identify.
3. Pagination when items exceed page size.
4. Current page / total pages or items visible.
5. Previous/Next works.
6. CRUD continues working with pagination.
7. Pending/Completed visually distinct.
8. Empty/error/validation states are clear.

### Due Date & Overdue Tracking
As a Todo user, I want to assign a due date and see overdue Todos, so that I can prioritize delayed work.

Acceptance Criteria:
1. Due date optional.
2. User can set/update due date.
3. Incomplete Todo past due date is Overdue.
4. Completed Todo is not Overdue.
5. Existing Todos without due dates continue working.
6. User can view/filter overdue Todos.

### Todo Expiration
As a Todo user, I want stale incomplete Todos to expire after an agreed period, so that my active list remains relevant.

Acceptance Criteria:
1. Expiration period explicitly agreed in refinement.
2. Expiration changes state; no physical deletion.
3. Completed Todos do not expire.
4. Expired Todos remain available for reference.
5. Existing active behavior remains unchanged before threshold.

## Flask REST API enabler

```text
GET    /api/todos
POST   /api/todos
PUT    /api/todos/{id}/complete
DELETE /api/todos/{id}
```

Later add edit/due-date/pagination contracts as needed. This is a technical enabler unless the business explicitly requires external API access.

## SDD flow for selected stories

### Constitution — once
Keep it project-wide:
- ADO/BA requirements authoritative.
- No silent business assumptions.
- Preserve existing working behavior unless approved change.
- Small/testable slices.
- Important behavior tested.
- Validate implementation against approved artifacts.
- Avoid unnecessary complexity.

### Specify
Use ADO story ID, description and Acceptance Criteria as the authoritative source. Focus on what/why. Preserve baseline. Surface ambiguity; do not invent material behavior.

### Clarify
Ask only material questions. If a BA/PO decision changes behavior:
**update ADO first → update spec**.

### Plan
Use Flask + SQLite + Flask REST API + simple UI + pytest. Preserve current behavior. Avoid React/cloud/Docker/auth/microservices unless approved.

### Checklist
Focused only on:
- ADO AC coverage,
- no invented business rule,
- ambiguity closure,
- regression,
- API/data/UI clarity,
- independent testability.

### Tasks
Group by ADO story. Include only necessary setup, implementation, and tests. Mark safe parallel work.

### Analyze
Resolve material spec-plan-task contradictions before coding.

### Implement
Implement one story/developer-owned slice at a time.

### Converge
Run after implementation. Implement material corrective tasks and rerun until no material gap.

## PR / Review
- Branch/PR references ADO ID.
- PR says AC covered, changes, tests and limitations.
- Tech Lead checks architecture and unrelated AI changes.
- Requirement issue → ADO/spec first; implementation defect → code fix.

## QA / UAT
- QA validates ADO Acceptance Criteria.
- Defects stay in normal ADO process.
- Requirement changes go BA/PO → ADO → SDD reconciliation.
- UAT follows normal team process.
- Close only after normal acceptance.

## Token/time controls
- One bounded story/feature at a time.
- Reuse constitution.
- No large ProjectContext.
- No ADO API/MCP integration yet.
- Focused clarify/checklist/analyze passes.
- Implement in small slices.
- Capture approximate time/credits by phase.

## What to capture
- Was the story clear enough?
- Why SDD / why not?
- BA clarification required?
- AI over-engineering?
- Multi-dev dependency/ownership issue?
- Analyze finding?
- Converge drift?
- Time/token overhead?
- Was SDD value worth the overhead?

## Immediate next actions
1. Keep Create/View as Done baseline.
2. Refine Complete/Delete in ADO.
3. Add Flask API technical enabler.
4. Add UX & Pagination.
5. Add Due Date & Overdue.
6. Add Todo Expiration.
7. Run normal refinement with BA/PO + Tech Lead + QA viewpoint.
8. Estimate and plan sprint normally.
9. Assign primary owners / necessary child tasks.
10. Mark selected stories for SDD.
11. Reuse/create short constitution once.
12. Run SDD story-by-story.
13. Return to normal PR → QA → UAT → Done.
14. Add one small normal-dev story later for comparison.
15. Capture lessons for the EA guideline, not endless Todo complexity.

> **Operating mantra:** ADO owns the business commitment and sprint flow. BA/PO owns business clarification. Spec Kit makes selected development work precise. Developers implement bounded slices. QA validates the same ADO Acceptance Criteria. EA learns from real usage and keeps the guidance light.
