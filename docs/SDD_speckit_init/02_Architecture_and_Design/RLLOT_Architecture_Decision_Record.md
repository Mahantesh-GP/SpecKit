# ADR-001: RLLOT Production Rollout Dashboard Modernization Architecture

**Status:** Proposed baseline - pending feasibility confirmation  
**Date:** 2026-08-12  
**Decision scope:** Greenfield modernization of the RLLOT Production Rollout Dashboard  
**Primary constraint:** Zero additional infrastructure/service cost for the first implementation

---

## 1. Context

The current RLLOT solution consolidates multiple DR-maintained Excel workbooks, processes them through Python scripts, writes a consolidated JSON file, and generates one self-contained static HTML dashboard hosted internally through IIS.

The dashboard currently provides valuable business behavior including six initiative views, three-phase progress, customer approval tracking, Pre/Post GenAI metrics, overdue detection, cross-DR consolidation, summary KPIs, filtering/search, and row-level detail.

The current technical model is not sustainable as the long-term application architecture because:

- the complete dataset is embedded in the generated HTML;
- browser filtering is UI-only and cannot act as an authorization boundary;
- dashboard refresh requires rerunning the full generation/deployment pipeline;
- data growth and richer detail directly increase generated page size;
- the extractor has fragile schema assumptions such as fixed column positions;
- there is no normalized application persistence layer;
- automated source-to-dashboard correctness testing is limited;
- future user-specific access cannot be enforced safely in the current client-side model.

The modernization objective is therefore to preserve approved business behavior while replacing the current implementation with a clean dynamic application.

---

## 2. Decision Drivers

The selected architecture must optimize for:

1. **Zero incremental cost** for V1.
2. Reuse of approved existing internal IIS/Windows infrastructure where feasible.
3. Preservation of approved current business calculations and behavior.
4. Dynamic/on-demand data retrieval rather than embedding the entire dataset in the browser.
5. Server-side security capability.
6. Low operational complexity.
7. Maintainability and testability.
8. Database portability for future SQL Server/PostgreSQL adoption.
9. Incremental migration with low cutover risk.
10. Compatibility with SDD / GitHub Spec Kit implementation.

---

## 3. Decision

### 3.1 Modernization model

The RLLOT dashboard will be rebuilt as a **greenfield dynamic application**.

The current dashboard will be used only as:

- a functional reference;
- a source of approved business rules;
- a source of representative test data and expected results;
- a UX reference for useful interactions.

The following existing technical mechanisms are **not** part of the target architecture:

- generated monolithic dashboard HTML;
- embedded complete dataset in the DOM/JavaScript;
- `rllot_data.json` as production application persistence;
- client-side-only data filtering as a security model;
- coupling data refresh to full HTML regeneration.

### 3.2 Target application architecture

```text
Controlled Excel input (V1)
        |
        v
RLLOT Ingestion Component
  - validate workbook/schema
  - normalize fields
  - apply approved rules
  - persist import atomically
        |
        v
Relational Persistence Boundary
        |
        +--> SQLite (V1 baseline)
        +--> SQL Server (future/existing enterprise option)
        +--> PostgreSQL (future option)
        |
        v
ASP.NET Core Application / API
  - filtered/paged queries
  - summary aggregation
  - details on demand
  - server-side authorization
        |
        v
React Dashboard
        |
        v
Existing Internal IIS / Windows Server
```

### 3.3 Hosting

The preferred V1 host is the existing internal IIS/Windows server.

This decision is conditional on a feasibility check confirming that the server is approved/capable of hosting the selected ASP.NET Core runtime and required IIS hosting components.

If the existing server cannot host ASP.NET Core under current policy, an alternative already-approved internal host may be used only if it preserves the zero-incremental-cost objective.

### 3.4 Frontend

React is selected as the baseline UI technology for the modernized dashboard.

The UI will preserve the business-relevant current interactions but will fetch summaries, filtered rows, pages, and row details from the server rather than loading the complete dataset upfront.

### 3.5 Backend

ASP.NET Core is selected as the baseline server-side application/API technology because it fits the existing Windows/IIS environment and supports clear separation of application, data-access, security, and business-rule responsibilities.

### 3.6 Persistence

The architecture will use a **database-agnostic relational persistence design**.

**V1 implementation choice: SQLite**, subject to infrastructure/security acceptance.

SQLite is selected for V1 because:

- it requires no separate database server;
- it introduces no additional infrastructure/service cost;
- the discovery workload is small and read-heavy;
- writes are expected mainly during controlled imports;
- it reduces operational setup for the first implementation.

SQLite is an implementation choice, **not an architectural dependency**.

The application will access persistence through EF Core/data-access abstractions and avoid unnecessary provider-specific SQL/functions. Future movement to SQL Server or PostgreSQL should not require redesigning the domain model, API contract, ingestion behavior, or UI, although provider-specific migration/data-type adjustments may still be required.

If an existing enterprise SQL Server database is already available at zero incremental cost or mandated by policy, it may replace SQLite without changing the target application boundaries.

### 3.7 Data source and ingestion

For V1, the Excel workbooks remain the source/business input. The new relational database is a normalized application query store.

The ingestion component is a separate architectural boundary from the UI/API and will:

- read workbook(s);
- validate required sheets and headers;
- prefer header/name-based mapping over fragile fixed-index assumptions;
- normalize statuses, dates, approval values, and metrics;
- apply approved calculation/input rules;
- persist data using a transaction/staging approach;
- record import metadata/result;
- retain the last-known-good dataset when an import fails.

The existing Python extractor is a source of proven parsing/business knowledge. The modernization reuses **validated logic and test cases**, not the existing end-to-end HTML-generation pipeline.

The final production ingestion implementation language remains open until the ingestion spike. A .NET implementation is preferred if parity and operational simplicity are achieved; retaining/refactoring Python remains acceptable if it is materially lower risk.

### 3.8 Authentication and authorization

The requirement is **no additional interactive login for normal viewers**, not necessarily anonymous access.

If all internal viewers see the same data, the application may operate without differentiated authorization. If user/group-specific access is required, seamless corporate authentication such as IIS Windows Authentication will be used where approved, and access filtering will be applied server-side before records are returned.

Client-side hiding/filtering will not be treated as authorization.

### 3.9 SharePoint automation

Automated SharePoint retrieval using Microsoft Graph/SharePoint APIs is **deferred**.

V1 will accept a controlled/manual Excel file handoff so identity, tenant permissions, and Graph approval do not block the primary modernization objective.

SharePoint automation will be implemented only after the dynamic application, ingestion, persistence, and parity goals are achieved.

### 3.10 Data correctness

Automated business-rule parity testing is mandatory.

A representative reference dataset will validate at least:

- status normalization;
- customer approval normalization;
- Phase 1/2/3 completion;
- overall completion;
- overdue logic;
- Productivity/Quality/Coverage behavior;
- lower-is-better metrics;
- excluded metrics;
- missing/invalid data;
- Pre=0 behavior once clarified.

---

## 4. Consequences

### Positive

- Removes the monolithic static HTML growth problem.
- Keeps V1 incremental cost at or near zero using existing infrastructure and SQLite.
- Enables server-side filtering, paging, aggregation, and security.
- Separates Excel ingestion from dashboard presentation.
- Improves testability of business rules.
- Allows current and modern dashboards to run in parallel.
- Keeps a practical migration path to SQL Server/PostgreSQL.
- Makes future SharePoint automation an independent feature rather than a blocker.

### Trade-offs / Costs

- Introduces a server-side runtime that must be supported operationally on IIS.
- Requires database lifecycle/backup handling even when SQLite is used.
- Database portability is a design goal, not a guarantee of zero-effort provider switching.
- Existing business logic must be carefully revalidated during extraction-rule migration.
- Identity-aware authorization requires infrastructure/security input.

---

## 5. Alternatives Considered

### A. Continue improving the generated static HTML solution

**Rejected as target architecture.**

It minimizes short-term change but preserves the core problems: full-data browser delivery, HTML growth, full-pipeline regeneration, limited security boundaries, and tight coupling between data and presentation.

### B. Keep Python + JSON + add a small API

**Not selected as the final baseline.**

This could provide a fast transitional POC, but JSON remains a weak application persistence model and keeping the new architecture close to the current implementation is not the modernization objective.

Python remains acceptable for the ingestion boundary if it proves lower-risk, but the application architecture will still use relational persistence and a proper server-side application layer.

### C. ASP.NET Core + SQLite + React on existing IIS

**Selected V1 baseline**, subject to feasibility checks.

This best balances zero incremental cost, maintainability, dynamic data access, team fit, portability, and reuse of existing IIS.

### D. ASP.NET Core + existing SQL Server

**Preferred enterprise alternative if already available at zero incremental cost or required by policy.**

No need to provision it solely for V1 if SQLite is accepted and sufficient.

### E. New paid Azure/cloud services

**Rejected for V1** because they conflict with the zero-additional-cost constraint.

### F. Automate SharePoint first

**Deferred.**

Graph/tenant/security approval adds dependencies that are not required to prove the primary modernization goal.

---

## 6. Open Validations / Decision Gates

The baseline architecture is accepted only after the following checks:

| Gate | Required Confirmation | Fallback |
|---|---|---|
| IIS/ASP.NET Core | Target/representative IIS can host the supported ASP.NET Core runtime | Another already-approved zero-cost internal host |
| Authentication | Windows Authentication is available if identity-based access is required | Approved corporate identity alternative or same-data-for-all access model |
| SQLite | Infrastructure/security permits an application-owned DB file and backup approach | Existing zero-cost SQL Server if available |
| Ingestion | Representative Excel can be validated/normalized and persisted with parity | Adjust parser/implementation before proceeding |
| Query model | Filtered/paged API returns only required data with acceptable response | Tune schema/index/API design |
| Business rules | Reference dataset results match approved existing behavior | Resolve rule ambiguity before implementation |

---

## 7. Implementation Sequence

### Stage 0 - Corrected discovery baseline

Capture business capabilities, business rules, pain points, constraints, data volume, access expectations, deferred scope, and open validations.

### Stage 1 - Feasibility spikes

1. Minimal ASP.NET Core application on IIS.
2. Windows Authentication if required.
3. SQLite read/write/backup/permissions.
4. One representative Excel -> normalized relational import.
5. One filtered/paged API -> simple React result view.

### Stage 2 - Architecture confirmation

Review spike results and update this ADR from Proposed to Accepted, or record a superseding ADR if a key assumption fails.

### Stage 3 - SDD / GitHub Spec Kit

Use the production-quality flow:

```text
/speckit.constitution
    -> /speckit.specify
    -> /speckit.clarify
    -> /speckit.plan
    -> /speckit.checklist
    -> /speckit.tasks
    -> /speckit.analyze
    -> /speckit.implement
    -> /speckit.converge
```

#### Constitution principles

- Zero incremental infrastructure/service cost for V1.
- Dynamic data retrieval; no full historical dataset by default.
- Server-side security; UI filtering is not authorization.
- Preserve approved business rules unless change is explicit.
- Separate ingestion, persistence, application logic, and UI.
- Automated business-rule parity tests are mandatory.
- Invalid imports cannot replace last-known-good data.
- Avoid database-provider-specific coupling where unnecessary.

#### `/speckit.specify`

Focus on **what and why**:

- users and outcomes;
- six-initiative tracking;
- progress/metric/detail behavior;
- filtering/search/paging expectations;
- data correctness and access outcomes;
- zero-cost constraint;
- acceptance criteria.

Do not put React/ASP.NET Core/SQLite in the feature specification unless they are stated as externally imposed constraints.

#### `/speckit.clarify`

Resolve:

- Pre=0 metric rule;
- exact access/data-scope behavior;
- required parity for first release;
- error/invalid workbook behavior;
- any ambiguous metric/phase calculations.

#### `/speckit.plan`

Introduce the validated technical architecture:

- React frontend;
- ASP.NET Core application/API;
- existing IIS hosting;
- EF Core persistence abstraction;
- SQLite V1 / provider portability;
- ingestion architecture;
- authentication/authorization approach;
- deployment, logging, backup, and rollback.

#### `/speckit.tasks`, `/speckit.analyze`, `/speckit.implement`, `/speckit.converge`

Break the feature into dependency-ordered work, verify artifact consistency, implement incrementally, and check the completed code against the spec/plan/tasks.

---

## 8. Deferred Follow-On Features

After the main modernization goal is achieved:

1. Automated SharePoint file retrieval through approved Microsoft Graph/SharePoint access.
2. Scheduled ingestion.
3. Broader row/data-level authorization if not in MVP.
4. Saved views/filter profiles.
5. ADO/work-item drill-through.
6. Additional operational monitoring/auditing as required.

---

## 9. Cutover Strategy

The existing dashboard remains available while the modernized application is validated.

Both versions will use equivalent source data and compare:

- team/row counts;
- phase percentages;
- overall completion;
- overdue items;
- metric summaries;
- filters/search;
- detail values.

Cutover occurs only after required parity and operational acceptance are achieved. The legacy static pipeline is retained briefly as rollback and then retired.

---

## 10. References

- Current RLLOT implementation documentation (`RLLOT_Dashboard_SDD.md`).
- Updated `RLLOT_Modernization_Discovery.md`.
- GitHub Spec Kit documentation: https://github.com/github/spec-kit
- ASP.NET Core on IIS: https://learn.microsoft.com/en-us/aspnet/core/host-and-deploy/iis/
- Windows Authentication in ASP.NET Core: https://learn.microsoft.com/en-us/aspnet/core/security/authentication/windowsauth
- EF Core SQLite provider: https://learn.microsoft.com/en-us/ef/core/providers/sqlite/
- EF Core SQLite limitations: https://learn.microsoft.com/en-us/ef/core/providers/sqlite/limitations
- SQLite appropriate uses: https://www.sqlite.org/whentouse.html
