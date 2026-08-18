# RLLOT Modernization Discovery

**Status:** Updated discovery baseline  
**Purpose:** Capture the current business behavior, constraints, technical characteristics, pain points, open questions, and feasibility checks that must be understood before architecture implementation and SDD/Spec Kit execution.

---

## 1. Current Business Capabilities

The following capabilities represent what the rollout dashboard does today and what must continue to work in the modernized solution unless the business explicitly changes the behavior.

| Capability | Detail |
|---|---|
| 6-initiative tracking | Backend Unit Testing, Generate Test Cases, Generate PostMan Scripts, Frontend Unit Testing, Integration Testing, Vulnerability Discovery |
| 3-phase progress per team | Phase 1, Phase 2, and Phase 3 status/ETA per team row |
| Customer Approval tracking | Separate approval status and prerequisite due date per row |
| Pre/Post GenAI metrics | Productivity, Quality, and Coverage metrics with before/after values per team |
| Overdue detection | Open/In Progress items with a past ETA are flagged |
| Cross-DR consolidation | Multiple DR-owned Excel workbooks are consolidated into a single dashboard view |
| Initiative-level KPIs | Overall completion %, phase completion %, and metric summaries per initiative |
| Filterable team table | Filter by phase status, overdue flag, and free-text fields such as team/project/org/PM/DR |
| Row-level details | Metrics before/after, explanation, and remarks for each team |

### 1.1 Current technical characteristic that is **not** a business capability

The current system produces a self-contained static HTML file with embedded data and no runtime server dependency. This is a property of the present implementation and **must not be treated as a modernization requirement**. The new solution is explicitly intended to replace this model.

---

## 2. Must-Preserve Business Rules

These rules are encoded in the current Python implementation and must be preserved unless the business approves a change.

| Rule | Current Source | Required Behavior |
|---|---|---|
| Status normalization | `extract_data.py -> norm()` | Normalize variants such as `in-progress` / `In Progress`; datetime in Phase 3 status is treated as `Open` |
| Customer Approval normalization | `extract_data.py -> norm_ca()` | Extends status normalization with mappings such as Approved, Rejected, and not-yet-provided variants |
| Completion denominator | `overall_progress()` | Only Completed, In Progress, Open, On Hold, and Not Filled count as active; NA, TBD, and Rejected are excluded |
| Overall completion formula | `overall_progress()` | Overall completion is the average of the three phase completion percentages, not a flat row count |
| Post-average vs improvement display | `is_post_avg_metric()` | Coverage and selected quality metrics show Post GenAI average; other applicable metrics show improvement delta |
| Lower-is-better metrics | `is_lower_better()` | Time/effort/hour/escape/maintenance/flaky-type metrics improve when values decrease |
| Excluded count metrics | `EXCLUDED_SUMMARY_METRICS` | Raw count metrics that are not meaningful averages remain excluded from summary cards |
| DR name derivation | `dr_from_filename()` | DR identity is derived from the workbook filename pattern unless the source model is intentionally redesigned |
| Overdue detection | `is_overdue()` | Only Open/In Progress with a past ETA are overdue; Completed/NA/TBD are not overdue |

### 2.1 Known rule gap requiring clarification

The existing implementation returns no improvement when the Pre GenAI value is zero, while the business reference describes a multi-sprint proxy-baseline behavior. This must be clarified and explicitly specified before the modernized calculation is finalized.

---

## 3. Current Technical Architecture

```text
SharePoint / DR Excel workbooks
        |
        v
Manual download / input folder
        |
        v
extract_data.py
  - parse workbook/sheets
  - normalize statuses/dates
  - extract metrics
        |
        v
rllot_data.json
        |
        v
build_rllot_dashboard.py
  - calculate summaries
  - render CSS/JS/SVG/HTML
  - embed all row data
        |
        v
RLLOT_Dashboard.html
        |
        v
Internal IIS / browser
```

The current implementation is read-only and optimized around static-file generation rather than a queryable application runtime.

---

## 4. Pain Points

| ID | Pain Point | Impact |
|---|---|---|
| P-01 | Manual refresh: collect Excel files, run scripts, copy HTML to server | High - operational effort, error-prone, dependent on specific operators |
| P-02 | Multiple distributed source workbooks and no normalized application-level data store | High - difficult validation, version drift, inconsistent structures can cause data loss |
| P-03 | Extractor depends on hardcoded column indexes | High - inserting/reordering a column can silently break extraction |
| P-04 | Pre=0 multi-sprint metric behavior not implemented | Medium - affected teams display no data instead of intended improvement behavior |
| P-05 | No automated data-correctness comparison against source/approved results | Medium - dashboard values are largely validated through manual spot checks |
| P-06 | Hardcoded month-specific reports path | Low - operational failure when month changes |
| P-07 | Dead/backup extractor code remains in repository | Low - ambiguity about which implementation is active |
| P-08 | No direct drill-through to source work items | Low - usability limitation; defer unless required for MVP |
| P-09 | Dashboard changes require rerunning the full generation pipeline | Medium - even small corrections require regeneration/redeployment |
| P-10 | Entire dataset is embedded in the generated HTML | High - file size grows with history/details; browser receives data it may not need |
| P-11 | Client-side filters are not a security boundary | High if differentiated access is introduced - restricted rows must be filtered before response |

---

## 5. Expected Data Growth

The discovery estimates below are planning inputs and must be validated with the dashboard owner before they are treated as committed capacity figures.

| Dimension | Current Estimate | 12-Month Projection |
|---|---:|---:|
| DR workbooks | 5 | 8-12 |
| Teams / total rows | ~100-150 | 200-300 |
| Initiatives | 6 | 6 in near term; possible later expansion |
| Metrics per row | ~4 per initiative | 4-6 |
| Refresh frequency | Daily | Daily |
| Concurrent viewers | <20 | <50 |

**Observation:** this is not a big-data workload. Modernization is driven primarily by maintainability, security, operational simplicity, data correctness, and elimination of the static full-dataset HTML model rather than raw scale.

---

## 6. User and Access Requirements

| User Type | Indicative Count | Need |
|---|---:|---|
| EA / operators | 3-4 | Import/operate/deploy/monitor the application |
| Viewers (leadership, PMs) | ~20-50 | Read-only dashboard access |
| DRs / data contributors | 5-12 | Maintain the source Excel workbooks; future input model may evolve |
| External users | None | Internal intranet only |

### 6.1 Authentication clarification

The requirement should be expressed as **no additional interactive login for viewers**, not as a blanket requirement for anonymous access.

If all viewers are allowed to see the same data, anonymous intranet access may remain technically possible. If the application must enforce user/group-specific data visibility, the server must know the caller identity. In that case, seamless corporate authentication (for example IIS Windows Authentication) is preferred so users do not see a separate login screen.

---

## 7. Zero-Additional-Cost Constraint

The first modernization baseline must introduce **no new incremental platform/service cost**.

Preferred reuse:

- Existing internal Windows/IIS server, if it can host the selected runtime.
- Existing corporate identity capabilities, if already available and approved.
- Open-source/framework components such as .NET/ASP.NET Core, React, and SQLite.
- Existing enterprise database infrastructure only if already provisioned/licensed for this workload at no additional project cost.

Out of scope for the baseline unless already provisioned at zero incremental cost:

- Azure App Service, Azure Functions, Azure SQL, Cosmos DB, or other metered cloud services.
- New paid SaaS/platform licenses.
- New paid plugins/connectors.

---

## 8. Existing Infrastructure and Hosting - Items to Validate

### Confirmed

- Current IIS server can serve static HTML/CSS/JS assets.
- Current solution is accessible on the internal network.

### Unknown / requires validation

- Whether a supported ASP.NET Core runtime and IIS Hosting Bundle / ASP.NET Core Module are installed or approved.
- Whether Windows Authentication is enabled/approved for the target application.
- Whether the application can have a protected writable data directory outside the web root.
- Whether the server permits a scheduled task or Windows service/worker for future ingestion automation.
- Whether the current server has outbound connectivity/permissions needed for future SharePoint API access.

**Important correction:** availability of classic ASP.NET/.aspx on IIS does not prove ASP.NET Core hosting is available. ASP.NET Core hosting must be validated separately.

---

## 9. Persistence Options

The modernized solution should use a **database-agnostic relational persistence boundary**. The application and ingestion logic must not depend directly on SQLite-specific behavior.

| Option | Assessment | Decision Position |
|---|---|---|
| SQLite | Embedded/serverless relational database; no separate DB server; strong fit for a small, read-heavy, single-application V1 | **Selected V1 implementation baseline, subject to server-policy validation** |
| Existing SQL Server | Strong enterprise option if an instance/database is already available at no incremental cost or required by policy | Preferred alternative when available/mandated |
| PostgreSQL | Valid future relational target through an EF Core provider | Future option; not assumed to exist today |
| JSON file | Current/transitional artifact; weak query/concurrency/security characteristics | Not a production persistence target |
| SharePoint List | Different data-entry/storage model with licensing/governance implications | Out of current V1 scope |
| Azure SQL / Cosmos DB | Metered cloud services | Outside zero-cost baseline |

### 9.1 Portability requirement

V1 will use SQLite through an abstraction such as EF Core. The data model should favor portable relational concepts and avoid unnecessary SQLite-specific SQL/functions. Migration to SQL Server or PostgreSQL should be feasible without redesigning the domain, API, or ingestion behavior, while recognizing that provider-specific migrations/data-type differences may still require work.

---

## 10. Source Data and Ingestion Boundary

### 10.1 Source of record for V1

The DR Excel workbooks remain the business input/source system for V1. The new database is a **normalized application query store**, not automatically a replacement for the business source of truth.

### 10.2 Ingestion responsibility

The ingestion component will:

1. Read the approved workbook(s).
2. Discover required sheets/columns by names/headers rather than relying only on fixed column indexes.
3. Validate workbook schema and reject/log invalid structures.
4. Normalize statuses, dates, customer approval values, and metric fields.
5. Apply approved business rules.
6. Write normalized records and import metadata to the relational store using an atomic/staged import approach.
7. Preserve the last-known-good dataset when an import fails.

### 10.3 Reuse strategy

The current Python extractor is a valuable reference for rules and parsing behavior. The modernization should reuse **validated logic and test cases**, not the old HTML-generation architecture.

The production ingestion implementation language remains an architecture implementation detail to confirm during the spike. A single-stack .NET implementation is preferred if parity can be achieved cleanly; retaining Python remains acceptable if it materially reduces risk and operational complexity.

---

## 11. Deferred Capabilities

The following items are intentionally deferred until the core modernization is working:

- Automated SharePoint retrieval through Microsoft Graph / SharePoint APIs.
- Scheduled automatic ingestion from SharePoint.
- Power Automate integration.
- ADO/work-item drill-through unless explicitly reprioritized.
- Saved user filter profiles/views.
- Broader workflow/write-back capability.

For V1, a controlled/manual Excel file handoff is acceptable so external permission work does not block the primary modernization goal.

---

## 12. Automated Data-Correctness Requirement

The modernized solution must introduce automated parity testing against approved reference data.

A representative Excel dataset should have expected outputs for at least:

- Phase 1/2/3 completion percentages.
- Overall completion percentage.
- Status normalization.
- Customer approval normalization.
- Overdue classification.
- Productivity/Quality/Coverage calculations.
- Lower-is-better metric behavior.
- Excluded summary metrics.
- Known edge cases such as missing data and Pre=0.

This requirement should become a project constitution principle and be enforced throughout SDD implementation.

---

## 13. Feasibility Spikes Before SDD Implementation

The following spikes should be small and evidence-driven:

1. **IIS hosting spike** - deploy a minimal ASP.NET Core application on the target/representative server.
2. **Authentication spike** - verify seamless Windows Authentication if identity-aware access is required.
3. **Persistence spike** - create/read/update representative SQLite data on the server; confirm backup and file permissions.
4. **Ingestion spike** - import one representative initiative/workbook using header/schema validation into the relational model.
5. **Query spike** - expose a filtered/paged endpoint and confirm the browser receives only requested data.

SharePoint/Graph integration is deliberately excluded from the core spike sequence and will be evaluated after the primary dashboard architecture is proven.

---

## 14. Discovery Exit Criteria

Discovery is complete enough to enter architecture implementation when:

- Must-preserve business behavior is agreed.
- The Pre=0 metric rule is clarified or explicitly deferred.
- IIS/ASP.NET Core hosting feasibility is known.
- Authentication expectation is clarified (same data for all vs identity-based data scope).
- SQLite is accepted by infrastructure/security **or** an existing zero-cost enterprise database is selected.
- Representative ingestion and filtered API spikes succeed.
- The architecture decision record is reviewed and accepted.

---

## 15. Relationship to SDD / GitHub Spec Kit

This discovery document is **pre-SDD input**. It should not be copied wholesale into `/speckit.specify`.

After architecture feasibility is established:

```text
Discovery baseline
    -> feasibility spikes
    -> architecture decision record
    -> /speckit.constitution
    -> /speckit.specify
    -> /speckit.clarify
    -> /speckit.plan
    -> /speckit.checklist
    -> /speckit.tasks
    -> /speckit.analyze
    -> /speckit.implement
    -> /speckit.converge
```

- `/speckit.constitution`: governing principles such as zero incremental cost, server-side security, testable business rules, data correctness, separation of concerns, and operational safety.
- `/speckit.specify`: what users need and why, with behavior/acceptance criteria; avoid implementation stack.
- `/speckit.clarify`: resolve access rules, metric edge cases, and other ambiguity.
- `/speckit.plan`: introduce the validated technical architecture and stack.
- `/speckit.tasks`: derive dependency-ordered work.
- `/speckit.analyze`: check spec/plan/tasks consistency before coding.
- `/speckit.implement`: build the approved tasks.
- `/speckit.converge`: verify the implementation against the artifacts and append any remaining tasks.
