# Todo SDD Pilot — Refinement to Delivery Copy/Paste Guide

**Purpose:** Run the Todo pilot exactly as a normal Scrum/ADO team would, while using Spec Kit to improve refinement and selected development work.

**Manager direction behind this guide**

- Keep Azure DevOps (ADO), BA requirements, Scrum, QA and UAT as the normal operating process.
- Do not create a separate SDD tracking system.
- Use ADO User Stories as the business requirement source.
- Use Spec Kit to help refine, specify, plan and implement selected work.
- Keep the pilot small and practical.
- Think as a real team with BA/PO, developer/lead and QA responsibilities, even if only two people are participating.
- Do not try to predict every project-specific SDD issue in advance.
- Real project pilots will generate deeper learning later.

---

# 1. Starting Point

Current application baseline:

```text
Create and View Todos → already implemented / existing baseline
```

Current stories:

```text
Complete a Todo → existing ADO User Story
Delete a Todo   → existing ADO User Story
```

New backlog/refinement candidates:

```text
Improve Todo List UX & Pagination
Due Date & Overdue Tracking
Todo Expiration
```

For the first execution, use the existing **Complete a Todo** and **Delete a Todo** stories to prove the process on an existing Todo application.

---

# 2. Roles for This Pilot

Recommended simulation:

```text
You
→ BA / Product Owner role
→ own business intent, Acceptance Criteria and business clarification

Colleague
→ Developer + Technical Lead role
→ challenge requirement gaps, dependencies and technical impact
→ later implement or review the SDD output

You + colleague
→ refinement / estimation / sprint-planning simulation

You later
→ QA role for Acceptance Criteria validation if no QA is available
```

Important:

> The developer/lead should ask questions. The BA/PO should decide business behavior. Do not let Copilot silently decide business requirements.

---

# 3. Folder Structure for Refinement Input

Create this folder in the Todo repository:

```text
input/
└── refinement/
    └── sprint-1/
```

Create one Markdown file per candidate ADO story.

Example:

```text
input/refinement/sprint-1/
├── US-<COMPLETE_ID>-Complete-A-Todo.md
└── US-<DELETE_ID>-Delete-A-Todo.md
```

Do not manually improve the ADO content before copying it.

The purpose is to use the **actual ADO story entering refinement**.

---

# 4. Copy "Complete a Todo" from ADO

Create:

```text
input/refinement/sprint-1/US-<COMPLETE_ID>-Complete-A-Todo.md
```

Use this structure:

```markdown
# ADO User Story — Complete a Todo

## Source
Azure DevOps

## ADO Work Item ID
<COMPLETE_ID>

## Title
Complete a Todo

## State
<copy current ADO state>

## Iteration Path
<copy current iteration path or Backlog>

## Story Points
<copy current value>

## Description
<PASTE EXACT ADO DESCRIPTION>

## Acceptance Criteria
<PASTE EXACT ADO ACCEPTANCE CRITERIA>

## Related Work / Dependencies
<PASTE EXISTING ADO LINKS/DEPENDENCIES IF ANY>

## Refinement Status
Not Refined
```

---

# 5. Copy "Delete a Todo" from ADO

Create:

```text
input/refinement/sprint-1/US-<DELETE_ID>-Delete-A-Todo.md
```

Use:

```markdown
# ADO User Story — Delete a Todo

## Source
Azure DevOps

## ADO Work Item ID
<DELETE_ID>

## Title
Delete a Todo

## State
<copy current ADO state>

## Iteration Path
<copy current iteration path or Backlog>

## Story Points
<copy current value>

## Description
<PASTE EXACT ADO DESCRIPTION>

## Acceptance Criteria
<PASTE EXACT ADO ACCEPTANCE CRITERIA>

## Related Work / Dependencies
<PASTE EXISTING ADO LINKS/DEPENDENCIES IF ANY>

## Refinement Status
Not Refined
```

---

# 6. First Refinement Conversation — Before Spec Kit

Sit with your colleague for 10–15 minutes.

You act as BA/PO.

Your colleague acts as Developer/Tech Lead.

For each story ask:

```text
1. What business outcome is expected?
2. Can every Acceptance Criterion be tested?
3. Is anything ambiguous?
4. Does it depend on another story?
5. What existing behavior must remain unchanged?
6. Is there any missing business rule that would block development?
7. Is the story small enough and understandable enough to estimate?
```

Possible questions for **Complete a Todo**:

```text
Can a Completed Todo be reopened?
Do we need a completed date/time?
What should the UI show for Completed?
Can Complete be called twice?
What happens if the Todo ID does not exist?
```

Possible questions for **Delete a Todo**:

```text
Hard delete or soft delete?
Should user confirm deletion?
Can Completed Todos be deleted?
What happens when ID does not exist?
Is deleted data recoverable?
```

Classify questions:

```text
Business behavior question → BA/PO decision
Technical behavior question → later /plan
Non-material question       → do not expand scope unnecessarily
```

---

# 7. Create One Capability Specification

For these two related stories, use one bounded feature:

```text
Todo Lifecycle Operations
```

Reason:

```text
Existing baseline:
Create / View

New lifecycle operations:
Complete
Delete
```

---

# 8. Run `/speckit.specify`

Attach/reference both refinement input files.

Run:

```text
/speckit.specify
```

Copy/paste:

```text
Create one bounded feature specification named:

Todo Lifecycle Operations

Authoritative business inputs:

- input/refinement/sprint-1/US-<COMPLETE_ID>-Complete-A-Todo.md
- input/refinement/sprint-1/US-<DELETE_ID>-Delete-A-Todo.md

Context:

- Create and View Todo functionality already exists and is the current working baseline.
- These stories are being evaluated/refined for an existing Todo application.
- The ADO User Story descriptions and Acceptance Criteria are the authoritative business requirement source.

Rules:

1. Preserve both ADO User Story IDs for traceability.
2. Cover the business intent and Acceptance Criteria from both User Stories.
3. Focus on WHAT the system must do and WHY.
4. Do not introduce Flask, SQLite, API route design, database schema, HTML structure or implementation tasks into the business specification.
5. Do not invent missing business decisions.
6. Surface material ambiguities, assumptions, edge cases and dependencies that should be discussed during refinement.
7. Preserve existing Create/View behavior unless the approved ADO requirements explicitly change it.
8. Keep this feature bounded to Complete and Delete Todo lifecycle operations.
9. Do not add unrelated Todo features.

Create the normal Spec Kit feature specification and keep it concise enough for refinement review.
```

Expected output:

```text
specs/
└── 00x-todo-lifecycle-operations/
    └── spec.md
```

---

# 9. Review `spec.md` With Your Colleague

Check:

```text
[ ] Complete Todo ADO ID is traceable.
[ ] Delete Todo ADO ID is traceable.
[ ] All important Acceptance Criteria are represented.
[ ] Existing Create/View behavior is preserved.
[ ] No technical implementation has entered the business specification.
[ ] No business rule was invented without ADO support.
[ ] Questions/ambiguities are visible.
```

---

# 10. Run `/speckit.clarify`

Run:

```text
/speckit.clarify
```

Copy/paste:

```text
Refine Todo Lifecycle Operations using the two source ADO User Stories.

Focus only on material ambiguities that affect:

- business behavior
- Acceptance Criteria
- dependency between Complete and Delete
- preservation of existing Create/View behavior
- QA testability

For every clarification question:

1. Explain why the answer matters.
2. Identify which ADO User Story it relates to.
3. Indicate whether it is:
   - a BA/Product Owner business decision,
   - a technical planning decision,
   - or non-material for the current scope.
4. Do not choose a business answer on behalf of the BA/Product Owner.
5. Keep the number of questions focused; do not expand the pilot unnecessarily.
```

---

# 11. Discuss Clarification Questions

Example:

```text
Copilot question:
Can a Completed Todo be reopened?

Developer/Lead:
This changes the Todo lifecycle. We need a business decision.

BA/PO:
Reopening is outside the current scope.

Decision:
No reopen capability in this story.
```

Example:

```text
Copilot question:
Should deletion be hard delete or soft delete?

Developer/Lead:
This affects persistence and recovery.

BA/PO:
For this lightweight Todo application, deletion means permanent removal.

Decision:
Hard delete.
```

Do not create complexity just because Spec Kit asks a question.

---

# 12. Record the Clarification in ADO

Use **ADO Discussion** for the decision trail.

Template:

```text
Refinement / SDD clarification

Question:
<question>

Decision:
<approved answer>

Reason:
<short reason>

Impact:
<Complete a Todo / Delete a Todo / both>

Acceptance Criteria update required:
Yes / No
```

Example:

```text
Refinement / SDD clarification

Question:
Can a Completed Todo be reopened?

Decision:
No. Reopening a Completed Todo is outside the current User Story scope.

Reason:
The current requirement only covers transitioning an incomplete Todo to Completed.

Impact:
Complete a Todo

Acceptance Criteria update required:
Yes
```

---

# 13. Update ADO Acceptance Criteria

Rule:

```text
ADO Discussion = why / conversation / decision history
ADO Acceptance Criteria = approved system behavior QA will validate
```

If clarification changes or makes business behavior explicit, update ADO Acceptance Criteria.

Do not add purely technical implementation choices such as:

```text
Use HTTP DELETE
Use SQLite DELETE
Use Flask route
```

Those belong in `/plan`.

---

# 14. Refresh the Local Story Snapshot

After ADO is updated, update each local file to match ADO.

Change:

```text
## Refinement Status
Refined
```

Optional:

```markdown
## Refinement Decisions
- <decision 1>
- <decision 2>
```

ADO remains authoritative.

---

# 15. Reconcile `spec.md`

Run only if needed:

```text
/speckit.clarify
```

Prompt:

```text
The ADO User Stories were updated after refinement.

Reconcile Todo Lifecycle Operations with the latest approved Acceptance Criteria in:

- input/refinement/sprint-1/US-<COMPLETE_ID>-Complete-A-Todo.md
- input/refinement/sprint-1/US-<DELETE_ID>-Delete-A-Todo.md

Apply the approved business decisions to spec.md.

Do not introduce new requirements.
Report any remaining material business ambiguity.
```

Stop when no material business questions remain.

---

# 16. Definition of Ready

Check each story:

```text
[ ] Business outcome is clear.
[ ] Acceptance Criteria are clear and testable.
[ ] Material business clarifications are resolved.
[ ] ADO contains the approved behavior.
[ ] Major dependencies are understood.
[ ] Existing behavior to preserve is understood.
[ ] spec.md is aligned with ADO.
[ ] No blocking requirement question remains.
[ ] Story can be estimated.
```

If all pass, add ADO tag:

```text
Ready-For-Sprint
```

Optional:

```text
SDD-Candidate
```

Do **not** create a custom ADO workflow state just for SDD.

---

# 17. Add Refinement Completion Comment in ADO

Copy/paste:

```text
Refinement completed.

- User Story and Acceptance Criteria reviewed.
- SDD draft specification created and reviewed.
- Material clarification questions discussed.
- Approved business decisions reflected in ADO Acceptance Criteria.
- Dependencies and existing behavior reviewed.
- No blocking requirement questions remain.
- Story is ready for estimation / Sprint Planning.

Status: Ready-For-Sprint
```

---

# 18. Sprint Planning

For each Ready-For-Sprint story:

```text
1. Review latest Acceptance Criteria.
2. Review refinement findings.
3. Revisit Story Points.
4. Check dependencies/capacity.
5. Decide whether story enters the Sprint.
6. Assign developer.
7. Set Iteration Path.
```

Ask:

```text
After refinement, is the original estimate still valid?
```

---

# 19. Developer Assignment

Example:

```text
Complete a Todo
→ Developer A / colleague

Delete a Todo
→ Developer B / you or simulated second developer
```

One primary developer owns each User Story.

---

# 20. Run `/speckit.plan` After Sprint Commitment

Run:

```text
/speckit.plan
```

Copy/paste:

```text
Create the technical implementation plan for Todo Lifecycle Operations.

Approved requirement sources:

- latest spec.md
- ADO-refined Complete a Todo User Story
- ADO-refined Delete a Todo User Story

Existing application baseline:

- Create and View Todo already works.
- Preserve current working behavior.

Technical direction for this lightweight pilot:

- Python
- Flask
- SQLite
- Flask REST API where appropriate
- simple server-rendered UI
- pytest

Keep the design minimal.

Plan only what is required for:

1. Complete a Todo
2. Delete a Todo
3. Required shared API/persistence changes
4. Automated and regression tests

Do not introduce:
- React
- cloud infrastructure
- Docker
- authentication
- microservices
- a second backend framework
- unnecessary abstraction layers

Clearly identify shared foundational work and story-specific work.
```

---

# 21. API Design Belongs in Plan

Possible routes:

```text
GET    /api/todos
POST   /api/todos
PUT    /api/todos/{id}/complete
DELETE /api/todos/{id}
```

Do not update BA Acceptance Criteria merely because implementation uses these routes unless the API itself is a requirement.

---

# 22. Run Focused `/speckit.checklist`

Run:

```text
/speckit.checklist
```

Prompt:

```text
Create a lightweight readiness checklist for Todo Lifecycle Operations.

Check only:

- Complete a Todo Acceptance Criteria coverage
- Delete a Todo Acceptance Criteria coverage
- no invented business behavior
- preservation of existing Create/View behavior
- resolved refinement clarifications
- API/data/UI impact is sufficiently clear for development
- each User Story can be independently tested
- regression expectations are covered

Keep the checklist concise.
```

Business gap → return to ADO/refinement.

Technical gap → fix plan.

---

# 23. Run `/speckit.tasks`

Run:

```text
/speckit.tasks
```

Prompt:

```text
Generate dependency-ordered implementation tasks for Todo Lifecycle Operations.

Group tasks by ADO User Story:

- <COMPLETE_ID> Complete a Todo
- <DELETE_ID> Delete a Todo

Include:

- only necessary shared foundational work
- backend/API work
- persistence work
- UI work where required
- automated tests
- regression tests for existing Create/View behavior

Mark safely parallel work.

Keep the task list lightweight.

Do not generate tasks for Due Date, Expiration, Pagination or other backlog stories.
```

Do not copy every Spec Kit task into ADO.

---

# 24. Run `/speckit.analyze`

Run:

```text
/speckit.analyze
```

Review:

```text
ADO AC missing from spec/tasks
spec contradicts plan
task has no requirement
shared dependency not handled
Complete/Delete conflict
existing Create/View regression risk missing
```

Fix material issues only.

---

# 25. Implement "Complete a Todo"

Run:

```text
/speckit.implement
```

Prompt:

```text
Implement only:

ADO <COMPLETE_ID> — Complete a Todo

and any shared foundational tasks required for this story.

Requirements:

- follow the approved Todo Lifecycle Operations specification
- follow the approved technical plan
- preserve existing Create/View behavior
- do not implement Delete a Todo yet unless a shared foundational change is unavoidable
- run all relevant automated tests
- run regression tests for existing Todo behavior
- stop once Complete a Todo independently satisfies its ADO Acceptance Criteria

Report:
- tasks completed
- tests run
- any assumption or deviation
```

---

# 26. Implement "Delete a Todo"

Run:

```text
/speckit.implement
```

Prompt:

```text
Implement only:

ADO <DELETE_ID> — Delete a Todo

Requirements:

- preserve existing Create/View behavior
- preserve completed Complete-a-Todo behavior
- follow the approved specification and plan
- do not implement unrelated backlog features
- run relevant automated and regression tests
- stop when Delete a Todo independently satisfies its ADO Acceptance Criteria

Report:
- tasks completed
- tests run
- any assumption or deviation
```

---

# 27. PR / Review Template

```markdown
## ADO User Story

<ADO ID> - <Title>

## Acceptance Criteria Covered

- AC1:
- AC2:
- AC3:

## Technical Changes

- ...

## Tests

- ...

## SDD Validation

- Spec reviewed: Yes
- Analyze clean: Yes
- Converge: Pending / Complete

## Known Limitations

- None / ...
```

Requirement issue → ADO/spec first.

Implementation defect → code fix.

---

# 28. Run `/speckit.converge`

Run:

```text
/speckit.converge
```

Goal:

```text
ADO requirement
      ↕
spec.md
      ↕
plan.md
      ↕
tasks.md
      ↕
code
      ↕
tests
```

Implement material corrective tasks and rerun until no material gap remains.

---

# 29. Developer Handoff to QA

ADO Discussion comment:

```text
Development completed.

SDD checkpoints:
- Specification reviewed
- Clarifications resolved
- Plan/tasks completed
- Analyze has no material blocker
- Implementation completed
- Automated/regression tests passed
- Converge has no material gap

Ready for QA.

Please validate against the latest ADO Acceptance Criteria.
```

---

# 30. QA Validation

QA validates ADO Acceptance Criteria.

If you simulate QA:

```text
ADO <ID>

AC1 → PASS
AC2 → PASS
AC3 → PASS
AC4 → PASS

Regression:
Create/View → PASS

Result:
PASS / FAIL
```

Implementation bug → fix code.

Requirement change → BA/PO → ADO → reconcile SDD artifacts → code.

---

# 31. ADO Completion

Use the existing ADO process.

Example only:

```text
New / Ready
→ Active / In Progress
→ Ready for QA / Resolved
→ QA/UAT
→ Closed / Done
```

Do not introduce SDD-specific states.

Optional tags only:

```text
SDD-Candidate
Ready-For-Sprint
SDD-Selected
```

---

# 32. What to Record for Manager Learning

```text
Story:
Complete / Delete

Initial Story Points:
...

Refinement questions found:
...

ADO Acceptance Criteria updated:
Yes / No

Clarify useful?
Yes / No + one sentence

Estimate after refinement:
...

Analyze finding:
...

Implementation time:
...

Token / premium-request observation:
...

Converge finding:
...

QA result:
...

Was full SDD worth it for this story?
...
```

The objective is:

```text
Where does SDD help?
Where is it overhead?
What should a normal Scrum team repeat?
```

---

# 33. After Complete/Delete

Use the same refinement pattern for:

```text
Due Date & Overdue Tracking
Todo Expiration
Improve Todo List UX & Pagination
```

Possible future grouping:

```text
Todo Time & Lifecycle Management
- Due Date & Overdue Tracking
- Todo Expiration
```

Possible separate capability:

```text
Todo List Experience
- Improve Todo List UX & Pagination
```

Decide grouping during refinement; do not force it.

---

# 34. End-to-End Cheat Sheet

```text
ADO CANDIDATE USER STORY
        ↓
Copy exact story to input/refinement/
        ↓
Refinement discussion
        ↓
/speckit.specify
        ↓
Review draft spec
        ↓
/speckit.clarify
        ↓
BA/PO + Dev/Lead discussion
        ↓
ADO Discussion
        ↓
Update ADO Acceptance Criteria if needed
        ↓
Refresh local ADO snapshot
        ↓
Reconcile spec
        ↓
Definition of Ready
        ↓
Tag: Ready-For-Sprint
        ↓
Sprint Planning
        ↓
Re-estimate / Commit / Assign
        ↓
/speckit.plan
        ↓
/speckit.checklist
        ↓
/speckit.tasks
        ↓
/speckit.analyze
        ↓
/speckit.implement
        ↓
PR / Review
        ↓
/speckit.converge
        ↓
Ready for QA
        ↓
QA validates ADO Acceptance Criteria
        ↓
UAT if required
        ↓
ADO Done
```

---

# 35. One Sentence to Explain the Process

> We use the ADO User Story as the authoritative requirement, use Spec Kit during refinement to expose ambiguities and create a stronger specification, push approved business clarifications back into ADO, mark the story Ready-for-Sprint, then use the same approved specification during planning and development while QA continues validating the ADO Acceptance Criteria.

---

# 36. First Action on Your Laptop

```text
1. Open Complete a Todo in ADO.
2. Copy exact Description + Acceptance Criteria.
3. Create:
   input/refinement/sprint-1/US-<ID>-Complete-A-Todo.md

4. Open Delete a Todo in ADO.
5. Copy exact Description + Acceptance Criteria.
6. Create:
   input/refinement/sprint-1/US-<ID>-Delete-A-Todo.md

7. Sit with colleague.
8. Run /speckit.specify using both files.
9. Stop.
10. Review spec.md together before /speckit.clarify.
```

Do not jump directly to coding.
