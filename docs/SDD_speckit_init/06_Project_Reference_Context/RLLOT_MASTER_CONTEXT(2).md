# RLLOT Modernization – Master Context & Decision Reference

**Purpose**  
This is the single working reference for the RLLOT Production Rollout Dashboard modernization.  
Use it in future conversations to avoid reconstructing decisions from chat history.

---

## 1. Project Goal

Modernize the existing RLLOT Production Rollout Dashboard from a static HTML reporting solution into a maintainable, dynamic, server-side application.

The modernization must:

- Preserve approved business behavior and calculations.
- Remove the continuously growing self-contained HTML model.
- Retrieve only required data on demand.
- Use server-side filtering and authorization.
- Reuse existing internal infrastructure wherever practical.
- Keep the first implementation at **zero additional infrastructure/service cost**.
- Be implemented using **SDD / GitHub Spec Kit** after architecture feasibility is validated.

---

## 2. Current Production Flow

DR/PM users update Excel trackers in SharePoint daily.

```text
SharePoint Excel trackers
        ↓
Developer manually downloads Excel files
        ↓
Python project
        ↓
extract_data.py
        ↓
rllot_data.json
        ↓
build_rllot_dashboard.py
        ↓
RLLOT_Dashboard.html
        ↓
Existing internal IIS / Windows server
        ↓
Users open dashboard
```

Current characteristics:

- Multiple per-DR Excel workbooks.
- Six initiative sheets.
- Python/openpyxl extraction and normalization.
- Consolidation into `rllot_data.json`.
- One fully self-contained HTML output.
- CSS, JS, SVG and row data embedded into HTML.
- Browser-side filtering.
- All row data loaded into the browser.
- Full regeneration/redeployment for each refresh.

---

## 3. Current Business Capabilities to Preserve

The existing dashboard is the **functional/business-rule reference**, not the technical foundation.

Must preserve unless explicitly changed:

1. Overview across initiatives.
2. Six initiative views:
   - Backend Unit Testing
   - Generate Test Cases
   - Generate PostMan Scripts
   - Frontend Unit Testing
   - Integration Testing
   - Vulnerability Discovery
3. Customer Approval status.
4. Phase 1, 2 and 3 status and ETA.
5. Phase completion calculations.
6. Overall progress calculation.
7. Overdue detection.
8. Productivity metrics.
9. Quality metrics.
10. Coverage metrics.
11. Pre/Post GenAI metric handling.
12. Search across Team / Project / Org / PM / DR.
13. Status filtering.
14. Overdue filtering.
15. Team-level detail view.
16. Metrics explanation.
17. Remarks.
18. Cross-DR consolidation.

---

## 4. Current Technical Design That Must NOT Be Preserved

These are legacy implementation characteristics, not modernization requirements:

- Single generated `RLLOT_Dashboard.html`.
- Entire dataset embedded in HTML / JavaScript / DOM.
- Client-side filtering as the primary data-access model.
- `rllot_data.json` as production persistence.
- Regenerating the whole dashboard for every refresh.
- `build_rllot_dashboard.py` as the new presentation architecture.
- Self-contained static HTML as a requirement.
- No-runtime-server design.
- Hard dependency on current Python structure.

The modernization is a **greenfield technical design**.

---

## 5. Modernization Principle

> Reuse validated business rules and source-data interpretation, not the legacy architecture.

The existing dashboard/code should answer:

- What data exists?
- What calculations are approved?
- What edge cases exist?
- What filters and drill-downs users need?
- What results are expected?

---

## 6. Main Pain Points

- Static HTML grows as history/details grow.
- Developer manually downloads files and reruns pipeline.
- No normalized application-level query store.
- Client-side filtering is not security.
- Legacy extractor uses fixed positions for columns 0–16.
- Data-correctness testing is incomplete.
- Known `pre = 0` multi-sprint gap.
- Dead code/hard-coded operational paths exist.
- Every refresh recreates the complete dashboard.

---

## 7. Data Volume Context

Indicative discovery estimates:

```text
Current:
DRs                  ~5
Teams                ~100–150
Concurrent users     <20

Potential 12-month:
DRs                  ~8–12
Teams                ~200–300
Concurrent users     <50
```

This is not a big-data workload. The modernization is mainly about maintainability, security, operations and architecture quality.

---

## 8. Agreed Target Architecture

```text
                    SharePoint Excel
                          |
                          | V1: manual/controlled file handoff
                          v
                 RLLOT Ingestion Component
                 - read Excel
                 - validate workbook/schema
                 - normalize data
                 - extract metrics
                 - persist atomically
                          |
                          v
                 Relational Persistence
                 - SQLite for V1
                 - database-agnostic design
                 - SQL Server/PostgreSQL later if needed
                          |
                          v
                 ASP.NET Core Application/API
                 - server-side filtering
                 - paging
                 - search
                 - calculations
                 - authorization
                          |
                          v
                     React Dashboard
                          |
                          v
                Existing Internal IIS
                          |
                          v
                 Authenticated Internal Users
```

---

## 9. Hosting Decision

V1 direction: reuse existing internal Windows/IIS infrastructure.

Validate:

- ASP.NET Core hosting/runtime availability.
- Deployment permissions.
- App/data/log folders.
- Windows Authentication.
- SQLite file storage policy.

No new Azure App Service, Function, Azure SQL, VM or other paid service is part of V1 baseline.

---

## 10. Frontend Decision

Preferred frontend: **React**.

The existing dashboard is only a UX/functionality reference.

New UI should support:

- Overview.
- Six initiative views.
- Search.
- Filters.
- Pagination.
- On-demand team details.
- Metrics and phase summaries.

The browser must not receive the full historical dataset by default.

---

## 11. Backend Decision

Use **ASP.NET Core** for the server-side application/API.

Responsibilities:

- Authentication integration.
- Authorization.
- Server-side filters/search.
- Pagination.
- Aggregations.
- Metric calculations where appropriate.
- Team detail retrieval.
- Database access.
- Operational logging.

---

## 12. Persistence Decision

### Architectural decision

Use a **database-agnostic relational persistence design**.

### V1 implementation

Use **SQLite** because it gives:

- Zero additional infrastructure cost.
- No separate DB server.
- Good fit for read-heavy internal dashboard.
- Controlled periodic writes.
- Small/moderate data volume.
- EF Core support.

### Future options

- SQL Server.
- PostgreSQL.

SQLite is a **V1 deployment choice, not a permanent architectural dependency**.

Use:

- EF Core entities.
- `DbContext`.
- LINQ.
- Normal PK/FK relationships.
- Portable data types where practical.

Avoid:

- SQLite-specific SQL in business logic.
- SQLite-specific functions/triggers unless unavoidable.
- Direct `.db` file manipulation from domain logic.

Possible V1 location:

```text
C:\Apps\RLLOT\Data\rllot.db
```

Store outside public `wwwroot`.

---

## 13. Excel as Business Input vs Database as Application Store

Initial model:

```text
Excel / SharePoint
      ↓
Authoritative business input
      ↓
Ingestion
      ↓
Normalized application database
      ↓
Dashboard query store
```

Do not claim Excel is replaced as the source of truth unless the business approves that change.

---

## 14. Ingestion – Definition

### Ingestion logic

How Excel becomes validated application data:

```text
Excel
  ↓
Read
  ↓
Validate
  ↓
Normalize
  ↓
Transform
  ↓
Persist
```

### Ingestion job

How/when ingestion runs:

- Manual execution.
- Windows Task Scheduler.
- .NET Worker / Windows Service.
- Future automated SharePoint retrieval.

For V1, scheduling is not required.

---

## 15. V1 Ingestion Scope

```text
Manual / controlled Excel file
          ↓
Ingestion component
          ↓
Database
```

The modernization must not be blocked by SharePoint automation.

---

## 16. SharePoint / Microsoft Graph Decision

**Deferred for V1.**

Automatic SharePoint retrieval will be considered only after the core modernization is working.

Later flow:

```text
SharePoint
   ↓
Microsoft Graph
   ↓
Scheduled retrieval
   ↓
Ingestion
   ↓
Database
```

Later requirements:

- Approved tenant app registration / identity.
- Least-privilege Graph/SharePoint permission.
- Security/admin approval.

---

## 17. Detailed Legacy Extraction Reference

Detailed compatibility reference:

**`SPECKIT_extract_data.md`**

Use it as the detailed reference for current `extract_data.py` behavior.

The new ingestion must preserve **approved source interpretation**, not necessarily old code or JSON output.

---

## 18. Current Extractor – File Discovery

- Input folder: `input/`.
- Scans `*.xlsx`.
- Files sorted alphabetically.
- Fatal if input folder missing/not directory/no Excel files.
- Unreadable individual workbook: warn and continue.

DR filename pattern:

```text
GenAI SDLC Initiatives Roll Out Tracker - <DR Full Name>.xlsx
```

Regex:

```text
-\s*(.+?)\.xlsx$
```

Fallback: filename without extension.

---

## 19. Current Extractor – Six Target Sheets

```text
BackEnd_Unit_Testing
Gen_Test_Case
Gen_Scripts_4_PostMan
Integration_Testing
Vuln_Discovery
FrontEnd_Unit_Testing
```

Missing sheet currently produces a warning and processing continues.

Whether V1 should continue or reject the workbook should be clarified.

---

## 20. Current Extractor – Header Detection

Current behavior:

- Scan first 11 rows.
- First row with column A == `#` becomes header.
- Row above is metric category row.
- No header found -> current extractor silently returns no rows for that sheet.

Modernization improvement:

- Schema failures should be explicit and logged.
- Malformed workbooks should not silently produce incomplete data.

---

## 21. Current Extractor – Fixed Column Mapping

Legacy fixed positions:

```text
0   #
1   Org
2   Project
3   Unit
4   Team
5   PM
6   Members
7   unused
8   Customer Approval
9   Prereq Date
10  Phase 1 Status
11  Phase 1 ETA
12  Phase 2 Status
13  Phase 2 ETA
14  Phase 3 Status
15  Phase 3 ETA
16  Remarks
17+ Metrics
```

### Agreed modernization improvement

Do **not** preserve fixed positional mapping.

Use header-based mapping wherever practical:

```text
"Phase 1 Status" → P1
"Phase 2 Status" → P2
"Phase 3 Status" → P3
"Remarks"        → Remarks
```

Validate required headers before import.

---

## 22. Metric Discovery Compatibility Rules

Metrics use:

```text
<Metric Name> (Pre GenAI)
<Metric Name> (Post GenAI)
```

Current semantics:

- Base name derived by removing suffix.
- Pre drives metric creation.
- Pre without Post -> Post = null.
- Post without Pre -> ignored.
- Category comes from category row.
- Category inheritance is sticky left-to-right.
- Default category: Productivity.
- Explanation header accepts:
  - `Metrics Explanation`
  - `Metrics Explaination`
- One explanation is shared across all metrics in one row.

These should become automated compatibility tests.

---

## 23. Row Iteration Compatibility Rules

Current behavior:

- Blank first cell increments empty streak.
- Five consecutive blanks stop processing.
- Numeric row number -> process.
- `1.0` -> accepted as `1`.
- Non-numeric labels such as `Total` -> skipped.

Preserve unless intentionally changed.

---

## 24. Status Normalization Rules

### Phase status

```text
None / blank     → Not Filled
in progress      → In Progress
in-progress      → In Progress
completed        → Completed
open             → Open
tbd              → TBD
na / n/a         → NA
hold / on hold   → On Hold
datetime         → Open
unknown text     → preserve as-is
```

### Customer Approval

```text
approved          → Approved
rejected          → Rejected
not yet provided  → Not Filled
yet to request    → Not Filled
hold / on hold    → On Hold
completed         → Completed
open              → Open
tbd               → TBD
in progress       → In Progress
```

Unknown non-blank values are preserved.

---

## 25. Date Handling

Current behavior:

```text
datetime → YYYY-MM-DD
anything else → null
```

Legacy workbook load:

```python
openpyxl.load_workbook(
    path,
    data_only=True,
    read_only=True
)
```

The new implementation must explicitly define handling for formula-backed cells and cached values.

---

## 26. Metric Value Handling

Current behavior:

- `int` / `float` -> float.
- `None`, string, datetime, error -> null.
- Values are not scaled.
- Example: `2.43` means `2.43%`.

Dead legacy percentage-normalization helpers do not need to be copied.

---

## 27. Edge Cases to Preserve or Review

| Case | Current Behavior |
|---|---|
| Short row | Missing fields default |
| Phase 3 is datetime | `"Open"` |
| Row number `1.0` | accepted as `1` |
| Row number `"Total"` | skipped |
| Pre exists, Post missing | Post = null |
| Post exists, Pre missing | metric ignored |
| Same initiative across DR files | rows concatenated |
| Explanation missing | empty string |

These should become compatibility tests.

---

## 28. New Ingestion Compatibility Principle

Replacement ingestion must produce **semantically equivalent normalized data**, not necessarily identical JSON.

Legacy:

```text
Excel
  ↓
Python extractor
  ↓
rllot_data.json
```

Modernized:

```text
Excel
  ↓
Ingestion component
  ↓
Normalized domain objects
  ↓
Persistence abstraction
  ↓
SQLite / SQL Server / PostgreSQL
```

Regression approach:

```text
Representative Excel
       │
       ├── Legacy extractor → JSON
       │
       └── New ingestion → normalized records
                          ↓
                    parity comparison
```

---

## 29. Recommended Ingestion Component Boundaries

```text
ExcelReader
    ↓
WorkbookSchemaValidator
    ↓
HeaderMapper
    ↓
RowExtractor
    ↓
StatusNormalizer
    ↓
MetricExtractor
    ↓
RolloutRecord / Metric models
    ↓
Persistence abstraction
```

Suggested tests:

```text
DRFilenameParsingTests
HeaderDetectionTests
HeaderMappingTests
WorkbookValidationTests
StatusNormalizerTests
CustomerApprovalNormalizerTests
MetricPairingTests
MetricCategoryTests
DateNormalizationTests
RowTerminationTests
IngestionParityTests
DatabaseIntegrationTests
```

---

## 30. Security Decision

Legacy client-side filtering is **not a security boundary**.

New application must:

- Authenticate users where required.
- Apply authorization server-side.
- Filter restricted data before returning it.
- Never rely on JavaScript hiding rows.

Preferred internal authentication direction:

- IIS Windows Authentication / AD.

Business wording:

> Users should not require an additional interactive login where seamless corporate authentication is available.

This is not the same as anonymous access.

---

## 31. Correct SDD / Spec Kit Sequence

Do not start Spec Kit by simply describing the old implementation.

### Pre-SDD

```text
Current-state understanding
        ↓
Business-rule baseline
        ↓
Modernization discovery
        ↓
Infrastructure validation
        ↓
Technical feasibility spikes
        ↓
Architecture decision
```

### Spec Kit

```text
/speckit.constitution
        ↓
/speckit.specify
        ↓
/speckit.clarify
        ↓
/speckit.plan
        ↓
/speckit.checklist (when useful)
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

## 32. Constitution – Agreed Principles

### Zero Incremental Cost
First production baseline should reuse approved existing infrastructure wherever feasible.

### Dynamic Data Access
Browser must not receive the full historical dataset when only a subset is required.

### Server-Side Security
UI filtering must never be treated as authorization.

### Business Rule Preservation
Approved calculations and normalization rules remain equivalent unless explicitly changed.

### Ingestion Compatibility
Modern ingestion must preserve approved source-data interpretation through automated compatibility tests while allowing legacy weaknesses to be corrected.

### Separation of Concerns
Ingestion, persistence, application/domain logic and UI are independent.

### Database Portability
SQLite is the V1 provider, not a permanent architectural dependency.

### Testable Business Logic
Normalization, calculations and ingestion behavior require automated tests using approved reference data.

### Operational Safety
Invalid imports must not replace last-known-good production data.

### Incremental Delivery
Modernization should be delivered in bounded, independently verifiable features.

---

## 33. What Belongs in `/speckit.specify`

Focus on **WHAT** and **WHY**.

Examples:

- Authorized users can monitor rollout progress.
- Users can view overview and six initiatives.
- Users can search/filter records.
- Users can view team details.
- Browser does not need the entire dataset.
- Approved calculations are preserved.
- Invalid Excel files are rejected with understandable errors.
- Only authorized data is returned.
- Zero-additional-cost is a project constraint.

Do not put React, ASP.NET Core, SQLite or EF Core into feature requirements unless explicitly mandated.

---

## 34. What Belongs in `/speckit.plan`

Current expected technical inputs:

```text
Frontend        React
Backend         ASP.NET Core
Hosting         Existing IIS
Authentication  Windows Authentication if approved
ORM             EF Core
Persistence     SQLite V1
Future DB       SQL Server / PostgreSQL
Source          Manual Excel initially
Ingestion       Dedicated ingestion component
SharePoint      Deferred automation
```

Also include:

- schema.
- API design.
- paging/filtering strategy.
- validation.
- deployment.
- logging.
- backup/rollback.
- migration.
- test strategy.

---

## 35. Proposed Feature Decomposition

### 001 – Application Foundation
- App shell.
- IIS-hosted backend.
- React frontend.
- Authenticated entry point.
- Initiative navigation.

### 002 – Rollout Data Ingestion
- Manual Excel input.
- Workbook validation.
- Header mapping.
- Normalization.
- Metric extraction.
- Relational persistence.
- Import history.
- Compatibility tests.

### 003 – Rollout Data Exploration
- Initiative records.
- Server-side search.
- Filters.
- Paging.
- Team details.

### 004 – Rollout Metrics & Progress
- Phase completion.
- Overall completion.
- Overdue.
- Productivity.
- Quality.
- Coverage.
- Known edge cases.

### 005 – Authentication & Authorization
- Windows Authentication.
- User/group scope.
- Server-side filtering.

### 006 – SharePoint Automation
**Deferred until core modernization succeeds.**

### 007 – Production Readiness
- Logging.
- Health checks.
- Backup.
- Rollback.
- Operational runbook.
- Performance validation.

---

## 36. V1 Scope

Included:

```text
Manual Excel input
      ↓
Ingestion
      ↓
Relational database
      ↓
ASP.NET Core API
      ↓
React dashboard
      ↓
Existing IIS
```

Capabilities:

- Overview.
- One complete initiative vertical slice first.
- Server-side search/filter/paging.
- Team details.
- Business-rule parity.
- Then extend to all six initiatives.

Not required for first vertical slice:

- Graph automation.
- Scheduled SharePoint download.
- Saved filters/views.
- ADO drill-through.
- Advanced analytics.
- Additional paid infrastructure.

---

## 37. Technical Feasibility Spikes

### Spike 1 – IIS
Can a minimal ASP.NET Core app run on target/representative IIS?

### Spike 2 – Authentication
Can Windows Authentication identify the current corporate user?

### Spike 3 – Persistence
Can the app create/read/update SQLite in an approved server folder?

If SQLite is rejected, evaluate existing SQL Server.

### Spike 4 – Ingestion
Can representative Excel be parsed into the normalized model with parity against the current extractor?

### Spike 5 – Dynamic query
Can API return only filtered/paged records?

Example:

```text
GET /api/rollouts
?initiative=BackEnd_Unit_Testing
&status=In%20Progress
&page=1
&pageSize=20
```

### Spike 6 – SharePoint
Deferred / optional.

---

## 38. Open Decisions / Pending Validation

1. Is ASP.NET Core permitted on the existing IIS server?
2. Which supported .NET version is available?
3. Is Windows Authentication enabled/approved?
4. Is SQLite permitted on the internal application server?
5. Is an existing SQL Server available at zero incremental cost?
6. Does corporate policy require a managed DB?
7. Should production ingestion remain Python or be ported to .NET?
8. Exact access model:
   - all internal users?
   - DR based?
   - manager based?
   - project/team based?
9. What should happen on malformed workbook?
10. Missing target sheet: reject whole import or skip sheet?
11. Approved rule for `pre = 0` / multi-sprint gap?
12. Acceptable refresh delay?
13. Historical retention period?

---

## 39. Deferred Items

Do not let these block the main modernization:

- Microsoft Graph SharePoint automation.
- Scheduled SharePoint retrieval.
- Power Automate.
- Azure services.
- Saved views/profiles.
- ADO drill-through.
- Advanced analytics.
- Multi-server deployment.
- DB migration unless required.

---

## 40. Migration Strategy

Keep current and new dashboards in parallel.

```text
Same Excel input
      │
      ├── Legacy pipeline → current HTML
      │
      └── New ingestion → database → new API/UI
```

Compare:

- team counts.
- status counts.
- phase percentages.
- overall percentages.
- overdue counts.
- metrics.
- detail values.
- filters.
- normalized statuses.

Do not cut over until required parity is accepted.

---

## 41. Architecture Decision Status

### Proposed baseline

```text
Greenfield modernization
+
Existing IIS / Windows server
+
React
+
ASP.NET Core
+
EF Core
+
Database-agnostic relational persistence
+
SQLite for V1
+
Manual Excel input initially
+
Dedicated ingestion
+
Server-side filtering/paging
+
Windows Authentication if approved
+
SharePoint Graph automation deferred
```

Status: **Proposed pending feasibility confirmation**.

---

## 42. Immediate Next Steps

1. Use this file as master context.
2. Keep `SPECKIT_extract_data.md` as detailed legacy extraction reference.
3. Validate IIS + ASP.NET Core.
4. Validate Windows Authentication.
5. Validate SQLite policy/storage.
6. Build ingestion parity spike.
7. Build filtered API spike.
8. Finalize ADR after spikes.
9. Initialize Spec Kit.
10. Create constitution.
11. Start first bounded feature specification.

---

## 43. Recommended Project Documentation

```text
RLLOT_MASTER_CONTEXT.md
    Single consolidated modernization context and decisions.

SPECKIT_extract_data.md
    Detailed legacy Excel extraction compatibility reference.

RLLOT_Architecture_Decision_Record.md
    Formal architectural decisions, alternatives and consequences.

RLLOT_Dashboard_SDD.md
    Legacy/current implementation behavior reference.

specs/
    GitHub Spec Kit feature specifications.
```

---

## 44. Short Context for Future AI Sessions

> RLLOT is an internal GenAI SDLC rollout dashboard currently generated as one static HTML file from per-DR Excel trackers using Python. The modernization is a greenfield rebuild using the current solution only as a business-rule and UX reference. The proposed target is React + ASP.NET Core on existing IIS, dynamic server-side retrieval, relational persistence designed to be database-agnostic, SQLite for the zero-cost V1, EF Core for portability, and a separate ingestion component that preserves validated Excel interpretation while correcting legacy fragility such as positional columns. Manual Excel input is acceptable initially; Microsoft Graph/SharePoint automation is deferred until the core modernization is complete. Windows Authentication is preferred if approved. Before SDD, validate IIS, authentication, SQLite and ingestion parity. Then use GitHub Spec Kit with constitution → specify → clarify → plan → tasks → analyze → implement → converge. Detailed legacy extraction behavior remains in `SPECKIT_extract_data.md`.

---

## 45. Current Dashboard UX / Screen Baseline

The current production dashboard screenshots captured on 2026-08-12 are part of the modernization reference.

They should be treated as the **UX and information-architecture baseline**, not as a pixel-perfect design that must be copied.

### 45.1 Global dashboard structure

The current dashboard contains:

- Title/header: **GenAI SDLC Prod Roll Out**.
- Last refreshed date.
- Global filters:
  - Organization.
  - DR.
  - PM / SM.
  - Reset Global.
- Initiative navigation:
  - Overview.
  - Backend.
  - Test Gen.
  - PostMan.
  - Frontend.
  - Integration.
  - Vulnerability.

Global filters apply across initiatives.

### 45.2 Overview screen

The Overview currently shows high-level KPI cards such as:

- Overall Completion %.
- Total Team-Initiatives.
- Fully Completed (all 3 phases).
- Phase 1 Completed.
- Phase 2 Completed.
- Phase 3 Completed.
- Teams with Due Phase(s).

It also contains an **Activity Summary** section with one card per initiative.

Each initiative card shows some combination of:

- Initiative completion %.
- Team count.
- Phase 1 / Phase 2 / Phase 3 completed counts.
- Progress/status visualization.
- Productivity metric.
- Quality metric.
- Coverage metric.
- "Click to explore" navigation.

The Overview also contains descriptive cards explaining what each of the six tracked activities means.

### 45.3 Initiative detail screen

A selected initiative currently includes:

- Initiative title.
- Completion donut / completion %.
- Team count.
- Phase 1 Status filter.
- Phase 2 Status filter.
- Phase 3 Status filter.
- Overdue filter.
- Free-text Search.
- Reset action.

The initiative screen contains:

#### Phase Progress

Three phase summary cards:

- Phase 1.
- Phase 2.
- Phase 3.

Each can show:

- Completion percentage.
- Status distribution.
- Completed count.
- In Progress count.
- Open count.
- NA / Not Filled where applicable.
- Overdue indicator.

#### Metrics Summary

Summary metrics are grouped under:

- Productivity.
- Quality.
- Coverage.

The exact metric displayed varies by initiative according to the approved business rules.

#### Team Details Table

The current table includes columns such as:

- #.
- Org.
- Project.
- Team.
- PM.
- DR.
- Approval.
- Phase 1.
- Phase 2.
- Phase 3.
- Productivity.
- Quality.
- Coverage.
- Details.

### 45.4 Team details modal / drawer

Selecting **Details** opens a team-level detail view.

The current detail experience includes:

- Team/project title.
- Organization/project context.
- Metrics Explanation.
- Metric cards grouped by category.
- Before value.
- After value.
- Change / improvement where calculable.
- Remarks.
- Empty/no-data behavior where metric values or explanation are unavailable.

---

## 46. UX Modernization Decision

### Preserve

Preserve the current **information hierarchy and core user workflow**:

```text
Global filters
      ↓
Overview
      ↓
Select initiative
      ↓
Initiative summary
      ↓
Filter / search teams
      ↓
Team table
      ↓
Open details
      ↓
Metrics + explanation + remarks
```

Preserve the presence and meaning of:

- Overview.
- Six initiative navigation.
- Global Organization / DR / PM-SM filters.
- Initiative-specific phase filters.
- Overdue filter.
- Search.
- KPI cards.
- Phase progress.
- Metrics summary.
- Team-level table.
- Details modal/drawer.

### Do not require

Do not require:

- Pixel-for-pixel reproduction.
- Exact current colors/icons.
- Exact card sizes or spacing.
- Existing HTML/CSS/JavaScript implementation.
- Entire data set to remain in the browser.

Small UX improvements are allowed as long as approved business behavior remains clear.

Examples:

- Better responsive layout.
- Clear loading states.
- Empty states.
- Error states.
- Server-side paging.
- Better accessibility.
- Clearer filter state.
- Better table responsiveness.

The first modernization objective is **architecture and functional parity**, not a visual redesign project.

---

## 47. Dynamic Data-Loading Model Derived from Current Screens

The new application should support the same visible workflow using server-side retrieval.

### Overview

Conceptual request:

```text
GET /api/dashboard/overview
```

Returns only overview aggregates/KPIs.

### Initiative summary

Conceptual request:

```text
GET /api/initiatives/{initiative}/summary
```

Returns phase progress and metrics summary for the selected initiative and active global filters.

### Initiative team table

Conceptual request:

```text
GET /api/initiatives/{initiative}/teams
    ?organization=...
    &dr=...
    &pm=...
    &p1=...
    &p2=...
    &p3=...
    &overdue=...
    &search=...
    &page=1
    &pageSize=50
```

Returns only filtered/paged records.

### Team details

Conceptual request:

```text
GET /api/teams/{id}/details
```

Returns detail data only when the user opens a row, including:

- Metrics explanation.
- Metric before/after values.
- Remarks.

These endpoint names are **illustrative architecture concepts**, not final API contracts. Final API design belongs in `/speckit.plan`.

---

## 48. Recommended First Vertical Slice Based on Current UX

The first end-to-end modernization slice should prove one initiative completely before reproducing all six.

Recommended first slice:

```text
Backend Unit Testing
        ↓
Phase summary
        ↓
Metrics summary
        ↓
Server-side filtered/paged team table
        ↓
Team details modal
```

End-to-end technical path:

```text
Representative Excel
        ↓
New ingestion
        ↓
SQLite V1
        ↓
ASP.NET Core API
        ↓
React UI
        ↓
Existing IIS
```

Once parity is proven for one initiative, repeat the pattern for the remaining five initiatives.

---

## 49. Three Primary Reference Sources

Future modernization and SDD discussions should use these references together:

```text
RLLOT_Dashboard_SDD.md
    → current business behavior and legacy dashboard implementation

SPECKIT_extract_data.md
    → detailed Excel extraction and normalization compatibility behavior

Current dashboard screenshots
    → UX, screen layout, navigation and information hierarchy baseline
```

The screenshots are a visual reference only; the written business/extraction rules remain authoritative for calculations and edge cases.

---

## 50. Updated Short Context for Future AI Sessions

> RLLOT is an internal GenAI SDLC rollout dashboard currently generated as one static HTML file from per-DR Excel trackers using Python. The modernization is a greenfield rebuild using the current solution only as a business-rule, ingestion-compatibility and UX reference. The current UX baseline includes global Organization/DR/PM-SM filters, an Overview with overall/phase KPI cards and six initiative activity cards, initiative detail views with phase/overdue/search filters, phase progress, productivity/quality/coverage summaries, a team details table, and a team-level details modal with metric explanation, before/after values and remarks. Preserve this information architecture and user workflow, but do not require pixel-perfect copying. The proposed target is React + ASP.NET Core on existing IIS, dynamic server-side retrieval, database-agnostic relational persistence, SQLite for the zero-cost V1, EF Core for portability, and a separate ingestion component that preserves validated Excel interpretation while correcting legacy fragility such as positional columns. Manual Excel input is acceptable initially; Microsoft Graph/SharePoint automation is deferred. Windows Authentication is preferred if approved. Before SDD, validate IIS, authentication, SQLite and ingestion parity. Then use GitHub Spec Kit with constitution → specify → clarify → plan → tasks → analyze → implement → converge. Detailed legacy extraction behavior remains in `SPECKIT_extract_data.md`.

## 51. Dashboard Builder / Calculation Compatibility Reference

Detailed current dashboard-builder reference:

**`SPECKIT_build_rllot_dashboard.md`**

This file captures the current `build_rllot_dashboard.py` behavior: JSON loading, initiative summary construction, completion formulas, overdue rules, metric aggregation, overview calculations, filtering behavior, detail drawer behavior, rendering/security conventions, and legacy Python/JavaScript coupling.

Use it as the detailed reference when rebuilding dashboard calculations and visible behavior.

### Modernization rule

Preserve **approved calculation semantics and user-visible behavior**. Do not preserve implementation mechanics that only exist because the current solution generates one static HTML file.

---

## 52. Initiative Constants and Status Semantics

### Six initiative display mappings

```text
BackEnd_Unit_Testing    → Backend Unit Testing
Gen_Test_Case           → Generate Test Cases
Gen_Scripts_4_PostMan   → Generate PostMan Scripts
FrontEnd_Unit_Testing   → Frontend Unit Testing
Integration_Testing     → Integration Testing
Vuln_Discovery          → Vulnerability Discovery
```

Display order matters for the current tab/activity-card sequence and should remain the UX baseline unless deliberately redesigned.

### Canonical status order

Current status display order:

```text
Completed
Approved
In Progress
Open
TBD
On Hold
Rejected
NA
Not Filled
```

This is mainly a presentation convention for legends, stacked bars and dropdown ordering.

### Active statuses for completion calculations

```text
Completed
In Progress
Open
On Hold
Not Filled
```

Excluded from the active denominator:

```text
NA
TBD
Rejected
```

This rule is a business/calculation compatibility requirement.

---

## 53. Phase Completion Formula

For any initiative and phase:

```text
active = number of rows where phase status ∈ ACTIVE_STATUSES
completed = number of rows where phase status == Completed

phase_completion = completed / active × 100
```

Rules:

- If `active == 0`, result is `0` in the current implementation.
- Result is rounded to 1 decimal place.
- `NA`, `TBD`, and `Rejected` do not affect numerator or denominator.

This calculation must be preserved unless the business explicitly changes it.

---

## 54. Per-Initiative Overall Completion Formula

The current initiative-level overall percentage is **not a flat total across all phase rows**.

It is:

```text
p1_pct = phase_completion(rows, p1)
p2_pct = phase_completion(rows, p2)
p3_pct = phase_completion(rows, p3)

overall_pct = (p1_pct + p2_pct + p3_pct) / 3
```

Rounded to 1 decimal.

Important consequence:

> Every phase has equal weight in an initiative's overall percentage, even when the number of active rows differs by phase.

This distinction must be preserved in parity tests.

---

## 55. Overview Overall KPI Formula – Different from Initiative Overall

The main Overview **Overall Completion** KPI uses a different formula from the per-initiative percentage.

Current formula:

```text
agg_denom =
    p1_active across all initiatives
  + p2_active across all initiatives
  + p3_active across all initiatives

agg_completed =
    p1_completed across all initiatives
  + p2_completed across all initiatives
  + p3_completed across all initiatives

overview_overall_pct = agg_completed / agg_denom × 100
```

This is a **flat weighted fraction** across all active phase records.

Therefore:

```text
Per-initiative overall
    = average of 3 phase percentages

Overview overall KPI
    = total completed phase records / total active phase records
```

These two calculations can legitimately produce different percentages.

This is one of the most important parity rules for the modernization.

---

## 56. Overview KPI Definitions

The current Overview contains seven primary KPI boxes:

1. Overall completion percentage.
2. Total Team-Initiatives.
3. Fully Completed (all three phases completed).
4. Phase 1 Completed count / active.
5. Phase 2 Completed count / active.
6. Phase 3 Completed count / active.
7. Teams with Due Phase(s) / overdue count.

The Overview also contains one activity card per initiative showing:

- Initiative overall percentage.
- Team count.
- P1/P2/P3 completion counts.
- Progress visualization.
- Productivity summary.
- Quality summary.
- Coverage summary.

For Overview activity-card Quality, the legacy implementation intentionally limits the concise Quality display to `Acceptance Rate %`.

---

## 57. Overdue Calculation

Current overdue logic:

```text
is_overdue(status, eta)
    = true only when:
      status is Open OR In Progress
      AND ETA exists
      AND today > ETA
```

Parse errors return false.

A row is considered overdue if **any** of these are overdue:

- Phase 1 + P1 ETA.
- Phase 2 + P2 ETA.
- Phase 3 + P3 ETA.
- Customer Approval + prerequisite date.

Important business behavior:

- Completed items are not overdue merely because the date is in the past.
- NA and TBD are not overdue.
- Overdue is based on current date at runtime in the modern application.

---

## 58. Metric Category Normalization

Current category normalization:

```text
blank / None    → Other
covergae        → Coverage
coverage        → Coverage
productivity    → Productivity
quality         → Quality
other text      → trimmed original text
```

The typo `covergae` exists in real/current data and must remain recognized during ingestion/calculation compatibility.

---

## 59. Lower-Is-Better Metric Rule

A metric is currently treated as **lower is better** when its name contains any of these case-insensitive tokens:

```text
time
effort
hrs
hour
escape
maintenance
flaky
```

For lower-is-better metrics:

```text
improvement = (pre - post) / pre × 100
```

A lower Post value therefore creates a positive improvement percentage.

For normal/higher-is-better metrics:

```text
improvement = (post - pre) / pre × 100
```

This name-based classification is a legacy business heuristic. Preserve it for parity first; later it may be replaced by explicit metric metadata if approved.

---

## 60. Metric Improvement Formula and Known Gap

Current generic improvement logic:

```text
if pre is null OR post is null OR pre == 0:
    improvement = No Data / null

else if lower-is-better:
    improvement = (pre - post) / pre × 100

else:
    improvement = (post - pre) / pre × 100
```

Known gap:

- `pre == 0` currently returns no improvement value.
- A separate business specification defines sprint-to-sprint fallback behavior for this case.
- The modernization must not silently invent the final rule.
- The correct `pre == 0` behavior must be clarified/approved before claiming complete parity.

---

## 61. Post-Average Metrics vs Improvement Metrics

Some metrics do not display improvement delta. They display the **average Post GenAI value**.

Current post-average rule returns true for:

- All metrics categorized as `Coverage`.
- `Acceptance Rate %`.
- `Critical/High Found Pre-Check-In`.
- `Critical/High Found Pre Check-In`.
- Any metric whose name contains `cov` or `coverage`.

For those metrics:

```text
avg_post = sum(post values for valid pre/post pairs) / reported_count
```

For normal improvement metrics, the builder aggregates totals first:

```text
total_pre  = sum(pre across valid pairs)
total_post = sum(post across valid pairs)

improvement = improvement(total_pre, total_post)
```

Important:

> The initiative summary improvement is calculated from aggregate totals, not as a simple average of each team's individual improvement percentages.

This must be preserved in parity tests.

---

## 62. Metric Reporting Count

A metric contributes to the initiative aggregate only when **both Pre and Post are non-null**.

```text
reported_count = number of rows with both Pre and Post
```

Current metric summary shows:

```text
{reported_count} / {total_rows} reported
```

This reporting count should remain available in the modern dashboard because it communicates data completeness.

---

## 63. Metrics Excluded from Summary Cards

Current raw-count metrics excluded from initiative Metrics Summary cards:

```text
Number of test cases generated
Number of test scripts generated
Number of unit tests generated
Number of integration scenarios generated
Number of APIs covered
```

Reason: these are count metrics and the current design does not treat their averaged/improvement values as meaningful summary KPIs.

Preserve this behavior initially unless business owners redefine summary metrics.

---

## 64. Productivity Metric Used in Team Table by Initiative

The Productivity table column uses one designated metric per initiative rather than averaging every Productivity metric.

Current mapping:

```text
Backend Unit Testing
    Unit test creation effort Hrs

Generate Test Cases
    Total Effort Hrs required to create test cases

Generate PostMan Scripts
    Time required to Generate API Collections

Frontend Unit Testing
    Total Effort Hrs required to create UI unit tests

Integration Testing
    Total time required to create integration tests

Vulnerability Discovery
    Time taken to run vulnerability
```

Matching is case-insensitive in the current builder.

This should initially be represented as configuration/metadata in the modern application rather than hardcoded across UI logic where possible.

---

## 65. Plain-Number Metric Display

The current builder treats these metric names as plain numbers instead of percentages:

```text
Critical/High Found Pre-Check-In
Critical/High Found Pre Check-In
```

This distinction should be captured in metric metadata or calculation/display rules in the modernized design.

---

## 66. Team-Level Metric Display Rules

The current team table contains three metric columns:

```text
Productivity
Quality
Coverage
```

Current behavior:

- Productivity uses the initiative-specific designated productivity metric.
- Quality may use Post average or improvement depending on metric classification.
- Coverage uses Post value/average semantics.
- Improvement values show signed percentage (`+x.x%` / `-x.x%`).
- Post-average values display unsigned percentage.
- Missing data displays a dash / no-data state.

These are user-visible compatibility behaviors.

---

## 67. Current Filter Semantics

### Global filters

Current global filters:

- Organization.
- DR.
- PM / SM.

They apply across all six initiatives and therefore affect Overview aggregates as well as initiative-specific rows.

### Initiative-level filters

Current initiative filters include:

- Phase 1 status.
- Phase 2 status.
- Phase 3 status.
- Customer Approval/status where present in legacy filter logic.
- Overdue yes/no.
- Free-text search.

Search currently targets concatenated values from:

```text
Team
Project
Org
PM
DR
```

### Modernization implication

The new application should preserve filter semantics, but filtering and aggregate recalculation should move to server-side query/application logic instead of DOM row hiding.

---

## 68. Filtered-State Recalculation Requirement

In the legacy dashboard, after any filter changes, JavaScript recalculates:

1. Phase completion cards.
2. Initiative overall percentage.
3. Metrics Summary.
4. Overview KPIs and initiative activity cards.

Therefore the real user requirement is:

> All displayed aggregates must reflect the **currently active filter scope**, not only the original full dataset.

In the modernized design, this should be implemented by server-side queries/aggregate endpoints or a coherent backend query model.

Do not reproduce the legacy duplication where business formulas exist separately in Python and JavaScript.

---

## 69. Critical Legacy Duplication to Eliminate

The current static implementation mirrors several business rules in both Python and JavaScript so filter changes can recalculate values in the browser.

Examples include:

- metric exclusion list.
- post-average classification.
- lower-is-better classification.
- completion calculations.
- metric aggregation.
- donut update logic.

This creates divergence risk.

### Modernization decision

Business calculation rules should have **one authoritative server-side implementation**.

React should consume calculated values/data from the API rather than independently reimplementing business formulas.

This is a major architectural improvement and should become a constitution/design principle.

---

## 70. Team Detail / Drawer Behavior

The current details view contains:

1. Team title / row number.
2. Project and organization context.
3. Metrics Explanation.
4. Metrics grouped in category order:
   - Productivity.
   - Quality.
   - Coverage.
   - Other.
5. Before value.
6. After value.
7. Change indicator where calculable.
8. Remarks when present.

Multiple metrics may carry the same explanation text. The current builder deduplicates repeated explanation strings while preserving first-seen order.

Modernization should preserve the user-visible outcome, but the new API can return structured data rather than pre-rendered HTML.

---

## 71. Sorting Behavior

The legacy team table allows clicking column headers to toggle ascending/descending sorting.

Legacy sorting attempts numeric comparison where possible, otherwise alphabetic comparison.

The modern dashboard should preserve useful column sorting, preferably using server-side sorting when the table is paged.

---

## 72. Status Colors and Visual Conventions

Current status colors provide a UX reference:

```text
Completed    green
Approved     blue/cyan
In Progress  amber
Open         blue
TBD          purple
On Hold      orange
Rejected     red
NA           grey
Not Filled   pale yellow / brown text
```

Exact hex values and rendering implementation do not need to be preserved unless the design team requires pixel parity.

The semantic distinction and consistent status styling should remain.

---

## 73. Donut/SVG Formula – Legacy Rendering Reference Only

The current static builder calculates donut arcs as:

```text
r = (size - stroke) / 2
circumference = 2 × π × r
dash = circumference × percentage / 100
```

Current legacy sizes include separate radii for phase, initiative and overview rings.

This is **not a business rule**.

The React implementation may use any appropriate chart/component library or CSS/SVG implementation, provided displayed percentages are correct and the zero-cost/security constraints are respected.

Do not make legacy SVG element IDs/circumference constants architectural requirements.

---

## 74. Legacy Embedded Data Structures – Do Not Preserve

The current static page embeds structures such as:

```text
window._rowData
window._allActivityData
row.dataset.metrics
row.dataset.p1 / p2 / p3
```

These exist to make a self-contained HTML file behave dynamically without a backend.

They are **legacy implementation details**.

The new application should replace them with API contracts and React state/query handling.

---

## 75. Security Lessons from Current Builder

The legacy builder correctly attempts to HTML-escape Excel-originated strings and uses a restrictive Content Security Policy because it embeds all content into one generated HTML file.

Modernization requirements derived from this:

- Treat all Excel-originated strings as untrusted input.
- Validate and safely encode output.
- Avoid raw HTML injection from imported data.
- React rendering should use normal encoded text by default.
- Do not use `dangerouslySetInnerHTML` for Excel-originated content unless a separately validated/sanitized requirement exists.
- API authorization must occur before restricted data is returned.
- Security must not depend on client-side hiding/filtering.

Legacy requirements such as inline-script CSP exceptions are not requirements for the new architecture.

---

## 76. Dashboard Calculation Parity Test Matrix

The modernization should create automated reference tests covering at least:

### Completion

- Phase with Completed + Open + In Progress.
- Phase containing NA/TBD/Rejected exclusions.
- Phase with zero active rows.
- Initiative overall as average of three phase percentages.
- Overview overall as flat weighted active/completed fraction.
- Demonstrate that initiative overall and Overview overall can differ.

### Overdue

- Open with past ETA → overdue.
- In Progress with past ETA → overdue.
- Open with future ETA → not overdue.
- Completed with past ETA → not overdue.
- NA/TBD with past ETA → not overdue.
- Customer Approval overdue behavior.
- Any-one-phase-overdue makes row overdue.

### Metrics

- Higher-is-better improvement.
- Lower-is-better improvement.
- Pre null.
- Post null.
- Pre zero known-gap scenario.
- Coverage post-average.
- Acceptance Rate post-average.
- Critical/High plain-number behavior.
- Aggregate totals vs average-of-row-improvements distinction.
- Reported count requires both Pre and Post.
- Excluded summary metric.
- Initiative-designated Productivity metric.
- `covergae` category typo compatibility.

### Filters

- Global Org filter updates all initiative/overview aggregates.
- DR filter.
- PM filter.
- P1/P2/P3 filter.
- Overdue filter.
- Search over Team/Project/Org/PM/DR.
- Combined filters.
- Filtered aggregates equal aggregates calculated directly from the filtered source rows.

### Details

- Explanation present.
- Duplicate explanation deduplication.
- Missing explanation.
- Metrics with values.
- Metrics without values.
- Remarks present/absent.

---

## 77. Updated Modernization Design Principle – Single Calculation Authority

Add this project principle:

> **Single Calculation Authority:** Completion, overdue, metric classification, aggregation, reporting-count and filtered-summary rules must be implemented once in the server-side application/domain layer and validated with automated tests. The frontend must not maintain an independent copy of business formulas.

This directly removes a major fragility in the legacy static architecture, where initial calculations are done in Python and filtered-state calculations are duplicated in JavaScript.

---

## 78. Updated Reference Source Set

Use these sources together in future RLLOT work:

```text
RLLOT_Dashboard_SDD.md
    → overall legacy system behavior and documented business/UI rules

SPECKIT_extract_data.md
    → exact Excel extraction, normalization and ingestion compatibility behavior

SPECKIT_build_rllot_dashboard.md
    → exact dashboard calculation, aggregation, filter and rendering compatibility behavior

Current dashboard screenshots
    → visual UX / navigation / information hierarchy baseline

RLLOT_Modernization_Discovery_Corrected.md
    → modernization problem, constraints, scope and open questions

RLLOT_Architecture_Decision_Record.md
    → formal proposed architectural decisions

RLLOT_MASTER_CONTEXT.md
    → consolidated context and working decisions for future sessions
```

---

## 79. Updated Short Context for Future AI Sessions – Including Dashboard Formulas

> RLLOT is an internal GenAI SDLC rollout dashboard currently produced from per-DR Excel trackers through Python extraction → JSON → one self-contained HTML dashboard. The modernization is a greenfield rebuild that preserves approved business semantics and UX while replacing the static all-data-in-browser architecture. Detailed ingestion semantics are captured in `SPECKIT_extract_data.md`; detailed dashboard formulas and filter behavior are captured in `SPECKIT_build_rllot_dashboard.md`. Key calculation rules: active completion statuses are Completed/In Progress/Open/On Hold/Not Filled; NA/TBD/Rejected are excluded. Phase completion = Completed / Active. Per-initiative overall = average of P1, P2 and P3 completion percentages. The Overview Overall KPI is different: total completed phase records / total active phase records across all initiatives. Overdue applies only to Open/In Progress with a past date, including customer approval prerequisite date. Metric improvement is `(post-pre)/pre` for higher-is-better and `(pre-post)/pre` for lower-is-better names containing time/effort/hrs/hour/escape/maintenance/flaky. Coverage, Acceptance Rate and selected Critical/High metrics use Post averages instead of improvement. Metric aggregates use summed Pre/Post values across valid pairs, not the average of individual row improvement percentages. The known `pre=0` rule remains unresolved and must be clarified. Filtered KPI/metric results must reflect the active filter scope. The modern architecture should implement business calculations once on the ASP.NET Core server and not duplicate formulas in React. Proposed target remains existing IIS + React + ASP.NET Core + EF Core + database-agnostic relational persistence with SQLite for V1; manual Excel input initially; SharePoint/Graph automation deferred; SDD/Spec Kit begins after feasibility validation.

**End of master context**
