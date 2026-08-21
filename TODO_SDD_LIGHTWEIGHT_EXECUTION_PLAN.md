# Todo App SDD Pilot — Lightweight Execution Plan

**Purpose:** Prove how a normal Scrum/ADO team can use Spec-Driven Development (SDD) inside the development phase without changing the existing BA, ADO, QA, or UAT process.

**Pilot style:** Small, from-scratch Todo application. Keep it lightweight, low-token, quick to finish, and easy to demonstrate.

**Recommended stack:** Python + Flask + SQLite + server-rendered HTML + pytest.

---

## 1. What We Are Proving

We are **not** trying to prove every possible SDD edge case.

We are proving this practical flow:

```text
BA / Product Owner
        ↓
ADO Feature + User Stories + Acceptance Criteria
        ↓
Sprint Planning / Grooming
        ↓
Developer takes approved User Story
        ↓
Spec Kit / SDD
        ↓
Specify → Clarify → Plan → Checklist → Tasks → Analyze → Implement → Converge
        ↓
Normal QA / UAT
        ↓
ADO remains the tracking system
```

### Key rule

> **ADO owns the business requirement and sprint tracking. Spec Kit makes the development work precise. QA validates the same acceptance criteria.**

---

## 2. Keep the Pilot Small

### Do

- Build one tiny Todo application from scratch.
- Use Flask + SQLite.
- Start with basic CRUD functionality.
- Use only 2–3 User Stories in Sprint 1.
- Add one meaningful enhancement in Sprint 2.
- Keep one project-wide constitution.
- Implement one User Story at a time.
- Capture only useful findings.

### Do not

- Use the RLLOT dashboard as the main pilot.
- Add React, Docker, cloud services, authentication, queues, or microservices.
- Build ADO API/MCP integration now.
- Generate a huge project-discovery document.
- Try to predict every issue another project might face.
- Copy all Spec Kit technical tasks into ADO.

---

# 3. Application Scope

## Sprint 1 — Core Todo Management

Build the minimum usable application.

### Core Todo fields

```text
Id
Title
IsCompleted
CreatedAt
```

### Required functionality

1. Create a Todo.
2. View Todos.
3. Mark a Todo as Completed.
4. Delete a Todo.

This gives us a real working application before adding later enhancements.

---

# 4. Create the ADO Structure

Create or reuse your SDD Experiment team/area/iteration.

Example:

```text
Team / Area:
SDD Experiment

Iteration:
SDD Pilot - Sprint 1
```

## ADO Feature

**Title**

```text
Core Todo Management
```

**Description**

```text
Provide the minimum Todo management capability required for users to create,
view, complete, and remove Todo items.

This feature is being used as a lightweight SDD pilot to demonstrate how
approved ADO requirements flow through Spec Kit into implementation while
keeping the normal Scrum, QA, and ADO process unchanged.
```

---

# 5. Sprint 1 User Stories

## US1 — Create and View Todos

### User Story

```text
As a Todo user,
I want to create a Todo and view my Todo list,
so that I can track work I need to complete.
```

### Acceptance Criteria

```text
1. The user can enter a Todo title.
2. The user can create the Todo.
3. A newly created Todo appears in the Todo list.
4. A Todo is initially created as not completed.
5. An empty or whitespace-only title is not accepted.
6. Existing Todo items remain available after the application restarts.
```

### Why this story exists

This creates the minimum useful Todo behavior and proves the first complete ADO → SDD → code path.

---

## US2 — Complete a Todo

### User Story

```text
As a Todo user,
I want to mark a Todo as completed,
so that I can distinguish finished work from unfinished work.
```

### Acceptance Criteria

```text
1. The user can mark an existing incomplete Todo as completed.
2. The completed state is persisted.
3. A completed Todo is visibly identified as completed.
4. Completing one Todo does not modify other Todo items.
```

### Why this story exists

This adds state change behavior and gives Spec Kit something more meaningful than only basic data entry.

---

## US3 — Delete a Todo

### User Story

```text
As a Todo user,
I want to delete a Todo that I no longer need,
so that obsolete Todo items do not remain in my list.
```

### Acceptance Criteria

```text
1. The user can delete an existing Todo.
2. The deleted Todo no longer appears in the Todo list.
3. Deleting one Todo does not affect other Todo items.
4. Refreshing or restarting the application does not restore the deleted Todo.
```

### Why this story exists

This completes the minimum CRUD lifecycle while keeping the feature small.

---

# 6. What Should Stay Out of ADO User Stories

Do not put technical implementation instructions such as:

```text
Create Flask route
Create SQLite table
Add Python class
Write SQL query
Add HTML button
Create pytest fixture
```

Those belong to Spec Kit planning/tasks.

ADO should contain:

```text
Business intent
Acceptance Criteria
Priority
Sprint
Owner
Status
```

---

# 7. Create the Tiny Project

Create a new folder:

```text
todo-sdd-pilot/
```

Recommended structure:

```text
todo-sdd-pilot/
│
├── app.py
├── requirements.txt
├── todo.db                # generated at runtime
├── templates/
│   └── index.html
├── tests/
│   └── test_todo.py
├── docs/
│   ├── ProjectContext.md
│   └── ado/
│       └── Sprint-1.md
└── .specify/
```

---

# 8. Initialize the Python Project

From terminal:

```bash
python -m venv .venv
```

### Windows PowerShell

```powershell
.\.venv\Scripts\Activate.ps1
```

Install the minimum packages:

```bash
pip install flask pytest
```

Create `requirements.txt`:

```bash
pip freeze > requirements.txt
```

Do not add an ORM for this pilot unless Spec Kit gives a strong reason and you approve it.

Python's built-in `sqlite3` module is enough.

---

# 9. Create a Very Small Project Context

Before creating the constitution, create:

```text
docs/ProjectContext.md
```

Use Copilot:

```text
Create docs/ProjectContext.md for this lightweight Todo SDD pilot.

Keep it under one page.

Project purpose:
- lightweight Todo application used to demonstrate Scrum + ADO + Spec Kit adoption

Technical constraints:
- Python
- Flask
- SQLite using Python built-in sqlite3
- server-rendered HTML
- pytest
- no authentication
- no React
- no cloud services
- no Docker
- no unnecessary frameworks
- keep the codebase intentionally small

Quality expectations:
- acceptance criteria must be testable
- important behavior must have automated tests
- do not introduce business behavior that is not approved in ADO
- avoid unnecessary architecture

Do not add feature-specific Todo requirements here.
```

Review the file manually.

---

# 10. Create the Constitution Once

Run:

```text
/speckit.constitution
```

Use this input:

```text
Create a lightweight project constitution for this Todo SDD pilot.

Use docs/ProjectContext.md as the project context.

Project-wide principles:

1. ADO/BA-approved User Stories and Acceptance Criteria are the authoritative
   source of business requirements.

2. Spec Kit and developers must not silently invent missing business behavior.
   Material ambiguity must be clarified.

3. Keep the solution intentionally simple and consistent with the approved
   Python + Flask + SQLite architecture.

4. Deliver small, independently testable User Story slices.

5. Important business behavior must have automated tests.

6. Before completion, implementation must be validated against the approved
   specification, plan, and tasks.

7. Avoid unnecessary frameworks, infrastructure, or architectural complexity.

Keep the constitution concise and project-wide.

Do not put Sprint 1 Todo CRUD requirements into the constitution.
```

### Important

Create the constitution **once for the project**.

Do not create a new constitution for every User Story or Sprint.

---

# 11. Capture the ADO Stories for Spec Kit

After creating the stories in ADO, create:

```text
docs/ado/Sprint-1.md
```

Use this format:

```markdown
# SDD Pilot - Sprint 1

Source: Azure DevOps

## Feature

ADO Feature ID: <ID>
Title: Core Todo Management

---

## User Story 1

ADO ID: <ID>
Title: Create and View Todos

Description:
<copy exactly from ADO>

Acceptance Criteria:
<copy exactly from ADO>

---

## User Story 2

ADO ID: <ID>
Title: Complete a Todo

Description:
<copy exactly from ADO>

Acceptance Criteria:
<copy exactly from ADO>

---

## User Story 3

ADO ID: <ID>
Title: Delete a Todo

Description:
<copy exactly from ADO>

Acceptance Criteria:
<copy exactly from ADO>
```

### Rule

> ADO is still the source of truth. This file is only the input snapshot used by Spec Kit.

If BA changes ADO, update ADO first and then refresh this file.

---

# 12. Sprint 1 — Run `/speckit.specify`

Run:

```text
/speckit.specify
```

Prompt:

```text
Create one bounded feature specification for Core Todo Management.

Authoritative requirement source:
docs/ado/Sprint-1.md

Project context:
docs/ProjectContext.md

Rules:

- Cover the three approved ADO User Stories.
- Preserve the ADO User Story IDs for traceability.
- Focus on WHAT and WHY.
- Do not include Flask, SQLite, Python, SQL, HTML, or implementation design.
- Do not invent new business behavior.
- If an important business rule is missing or ambiguous, identify it for
  clarification rather than silently deciding it.
- Keep this feature small.

Generate:
- prioritized user scenarios
- functional requirements
- acceptance scenarios
- edge cases
- key entities
- measurable success criteria
```

---

# 13. Review the Specification Manually

Before continuing, check:

```text
☐ US1 is covered.
☐ US2 is covered.
☐ US3 is covered.
☐ Acceptance Criteria are represented correctly.
☐ No technical architecture has leaked into the spec.
☐ No unsupported business rule was invented.
☐ The feature is still small.
```

Do not blindly accept the generated spec.

---

# 14. Run `/speckit.clarify`

Run:

```text
/speckit.clarify
```

Prompt:

```text
Review the Core Todo Management specification against:

- docs/ado/Sprint-1.md
- docs/ProjectContext.md

Ask only questions that materially affect business behavior or acceptance.

For each question:
- state why it matters
- indicate whether BA/Product Owner confirmation is required

Do not answer business questions on behalf of the BA.

Keep the clarification pass lightweight.
```

---

# 15. If Clarify Finds a Business Gap

Example:

```text
Should duplicate Todo titles be allowed?
```

Do not let AI decide if this changes business behavior.

Use:

```text
Spec Kit question
        ↓
Ask BA / Product Owner
        ↓
Update ADO Acceptance Criteria
        ↓
Refresh docs/ado/Sprint-1.md
        ↓
Update / clarify spec
```

This is one of the main things the pilot should demonstrate.

---

# 16. Run `/speckit.plan`

Once the specification is approved:

```text
/speckit.plan
```

Prompt:

```text
Create the technical implementation plan for Core Todo Management.

Use:
- approved specification
- docs/ProjectContext.md
- project constitution

Use the approved lightweight architecture:

- Python
- Flask
- Python built-in sqlite3
- SQLite
- server-rendered HTML
- pytest

Keep the design minimal.

Expected scope:
- Todo persistence
- create/list Todo behavior
- complete Todo behavior
- delete Todo behavior
- input validation
- HTML UI
- automated tests

Do not add authentication, React, APIs for external consumers, Docker,
cloud infrastructure, background jobs, ORMs, or unnecessary abstractions.
```

---

# 17. Run One Lightweight Checklist

Run:

```text
/speckit.checklist
```

Prompt:

```text
Create a focused requirements-quality checklist for this pilot.

Check only:

- coverage of all ADO Acceptance Criteria
- missing or ambiguous business behavior
- accidental technical details in the specification
- accidental business behavior not present in ADO
- independent testability of US1, US2 and US3
- regression risk between stories

Keep the checklist small.
```

If it exposes a real business gap, return to ADO/Clarify.

---

# 18. Run `/speckit.tasks`

Run:

```text
/speckit.tasks
```

Prompt:

```text
Generate dependency-ordered implementation tasks for Core Todo Management.

Group implementation tasks by User Story:

US1 - Create and View Todos
US2 - Complete a Todo
US3 - Delete a Todo

Include:
- minimum setup/foundational work
- automated tests
- only necessary implementation tasks

Mark safely parallelizable work where appropriate.

Keep the task list small and implementation-focused.
```

Expected shape:

```text
Setup / Foundational
        ↓
US1 Create + View
        ↓
US2 Complete
        ↓
US3 Delete
        ↓
Regression / final validation
```

---

# 19. Simulate a Real Team

Even if you execute alone, explain how a team could split it.

Example:

```text
Developer A
→ foundational + US1

Developer B
→ US2

Developer C
→ US3

QA
→ validates ADO acceptance criteria

Tech Lead
→ reviews spec/plan/analyze/converge
```

Do not create separate duplicate specifications for each developer.

---

# 20. Run `/speckit.analyze`

Before coding:

```text
/speckit.analyze
```

Stop if there is a material issue such as:

```text
ADO Acceptance Criterion missing from spec/tasks
Plan contradicts spec
Task has no requirement
Technical design changes business behavior
User Story is not independently testable
```

Fix the owning artifact and rerun Analyze.

Do not keep rerunning it unnecessarily once the important issues are resolved.

---

# 21. Implement Sprint 1 Story by Story

## Implement US1 first

```text
/speckit.implement

Implement only foundational tasks and US1 - Create and View Todos.

Run relevant automated tests.

Stop when US1 independently satisfies its approved acceptance criteria.
Do not implement US2 or US3 yet.
```

Then:

```text
/speckit.implement

Implement only US2 - Complete a Todo.

Preserve US1 behavior.
Run relevant automated and regression tests.
```

Then:

```text
/speckit.implement

Implement only US3 - Delete a Todo.

Preserve US1 and US2 behavior.
Run relevant automated and regression tests.
```

---

# 22. Run `/speckit.converge`

After all three stories:

```text
/speckit.converge
```

Check:

```text
Specification
        ↕
Plan
        ↕
Tasks
        ↕
Code
        ↕
Tests
```

If Converge creates corrective tasks:

```text
Implement corrective tasks
        ↓
Run tests
        ↓
Converge again
```

Stop when there are no material gaps.

---

# 23. Return to Normal ADO + QA

After development:

```text
Developer completes coding
        ↓
Update ADO story status normally
        ↓
Normal PR / review if used
        ↓
QA validates ADO Acceptance Criteria
        ↓
UAT if required
        ↓
Close User Stories normally
```

Do not create a separate SDD tracking system.

---

# 24. Sprint 2 — Meaningful Enhancement

Only after Sprint 1 works, add a second feature.

## Feature

```text
Todo Expiration
```

Recommended User Stories:

```text
US4 - Automatically expire incomplete Todo items after 30 days.

US5 - View expired Todo items separately.
```

This Sprint is useful because now Spec Kit must modify an existing application rather than create the first baseline.

---

# 25. Sprint 2 — US4

## User Story

```text
As a Todo user,
I want stale incomplete Todo items to expire after 30 days,
so that my active list remains relevant.
```

## Acceptance Criteria

```text
1. An incomplete Todo becomes Expired after 30 calendar days from CreatedAt.
2. Expiration does not physically delete the Todo.
3. Completed Todos do not become Expired.
4. Before 30 days, existing behavior remains unchanged.
5. Expired state is persisted.
```

---

# 26. Sprint 2 — US5

## User Story

```text
As a Todo user,
I want to view expired Todo items separately,
so that expired items do not clutter my current Todo list.
```

## Acceptance Criteria

```text
1. Expired Todos are excluded from the default active view.
2. An Expired view/filter is available.
3. The Expired view shows only expired Todos.
4. Existing active/completed behavior continues to work.
5. Expired Todos are visibly identified.
```

Run the same lightweight SDD cycle again:

```text
ADO
 ↓
Specify
 ↓
Clarify
 ↓
Plan
 ↓
Checklist
 ↓
Tasks
 ↓
Analyze
 ↓
Implement
 ↓
Converge
 ↓
QA / ADO
```

---

# 27. Token and Time Control Rules

Use these throughout the pilot:

```text
1 feature at a time
2–3 User Stories maximum per Sprint
small project context
one project-wide constitution
one focused clarification pass
one focused checklist
one analyze pass unless real issues appear
implement story by story
avoid unnecessary files and frameworks
do not over-document project-specific edge cases
```

Record rough usage:

| Stage | Approx. time | Notes |
|---|---:|---|
| ADO | | |
| Constitution | | |
| Specify | | |
| Clarify | | |
| Plan | | |
| Checklist | | |
| Tasks | | |
| Analyze | | |
| Implement | | |
| Converge | | |
| QA | | |

Do not invent exact historical token numbers. Record them as you execute.

---

# 28. Evidence to Capture

Only capture useful learning.

### ADO

```text
Story IDs
Acceptance Criteria
Sprint assignment
```

### Specify

```text
Any wrong assumption generated?
Any missing BA requirement identified?
```

### Clarify

```text
Question asked
Who decided?
Was ADO updated?
```

### Plan

```text
Did AI over-engineer?
Did you reject unnecessary architecture?
```

### Analyze

```text
Material gaps found
```

### Implement

```text
Time
Credit/token usage
Unexpected code issue
```

### Converge

```text
Implementation drift found?
Corrective tasks?
Final status?
```

---

# 29. Pilot Success Criteria

The pilot is successful if:

```text
☐ ADO remains the requirement and sprint source.
☐ Spec Kit uses the approved User Stories.
☐ Missing business requirements are clarified instead of invented.
☐ The constitution is reused project-wide.
☐ The generated spec/plan/tasks are small enough to review.
☐ User Stories can be distributed to developers.
☐ Analyze catches inconsistencies before coding if they exist.
☐ The app satisfies all ADO Acceptance Criteria.
☐ Existing behavior remains intact across stories.
☐ Converge reports no material gaps.
☐ Normal QA/ADO process still works.
☐ The pilot finishes quickly enough to be repeatable.
```

---

# 30. What to Show Your Manager

A short demo is enough.

```text
1. Show ADO Sprint + Feature + User Stories.
2. Show the one-time constitution.
3. Show how ADO User Stories became one Spec Kit feature spec.
4. Show one clarification, if any.
5. Show plan + tasks grouped by User Story.
6. Show the working application.
7. Show Analyze/Converge result.
8. Show QA acceptance against the same ADO criteria.
9. Show approximate time/token usage.
10. Explain what project teams should repeat.
```

---

# 31. One-Sentence Pilot Message

> **We kept the existing Scrum/ADO process unchanged and inserted Spec Kit into the development phase, using approved User Stories to drive specification, planning, implementation, and validation in small reviewable increments.**

---

# 32. Immediate Execution Order

When you continue on your laptop, follow exactly this:

```text
1. Create project folder.
2. Create Python virtual environment.
3. Install Flask + pytest.
4. Create Sprint 1 in ADO.
5. Create Feature: Core Todo Management.
6. Create US1, US2, US3.
7. Create docs/ProjectContext.md.
8. Initialize Spec Kit.
9. Create/review constitution.
10. Create docs/ado/Sprint-1.md from ADO.
11. Run /speckit.specify.
12. Review spec.
13. Run /speckit.clarify.
14. Update ADO if business clarification changes a requirement.
15. Run /speckit.plan.
16. Run focused /speckit.checklist.
17. Run /speckit.tasks.
18. Run /speckit.analyze.
19. Implement US1.
20. Test.
21. Implement US2.
22. Test.
23. Implement US3.
24. Test.
25. Run /speckit.converge.
26. Run final application/regression tests.
27. Return to normal ADO/QA flow.
28. Capture learning/time/usage.
29. Only then start Sprint 2 - Todo Expiration.
```

---

**Recommended first stopping point:** Finish steps 1–6 (project + ADO Feature/User Stories). Review them before running Spec Kit.
