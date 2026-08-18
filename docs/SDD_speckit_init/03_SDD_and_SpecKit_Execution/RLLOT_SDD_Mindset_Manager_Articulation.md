# RLLOT Modernization — How I Applied the SDD Mindset

## Purpose

This document explains how I approached the RLLOT Production Rollout Dashboard modernization from an **SDD mindset**, before implementation or Spec Kit execution.

It is intended to help articulate the thinking if asked:

- How did you approach the problem first?
- What did you do before `/speckit.specify`?
- How is this different from simply using Copilot to generate documents?
- How did the SDD mindset influence the modernization approach?

---

## 1. Requirement I Received

The requirement was not simply:

> Convert the current dashboard to React or .NET.

The actual problem was broader:

> Use the existing Production Rollout Dashboard as a real greenfield use case for Spec-Driven Development / GitHub Spec Kit, modernize the solution, and use the learning to help establish a practical SDD guideline.

The current dashboard was also identified as not sustainable because:

- it generates one large static HTML file,
- all data is loaded up front,
- the file grows as more history and detail are added,
- wider organizational access will eventually require proper server-side access control,
- richer filtering and drill-down should be possible without loading everything into the browser.

---

## 2. First Thing I Did

I did **not** start by selecting React, ASP.NET Core, SQLite, or any other technology.

I also did **not** start by immediately running:

```text
/speckit.specify
```

The first thing I did was to **frame the problem independently of the current implementation**.

The first questions were:

```text
What business problem does this dashboard solve?

Who uses it?

What information do they rely on?

What behavior must remain correct?

What limitations of the current system are we trying to remove?

What constraints do we have?

What is still unknown?
```

This was the starting point.

---

## 3. I Separated the Problem Space from the Solution Space

The current system contains both:

1. **business behavior that matters**, and
2. **legacy implementation choices that happened to deliver that behavior**.

The SDD mindset required separating these two.

### Business behavior to preserve

Examples:

- six rollout initiatives,
- Phase 1 / Phase 2 / Phase 3 status,
- Customer Approval,
- overdue calculation,
- Productivity / Quality / Coverage metrics,
- Pre/Post GenAI handling,
- overview KPIs,
- filters,
- team details,
- remarks,
- metrics explanation,
- cross-DR consolidation.

### Legacy implementation choices that do not need to survive

Examples:

```text
Python
    ↓
JSON
    ↓
one generated HTML file
    ↓
all data embedded in browser
```

Also:

- positional Excel column mapping,
- duplicated calculations in Python and JavaScript,
- client-side-only filtering,
- static HTML regeneration for every refresh.

The key decision was:

> **Preserve the business semantics, not the old architecture.**

---

## 4. I Used the Existing System as Evidence, Not as the New Specification

The current project was useful because many important rules were already encoded in the implementation.

I used the existing project to understand:

- Excel structure,
- file naming conventions,
- six target sheets,
- normalization rules,
- phase formulas,
- overdue rules,
- metric calculations,
- edge cases,
- current UI behavior.

Copilot was used to help inspect and document this existing behavior faster.

However:

> **The Copilot-generated documents were not treated as the new specification.**

They were treated as **reverse-engineering/reference material**.

The process was:

```text
Decide what needs to be understood
        ↓
Inspect current system/code
        ↓
Capture existing behavior
        ↓
Validate what is business behavior
        ↓
Separate it from implementation detail
        ↓
Use the result as discovery input
```

Not:

```text
Ask Copilot to generate spec
        ↓
Accept generated document
        ↓
Start implementation
```

---

## 5. I Identified the Business Invariants

A major SDD step was identifying the things that should remain true regardless of technology.

### Phase completion

```text
Completed / Active × 100
```

where Active includes:

```text
Completed
In Progress
Open
On Hold
Not Filled
```

and excludes:

```text
NA
TBD
Rejected
```

### Per-initiative overall completion

```text
Average(P1 %, P2 %, P3 %)
```

### Overdue

A phase is overdue only when:

```text
Status = Open or In Progress
AND
ETA is in the past
```

The current implementation also considers the Customer Approval prerequisite date.

### Metrics

The existing lower-is-better / higher-is-better behavior, post-average metrics, excluded summary metrics, and initiative-specific productivity metrics were captured as business behavior.

These rules became **compatibility requirements**, not code that must be copied.

---

## 6. I Identified What Should Be Improved, Not Preserved

The SDD mindset does not mean reproducing every legacy behavior exactly.

Some behaviors were identified as implementation weaknesses.

Example:

The current extractor uses fixed positions for columns 0–16.

```text
10 = Phase 1 Status
12 = Phase 2 Status
14 = Phase 3 Status
```

This creates a risk that inserting a column in Excel silently breaks the mapping.

The modernization decision is therefore:

> Preserve the meaning of the fields, but replace fixed positional mapping with header/schema-based validation.

This is an example of preserving the business contract while improving the implementation.

---

## 7. I Captured the UX as a Reference, Not a Pixel-Perfect Requirement

The current dashboard screenshots were reviewed to understand the user workflow.

The existing information hierarchy is:

```text
Global filters
      ↓
Overview
      ↓
Select initiative
      ↓
Initiative summary
      ↓
Filter/search teams
      ↓
Team table
      ↓
Open details
      ↓
Metrics + explanation + remarks
```

This workflow is useful and should largely be preserved.

However, the modernization does not require:

- identical colors,
- identical CSS,
- identical card sizes,
- identical HTML structure.

The goal is:

> Preserve the user journey and information architecture while replacing the underlying static data-loading model.

---

## 8. I Identified the Constraints Before Finalizing Architecture

The most important project constraint is:

> **Zero additional infrastructure/service cost for the first implementation.**

Other relevant constraints:

- existing internal IIS / Windows infrastructure should be reused where practical,
- internal users,
- current Excel remains the business input initially,
- no need for big-data architecture,
- current user/data volume is relatively small,
- SharePoint automation should not block the main modernization.

This prevented choosing technology without context.

---

## 9. I Identified Unknowns Instead of Making Assumptions

Before treating the architecture as final, I identified the risky assumptions that need validation.

Examples:

```text
Can ASP.NET Core run on the existing IIS server?

Is the required .NET runtime/Hosting Bundle available?

Is Windows Authentication allowed?

Can SQLite be stored on the internal server?

Is an existing SQL Server already available at zero incremental cost?

Does security policy allow SQLite?

Should production ingestion remain Python or be ported to .NET?
```

These are not specification questions.

They are **architecture feasibility questions**.

The SDD mindset here is:

> Make uncertainty explicit instead of silently designing around assumptions.

---

## 10. I Defined a Greenfield Modernization Direction

After understanding the problem, rules, constraints, and unknowns, the proposed target became:

```text
Manual / controlled Excel input initially
        ↓
Dedicated ingestion component
        ↓
Database-agnostic relational persistence
        ↓
SQLite for V1
        ↓
ASP.NET Core API
        ↓
React dashboard
        ↓
Existing IIS
```

Important principles:

- SQLite is the V1 provider, not a permanent dependency.
- Future migration to SQL Server/PostgreSQL should remain possible.
- Business logic should not depend on SQLite.
- The browser should retrieve only required data.
- Server-side authorization should be possible.
- SharePoint/Graph automation is deferred until the main modernization works.

---

## 11. I Did Not Let SharePoint Automation Distract from the Main Goal

Automatic SharePoint retrieval using Microsoft Graph is useful, but it introduces:

- tenant/app registration,
- permission approval,
- identity setup,
- security dependencies.

It is not the main modernization problem.

Therefore:

```text
V1:
Manual/controlled Excel input
        ↓
Modern ingestion
        ↓
Database
        ↓
Dynamic application
```

Later:

```text
SharePoint
    ↓
Microsoft Graph
    ↓
Scheduled ingestion
```

This keeps the first implementation focused on the architectural problem.

---

## 12. I Decided What Needs to Be Proven Before Full SDD Implementation

Before full implementation, the risky assumptions should be validated with small technical spikes.

### Spike 1 — IIS hosting
Can a minimal ASP.NET Core application run on the existing IIS environment?

### Spike 2 — Authentication
Can Windows Authentication identify the current corporate user?

### Spike 3 — Persistence
Can SQLite be created/read/written from the approved application folder?

If not, is an existing SQL Server available?

### Spike 4 — Ingestion parity
Can representative Excel data be ingested into the new normalized model while matching approved legacy behavior?

### Spike 5 — Dynamic retrieval
Can one initiative be queried using server-side:

- filters,
- search,
- paging,
- details?

Once these assumptions are proven, the architecture is no longer just theoretical.

---

## 13. Only Then Does Spec Kit Become the Main Implementation Workflow

Once discovery and architecture feasibility are sufficiently understood, Spec Kit is used to drive implementation.

```text
/speckit.constitution
        ↓
/speckit.specify
        ↓
/speckit.clarify
        ↓
/speckit.plan
        ↓
/speckit.tasks
        ↓
/speckit.analyze
        ↓
/speckit.implement
        ↓
/speckit.converge
```

---

## 14. What Goes into the Constitution

The constitution should capture principles that apply throughout the project.

### Zero Incremental Cost
The first implementation should reuse approved existing infrastructure wherever feasible.

### Dynamic Data Access
The browser should not receive the full historical dataset when only a subset is required.

### Server-Side Security
UI filtering must never be treated as authorization.

### Business Rule Preservation
Approved calculations and normalization rules must remain equivalent unless explicitly changed.

### Ingestion Compatibility
Approved source interpretation must be preserved through automated compatibility tests.

### Database Portability
SQLite is the initial provider, not a permanent architectural dependency.

### Testable Business Logic
Business rules must have automated tests using approved reference data.

### Operational Safety
Invalid imports must not replace the last-known-good dataset.

---

## 15. What Goes into `/speckit.specify`

The specification should describe **WHAT and WHY**.

For example:

> Build an internal Production Rollout Dashboard that allows authorized users to monitor GenAI SDLC rollout progress across six initiatives.

The specification should describe outcomes such as:

- users can view overview and initiative progress,
- users can filter/search rollout records,
- users can inspect team-level details,
- approved calculations remain correct,
- the browser does not need to load the complete dataset,
- unauthorized data is not returned,
- the first implementation introduces no additional infrastructure/service cost.

It should **not begin with**:

```text
Use React
Use ASP.NET Core
Use SQLite
```

Those are technical planning decisions.

---

## 16. What Goes into `/speckit.clarify`

Clarify unresolved business questions before planning.

Examples:

- What exact access model is needed?
- Should a missing sheet reject the entire import?
- What should happen with unknown status values?
- What is the approved `pre = 0` metric behavior?
- Which existing behaviors are mandatory for V1 parity?
- What historical retention is required?

---

## 17. What Goes into `/speckit.plan`

Once architecture feasibility is validated, the plan can introduce:

```text
Frontend        React
Backend         ASP.NET Core
Hosting         Existing IIS
ORM             EF Core
Persistence     SQLite V1
Future DB       SQL Server/PostgreSQL
Input           Manual Excel initially
Ingestion       Dedicated ingestion component
Authentication  Windows Authentication if approved
SharePoint      Deferred
```

The plan also defines:

- solution structure,
- data model,
- API boundaries,
- validation,
- error handling,
- deployment,
- testing,
- logging,
- rollback.

---

## 18. How I Would Explain the Copilot Documents

If asked:

> “Did you just use Copilot to generate these documents?”

My answer:

> No. I first identified what I needed to understand from the current system — business capabilities, calculations, Excel interpretation, edge cases, UX behavior and technical limitations. I then used Copilot against the existing codebase to accelerate reverse engineering and capture that behavior in a structured form. Those generated documents are reference material, not the new specification. I reviewed the captured behavior, separated business invariants from legacy implementation choices, and used that analysis to form the modernization approach.

---

## 19. How I Would Answer: “What Did You Do First?”

> The first thing I did was not coding and not Spec Kit. I first framed the dashboard as a business problem and separated the problem from the current implementation. I identified what users rely on, what calculations and behavior must remain correct, what constraints we have, what parts of the current solution are technical debt, and what assumptions still need validation. Then I used the existing code and dashboard as evidence to capture the current behavior. Only after that did I form the greenfield modernization direction.

---

## 20. How I Would Answer: “How Is This SDD Mindset?”

> For me, the SDD mindset here means that I am not letting the existing code or the implementation technology define the solution. I first establish the required behavior, business invariants, constraints and acceptance conditions. I make unknowns explicit and validate risky architecture assumptions. The specification then describes the desired system behavior, and the plan describes how we implement it. The code comes after those decisions rather than becoming the source of truth.

---

## 21. How I Would Answer: “Why Not Start Directly with `/speckit.specify`?”

> Because I did not want to feed assumptions or legacy implementation details into the specification. Before specifying the new product, I needed to understand the real business behavior, identify what should and should not be preserved, understand the zero-cost constraint, and identify architecture questions that required validation. Once that baseline is clear, `/speckit.specify` can focus on the actual required outcomes rather than accidentally describing the old solution.

---

## 22. How I Would Answer: “What Is the Main Modernization?”

> The main modernization is not Python-to-.NET or HTML-to-React. It is changing the architecture from a generated static dashboard that carries all data in the browser to a proper server-side application where information is stored in a structured data store and retrieved dynamically based on user need.

```text
OLD

Excel
  ↓
JSON
  ↓
Huge static HTML
  ↓
Everything loaded


NEW

Excel
  ↓
Ingestion
  ↓
Database
  ↓
Server-side API
  ↓
Only requested data
```

---

## 23. How I Would Answer: “Why Greenfield?”

> The existing dashboard is useful as a source of business knowledge, but its architecture is exactly what we are trying to move away from. A greenfield design lets us preserve the required behavior without carrying forward the static HTML model, positional Excel dependencies, duplicated calculation logic and client-side-only data handling.

---

## 24. How I Would Answer: “What Is Your Current Status?”

> I have completed the current-state understanding and captured the major business rules, extraction behavior, dashboard calculations, UX flow, pain points and constraints. I have also defined the proposed greenfield modernization direction and identified the architecture assumptions that need technical validation. The next step is to prove those assumptions through small feasibility spikes before starting the bounded Spec Kit implementation.

---

## 25. How I Would Answer: “What Will This Project Finally Produce?”

The project has two outcomes.

### Outcome 1 — Modernized RLLOT application
A dynamic, maintainable rollout dashboard.

### Outcome 2 — Practical SDD reference
A real greenfield example showing:

```text
Problem framing
        ↓
Discovery
        ↓
Business invariants
        ↓
Constraints
        ↓
Architecture validation
        ↓
Constitution
        ↓
Specification
        ↓
Clarification
        ↓
Plan
        ↓
Tasks
        ↓
Implementation
        ↓
Validation
        ↓
Lessons learned / guideline
```

This can then inform how SDD / Spec Kit should be applied to future brownfield work.

---

## 26. Short Version for a Live Discussion

> I did not start by running Spec Kit commands or selecting a technology. I first treated the rollout dashboard as a business problem and separated what must be preserved from how the existing system happens to implement it. I captured the current business capabilities, calculations, Excel interpretation, UX behavior, pain points and zero-cost constraint. I used Copilot on the existing project only to accelerate reverse engineering of the current behavior; those documents are reference inputs, not the specification. From that analysis I identified the greenfield direction: dynamic server-side retrieval, separate ingestion and relational persistence, while keeping the first implementation zero-cost and using the existing IIS environment. I also identified the assumptions that still need validation, such as IIS hosting, authentication and persistence. Once those are proven, I will use Spec Kit properly — constitution for principles, specify for what/why, clarify ambiguities, plan the validated architecture, then tasks and implementation.

---

## 27. Core Message to Remember

> **I did not start from the code and generate a specification. I started from the problem, used the current code as evidence, extracted the business contract, separated that from legacy implementation, validated constraints and unknowns, and only then prepared the system for SDD.**

---

**End of document**
