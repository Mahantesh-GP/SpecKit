# RLLOT Spec Kit Execution — Problems, Clarifications, Decisions, and Lessons

**Project:** RLLOT Production Rollout Dashboard Modernization  
**Feature:** `001-excel-import-validation` — Controlled Excel Import, Validation, and Active Dataset  
**Execution window captured:** 2026-08-17 to 2026-08-18  
**Scope:** Actual issues encountered while executing `/speckit.specify`, `/speckit.clarify`, `/speckit.plan`, specification review, and `/speckit.checklist`.

---

# 1. Why this document exists

This document records the actual execution journey rather than presenting only the final clean artifacts.

It captures:

- where the AI misunderstood the RLLOT domain,
- which requirements were initially missing or ambiguous,
- which recommendations were accepted,
- which recommendations were rejected,
- why each clarification decision was made,
- where `/speckit.plan` contradicted already-clarified requirements,
- how the specification was corrected after review,
- what additional gaps were found by later clarification/checklist gates,
- and what this taught us about applying Spec Kit to a real enterprise modernization.

This is useful evidence for the SDD pilot because the value of the process is not only the generated Markdown files. The value is also the sequence of decisions, corrections, and review gates that prevented wrong assumptions from reaching implementation.

---

# 2. Starting Feature 001 with `/speckit.specify`

## Goal

Feature 001 was intentionally selected as the first implementation feature:

> Controlled Excel Import, Validation, and Active Dataset.

The intended business outcome was:

- accept the current DR-maintained Excel workbooks,
- validate them,
- normalize the approved rollout data,
- create a complete application dataset,
- and prevent a failed/partial import from replacing the last-known-good dataset.

## Initial `/speckit.specify` result

Spec Kit created the feature folder:

```text
001-excel-import-validation
```

and generated:

```text
spec.md
checklists/requirements.md
```

The initial quality result was approximately:

```text
Content Quality           4/4 pass
Requirement Completeness  7/8 pass
Feature Readiness         4/4 pass
```

The important outcome was that the spec was **not treated as final**. It still contained unresolved behavior and therefore moved to `/speckit.clarify`.

---

# 3. First major problem — AI misunderstood the workbook model

## Problem

The first clarification exposed the most important early factual mistake.

Spec Kit interpreted the requirement as if there were:

```text
six initiative workbooks
```

and therefore reasoned about whether all six workbooks had to be present.

That was incorrect.

## Real RLLOT source model

```text
one or more DR workbooks
        ↓
each DR workbook contains six initiative sheets
```

The six required initiative sheets are:

```text
BackEnd_Unit_Testing
Gen_Test_Case
Gen_Scripts_4_PostMan
FrontEnd_Unit_Testing
Integration_Testing
Vuln_Discovery
```

## Decision

The recommendation to require "six workbooks" was rejected.

Custom answer supplied:

```text
DR workbook requires six sheets
```

## Why this answer was selected

The number of DR workbooks varies with the number of DRs.

The invariant is not:

```text
exactly six workbooks
```

The invariant is:

```text
every submitted DR workbook must contain the six required initiative sheets
```

## SDD lesson

A recommendation can be internally logical while still be factually wrong because the model misunderstood the domain.

This became the first strong example of:

> AI-generated specification content must be reviewed against actual domain evidence before it becomes project truth.

---

# 4. First `/speckit.clarify` session — questions and decisions

## Q1 — Workbook structure

### Question/problem

Does the import require all six initiative workbooks, or can fewer be submitted?

### Decision

Custom clarification:

```text
DR workbook requires six sheets
```

### Final interpretation

- one or more DR workbooks may be submitted,
- each DR workbook contains six required initiative sheets,
- the number of DR workbooks is not fixed.

### Why

This reflects the actual RLLOT input model.

---

## Q2 — Unknown status values

### Question

If a status value does not match the approved normalization mapping, should it fail the import?

### Decision

```text
B
```

Unknown status is a **non-fatal warning**.

### Final behavior

- retain the row,
- preserve the raw/unrecognized status value,
- record a warning,
- allow the batch to continue if no blocking validation fails.

### Why this answer was selected

The current extractor preserves unknown non-blank status values rather than rejecting the workbook.

Rejecting the full batch would therefore be an intentional behavior change.

The chosen behavior preserves compatibility while adding visibility through diagnostics.

---

## Q3 — Unreadable workbook

### Question

If one submitted workbook cannot be opened or parsed, should the system stop immediately or continue inspecting the remaining files?

### Decision

```text
B
```

### Final behavior

- record the unreadable-workbook error,
- skip processing that workbook,
- continue validating the remaining submitted workbooks,
- collect all errors,
- fail the batch at activation,
- never activate a partial dataset.

### Why this answer was selected

This separates:

```text
validation/diagnostic processing
```

from:

```text
dataset activation
```

Continuing the scan gives the operator a complete diagnostic report in one run while still protecting the active dataset.

---

## Q4 — Formula-backed Excel cells

### Question

How should cells containing formulas be interpreted?

### Decision

```text
A
```

Read the cached / last-computed value.

### Final behavior

- use the value stored/cached in the workbook,
- treat formula-backed values like static values during extraction,
- do not execute/recalculate arbitrary formulas as part of import.

### Why this answer was selected

The existing extractor already uses cached values (`data_only=True` behavior), so this preserves the approved source interpretation.

Additional testing concern captured:

> If no usable cached value exists, do not invent a value; surface a data-quality/validation condition.

---

## Q5 — Concurrent imports

### Question

What happens if two operators submit import batches at the same time?

### Decision

```text
A
```

Only one import batch may be active at a time.

### Final behavior

The second submission is rejected with a clear message such as:

```text
Import already in progress
```

### Why this answer was selected

For V1 imports are manual and infrequent.

Serializing the process avoids:

- active-dataset race conditions,
- last-writer-wins ambiguity,
- unnecessary queueing logic,
- more complex concurrency tests.

---

# 5. `/speckit.plan` first attempt — prompt/context problem

## Problem

The first plan interaction showed the plan request was cut off:

```text
"I am building with..."
```

Spec Kit asked for the missing technology stack.

It also found four unresolved behaviors:

```text
missing-sheet policy
duplicate row IDs
import history retention
invalid date severity
```

## Learning

This exposed two things:

1. the plan prompt itself needed to be complete,
2. unresolved requirements should not be silently decided during planning.

---

# 6. Important new domain discovery before finalizing the plan

During execution an additional real-world fact was identified:

> DR workbooks are similar but are not guaranteed to have identical physical columns. Some DRs have additional columns that others do not require.

This materially changed the ingestion design.

## Wrong approach that was rejected

Do not create:

```text
if DR == A:
    parse using layout A
elif DR == B:
    parse using layout B
...
```

Do not maintain separate parsing code per DR.

## Required approach

Use one common, header-driven pipeline:

```text
ExcelReader
    ↓
WorkbookSchemaValidator
    ↓
HeaderMapper
    ↓
RowExtractor
    ↓
Normalizers
    ↓
MetricExtractor
    ↓
Canonical Domain Model
    ↓
Persistence
```

## Key decisions

- canonical fields are identified by semantic header names,
- fixed positional indexes must not define field meaning,
- optional/extra columns may vary by DR,
- extra columns must not shift or corrupt known mappings,
- metrics are discovered dynamically,
- normalized output is common across all DRs,
- genuinely different business semantics must be surfaced for clarification rather than hidden in DR-specific code.

## Why this belonged in `spec.md`

This is observable required behavior, not merely a coding preference.

Therefore the requirement was pushed back into the specification before planning.

## SDD lesson

> Requirements discovered during planning should move back to the specification when they describe required behavior.

---

# 7. Second `/speckit.clarify` session after schema correction

After updating the specification with DR schema variation, Spec Kit re-evaluated the feature and found additional gaps.

## Q1 — Missing required initiative sheet

### Question

If a required initiative sheet is missing, fail immediately or collect all errors first?

### Decision

```text
B
```

### Final behavior

- record the missing-sheet error,
- continue processing the remaining sheets/workbooks,
- collect all diagnostics,
- fail the batch at activation,
- no partial activation.

### Why

Consistent with the unreadable-workbook decision and gives the operator a complete error report.

---

## Q2 — Supported Excel format

### Question

Which Excel formats are supported?

### Decision

```text
A
```

`.xlsx` only.

### Why

The current RLLOT input process uses `.xlsx`.

Supporting `.xls` or `.xlsm` without a known business requirement would add unnecessary parsing/security/test scope to V1.

---

## Q3 — Duplicate numeric row ID inside one sheet

### Question

If the same numeric Excel `#` appears more than once in the same initiative sheet, what should happen?

### Decision

```text
D
```

Keep all otherwise-valid rows and record a warning.

### Why

The Excel `#` is source/display metadata, not a safe application primary key.

Silently keeping only the first or last occurrence could discard valid business data.

The application must use an independent persistent identity.

---

## Q4 — Invalid date

### Question

What should happen if an optional date cell contains a value that cannot be interpreted as a valid date?

### Decision

```text
C
```

### Final behavior

- retain the row,
- normalize the invalid date to `null`,
- record a non-fatal warning,
- batch may still activate if all blocking checks pass.

### Why

Dropping the complete business row because one optional date is malformed would unnecessarily lose otherwise valid data.

---

## Q5 — Import history retention

### Question

How long should import-run metadata be retained?

### Decision

```text
A
```

Retain all import records indefinitely in V1; no purge mechanism.

### Why

Import-run records are small diagnostic/audit artifacts.

Adding configurable purge/archive behavior during this pilot adds implementation scope without a demonstrated need.

### Important distinction

This decision applies to:

```text
import execution history
```

It does **not** mean:

```text
historical rollout snapshots
trend charts
progress-over-time business history
```

Feature 001 still maintains the latest successful active business dataset rather than implementing business trend history.

---

# 8. `/speckit.plan` generated artifacts

The plan generated technical design artifacts such as:

```text
plan.md
research.md
data-model.md
contracts/import-api.md
quickstart.md
```

It also proposed technical decisions including:

```text
.NET 9
ClosedXML
atomic activation pattern
DB-backed import lock
header-driven pipeline
compatibility test strategy
```

---

# 9. Major plan problem — planning defaults contradicted clarified decisions

The generated plan displayed four "stakeholder confirmation" decisions.

Some of them did **not** match what had already been decided in `/speckit.clarify`.

## Contradiction 1 — duplicate row IDs

### Plan default

```text
accept first, warn on duplicates
```

### Clarified specification

```text
accept all otherwise-valid occurrences and warn
```

### Why the plan default was wrong

Accepting only the first row silently discards later business rows.

That contradicts the clarified decision that Excel `#` is not the persistent unique identity.

---

## Contradiction 2 — import history

### Plan default

```text
retain last 100 records
```

### Clarified specification

```text
retain all import-run records in V1
```

### Why the plan default was wrong

The plan invented a retention limit that stakeholders had not selected.

---

## Missing-sheet wording was too compressed

Plan summary:

```text
fail workbook, fail batch
```

The actual clarified behavior is more precise:

```text
record the error
continue validating remaining sheets/workbooks
collect all diagnostics
fail only at activation
```

This distinction matters because diagnostic completeness is part of the desired operator experience.

---

## Invalid date

Plan behavior:

```text
null + non-fatal warning
```

This matched the clarified decision.

---

## Corrective action

The plan was instructed to:

- treat `spec.md` and the constitution as source of truth,
- remove planning defaults that contradicted clarified requirements,
- update `plan.md` / `research.md`,
- and not generate tasks until corrected.

## SDD lesson

> `/speckit.plan` is not allowed to silently redefine an already-clarified business requirement.

This became one of the strongest findings in the pilot.

---

# 10. Prompt-size/process learning during `/plan`

An oversized plan prompt was initially considered.

After reviewing the Spec Kit workflow, the better SDD pattern was established:

```text
constitution.md
    = project-wide governance

spec.md
    = feature behavior / business intent

plan.md
    = technical design

tasks.md
    = executable work
```

Therefore downstream commands should consume prior artifacts instead of receiving the entire project story again.

## Decision

- detailed constitution prompt: acceptable because it establishes one-time project governance,
- detailed `/specify` prompt: acceptable because it creates the feature source of truth,
- `/plan` prompt: should be comparatively short and focus on technical direction not already captured,
- `/implement`: should primarily execute generated tasks/artifacts rather than repeat context.

## SDD lesson

> Put context into the artifact where it belongs; do not repeatedly recreate the same context in every prompt.

---

# 11. Manual `spec.md` review found stale contradictions

After clarify/plan, `spec.md` was manually reviewed from the generated preview.

Several leftover phrases still reflected the earlier wrong model.

## Problem 1 — User Story 1 still said "one workbook per initiative"

The spec still contained wording equivalent to:

```text
one workbook per supported rollout initiative
```

This contradicted the corrected source model.

### Required correction

```text
one or more DR workbooks,
each containing all six required initiative sheets
```

---

## Problem 2 — Acceptance scenario repeated the same incorrect model

The acceptance scenario referred to:

```text
DR Excel workbooks (one per initiative)
```

It needed the same correction.

---

## Problem 3 — Edge cases assumed initiatives were separate workbooks

Examples included:

```text
What happens when a workbook for one or more initiatives is missing?
What happens when the same initiative appears in more than one workbook?
```

Those edge cases were no longer aligned with the real DR-workbook model.

They were replaced with DR-oriented questions such as:

```text
What happens when no DR workbook is submitted?
What happens when a DR workbook is missing a required initiative sheet?
What happens when the same DR workbook is submitted twice?
What happens when two workbooks resolve to the same DR identity?
```

---

## Problem 4 — Entity definition said "exactly six sheets"

The `Workbook Submission` entity said approximately:

```text
contains exactly six sheets
```

But the true business rule is:

```text
contains all six required initiative sheets
```

A workbook may contain:

```text
six required initiative sheets
+
additional harmless/reference sheets
```

The requirement should not reject those unless explicitly stated.

---

## Problem 5 — heading still said "Unresolved Behaviors"

The decisions were already resolved, but the section heading still implied they were unresolved.

It was appropriate to rename it to:

```text
Resolved Behavior Decisions
```

## SDD lesson

Generated artifacts require cross-reading after clarification because stale phrases can survive even when the main functional requirements have been updated.

---

# 12. Specification quality checklist (`checklists/requirements.md`)

A generated file appeared under:

```text
checklists/requirements.md
```

This file is not the product's business requirement list.

Its purpose is to validate the quality of `spec.md`.

It checked items such as:

```text
No implementation details
Focused on user value/business needs
Written for non-technical stakeholders
No [NEEDS CLARIFICATION] markers
Requirements are testable/unambiguous
Success criteria measurable
Acceptance scenarios defined
Edge cases identified
Scope clearly bounded
Dependencies/assumptions identified
```

The checklist eventually showed all relevant items checked.

## SDD lesson

This is a **specification quality gate**, not the implementation task list.

---

# 13. Third `/speckit.clarify` session — remaining data semantics

A later clarification pass found additional gaps that became visible only after the workbook model was corrected.

## Q1 — How rows from multiple DR workbooks are combined

### Question

If multiple DR workbooks are submitted, how are their rows represented in the active dataset?

### Decision

```text
A
```

Merge rows into a common dataset per initiative.

### Final model

```text
DR-A workbook ─┐
DR-B workbook ─┼─> Backend Unit Testing initiative rows
DR-C workbook ─┘
```

Each normalized row still carries DR/source provenance.

### Why

The dashboard needs the organization-wide rollout picture while retaining the ability to filter/group by DR.

DR is an attribute/provenance dimension, not a separate active dataset.

---

## Q2 — Same row number across different DR workbooks

### Question

What if two different DR workbooks both contain row `#10` on the same initiative sheet?

### Decision

```text
A
```

Accept both rows and record a non-fatal warning.

### Why

Row numbers are DR-local/source identifiers and are not globally unique.

Two DRs assigning the same numeric row number is not sufficient evidence that one record should be discarded.

The rows remain distinguishable through source/DR provenance.

---

## Q3 — Zero-workbook submission

### Question

What if an operator submits an empty batch?

### Decision

```text
A
```

Reject immediately.

### Final behavior

- no processing,
- no dataset creation,
- clear error such as:

```text
No workbooks were provided
```

### Why

An empty submission cannot produce a valid active dataset and should not be recorded as a harmless skipped operation.

---

## Q4 — Additional non-required sheets

### Question

The entity description said extra sheets were allowed, but there was no formal requirement. Should this be made testable?

### Decision

```text
A
```

Formalize it as a requirement.

### Final behavior

Additional sheets beyond the six required initiative sheets:

- are allowed,
- are ignored,
- do not cause workbook validation failure.

### Why

If this behavior matters, it should exist as a formal testable requirement rather than only as descriptive entity text.

---

## Q5 — Duplicate DR workbook identity

### Question

How should the system know whether two workbooks in one batch represent the same DR?

The proposed options did not exactly represent the desired behavior.

### Decision

Custom answer:

```text
Reject duplicate filename-derived DR identity
```

### Final intent

- derive DR identity using the approved filename convention,
- if two workbooks in the same batch resolve to the same DR identity, reject the batch as a validation error.

### Why

Simply checking for an identical physical filename is weaker than checking the derived DR identity.

Allowing the same DR twice could duplicate that DR's rollout records in the merged active dataset.

## SDD lesson

Custom answers are sometimes preferable to choosing the closest multiple-choice option when none exactly represents the business rule.

---

# 14. `/speckit.checklist` gate — first interaction problem

When the custom checklist command was invoked, the message was initially incomplete:

```text
Create a checklist for the following domain...
```

Spec Kit therefore asked which domain to cover:

```text
Ingestion pipeline
Validation & diagnostics
Activation & safety
API contracts
Data model
```

## Decision

Instead of choosing only one domain, a custom end-to-end scope was provided:

```text
End-to-end Feature 001 readiness:
ingestion pipeline,
validation and diagnostics,
activation safety,
data model,
API contracts,
and consistency with the clarified specification and constitution.
```

## Why

The purpose at this stage was to determine whether the **entire Feature 001 design was ready for task generation**, not only one technical slice.

---

# 15. Checklist question — who uses the checklist?

Spec Kit asked:

```text
Who uses this checklist and when?
```

Options included:

```text
Author — pre-tasks gate
Plan reviewer
Both — author self-check, then reviewer sign-off
```

## Decision

```text
3 — Both
```

## Why

This gives the checklist two purposes:

1. author self-review before `/speckit.tasks`,
2. reviewer/manager sign-off on feature readiness.

For the SDD pilot, this provides stronger evidence than treating the checklist as an AI-only artifact.

---

# 16. Checklist question — explicit cross-artifact traceability

Spec Kit asked whether the previously clarified decisions should each receive explicit traceability checks across artifacts.

Examples included:

```text
missing sheet
duplicate rows
history retention
invalid dates
```

## Decision

```text
1 — Yes
```

Each important clarified decision gets its own checklist items.

## Why

The objective is to verify that a decision does not exist only in one document.

Important rules should be traceable through:

```text
spec.md
    ↓
plan.md
    ↓
data-model / contracts
    ↓
tasks.md
    ↓
tests
```

This directly addresses the earlier problem where `/plan` overrode already-clarified spec decisions.

---

# 17. Environment/setup question encountered during planning

A process question arose:

> Should there be a separate Feature 000 only for creating the .NET solution, installing tooling, configuring EF Core/database, etc.?

## Decision

Do **not** create an artificial business Feature 000 solely for technical setup.

Use this separation:

### Project/developer prerequisites

Examples:

```text
verify/install .NET SDK
verify Node/npm
verify Git
initialize Spec Kit/repository
```

These are environment/bootstrap prerequisites.

### Feature 001 setup/foundational tasks

Because Feature 001 is the first greenfield implementation feature, its generated tasks may legitimately include:

```text
create .sln
create ASP.NET Core project
create Domain/Infrastructure/Test projects
add required packages
configure EF Core
create DbContext
create migration/database
```

## Why

Technical scaffolding is not an independent user/business capability and therefore should not be turned into a fake feature.

---

# 18. Key problems found across the execution

## 18.1 Domain misunderstanding

Example:

```text
six workbooks
```

instead of:

```text
multiple DR workbooks, each with six initiative sheets
```

**Mitigation:** manual review against real source evidence.

---

## 18.2 Missing source-data variability

Initial thinking did not fully capture that DR workbooks can contain extra/variant columns.

**Mitigation:** moved the requirement back into `spec.md` and required header-driven normalization.

---

## 18.3 Ambiguity hidden inside implementation choices

Examples:

```text
unknown status severity
unreadable workbook behavior
invalid dates
duplicate IDs
formula values
concurrency
history retention
```

**Mitigation:** resolve them in `/speckit.clarify` before implementation.

---

## 18.4 Planning defaults contradicting specification

Examples:

```text
accept first duplicate
retain last 100 imports
```

despite explicit clarified decisions to:

```text
retain all duplicate rows with warning
retain all import records in V1
```

**Mitigation:** treat `spec.md` as source of truth and review `plan.md` before tasks.

---

## 18.5 Stale text remained after corrections

Examples:

```text
one workbook per initiative
exactly six sheets
initiative-workbook edge cases
```

**Mitigation:** manual full-document review after clarify/plan.

---

## 18.6 Important behavior existed only in descriptive text

Example:

```text
extra sheets permitted
```

was mentioned in an entity definition but not in a formal functional requirement.

**Mitigation:** promote important observable behavior into a testable requirement.

---

## 18.7 Multiple-choice recommendations were not always sufficient

Example: duplicate DR identity.

**Mitigation:** use a custom clarification answer when none of the generated options accurately expresses the business rule.

---

## 18.8 Repeating too much context in downstream prompts

A giant plan prompt risked repeating or conflicting with the specification.

**Mitigation:** use artifact-driven context:

```text
constitution → project rules
spec        → feature behavior
plan        → technical design
tasks       → work breakdown
```

---

# 19. Final clarification/decision summary

| Topic | Decision |
|---|---|
| DR workbook structure | One or more DR workbooks; each contains six required initiative sheets |
| Unknown status | Keep raw value + non-fatal warning |
| Unreadable workbook | Record error, continue other files, fail batch at activation |
| Formula cells | Read cached/last-computed value |
| Import concurrency | One active import; reject second concurrent submission |
| Missing required sheet | Record error, continue validation, fail at activation |
| Supported format | `.xlsx` only |
| Duplicate row ID within one sheet | Keep all rows + warning |
| Invalid optional date | `null` + warning; retain row |
| Import history retention | Retain all import-run records in V1; no purge |
| Different DR column layouts | Common header-driven parser; no DR-specific parsing |
| Multi-workbook aggregation | Merge rows per initiative; retain DR provenance |
| Same row ID across DR workbooks | Accept both + warning |
| Empty import batch | Reject immediately |
| Additional non-required sheets | Allow/ignore; do not fail validation |
| Duplicate DR identity in same batch | Reject duplicate filename-derived DR identity |
| Checklist scope | End-to-end Feature 001 readiness |
| Checklist actor | Author self-check + reviewer sign-off |
| Traceability | Explicit cross-artifact checks for clarified decisions |

---

# 20. What this execution demonstrated about SDD

The strongest learning from Feature 001 is:

> The value of Spec Kit is not that the AI generates a perfect specification on the first attempt.

The useful behavior was the sequence:

```text
high-level intent
    ↓
generated specification
    ↓
clarification
    ↓
human correction
    ↓
technical plan
    ↓
cross-artifact contradiction found
    ↓
plan correction
    ↓
manual spec review
    ↓
additional clarification
    ↓
quality/readiness checklist
```

The process repeatedly surfaced decisions before they became hidden implementation assumptions.

The most valuable human contributions were:

- correcting the workbook/sheet domain model,
- explaining real DR schema variation,
- deciding which legacy behaviors must remain compatible,
- distinguishing warnings from blocking errors,
- rejecting AI defaults that would discard data,
- preventing business rules from being redefined in the plan,
- and verifying that requirements remain traceable across artifacts.

---

# 21. Recommended next execution sequence

After the current checklist is complete and reviewed:

```text
review checklist findings
        ↓
correct spec/plan if checklist finds gaps
        ↓
/speckit.tasks
        ↓
review generated Setup / Foundational / User Story tasks
        ↓
/speckit.analyze
        ↓
fix cross-artifact inconsistencies
        ↓
/speckit.implement
        ↓
tests / representative workbook validation
        ↓
/speckit.converge
```

Do not treat task generation as an automatic approval of the prior artifacts. Continue the same review discipline used during specify, clarify, and plan.

---

# 22. Manager-ready summary

> Feature 001 showed why the SDD gates matter. The first generated spec misunderstood our Excel model, clarification corrected it, later discovery exposed DR-specific column variation, and the generated technical plan then introduced defaults that contradicted already-approved clarification decisions. Manual review caught those issues before implementation. Additional clarification and checklist passes then made the remaining edge cases explicit. The artifacts are therefore not just generated documentation; they are the trace of how uncertainty was progressively removed before code.
