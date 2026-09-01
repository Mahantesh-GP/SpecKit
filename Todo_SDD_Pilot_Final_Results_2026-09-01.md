# Todo SDD Pilot - Final Results
## End-to-End Journey, Findings and Recommended Operating Model

**Date:** 1 September 2026

## 1. Executive Summary

The Todo pilot was used to validate how Spec-Driven Development (SDD), using Spec Kit, can fit into an existing Scrum and Azure DevOps (ADO) delivery model without replacing backlog management, refinement, sprint planning, testing, pull requests, or normal engineering governance.

The pilot evolved from an SDD-only demonstration into a realistic mixed-sprint experiment. Medium or more involved stories were taken through an SDD-assisted path, while a deliberately small and obvious UI change was completed through normal development. This comparison produced the central result: SDD is most useful as a selective engineering approach, not as a mandatory lifecycle for every user story.

## 2. Pilot Objective

Validate an end-to-end operating model in which ADO remains the system of record for user stories and sprint execution, while SDD is introduced only where structured specification, clarification, planning, and traceability add meaningful engineering value.

The Todo application was intentionally small so that the experiment could exercise the complete lifecycle quickly and focus on process learning rather than application complexity.

## 3. Working Baseline

The pilot first established a working Create and View Todos baseline. Subsequent requirements were therefore handled as changes to an existing application, allowing the experiment to exercise refinement, change impact, implementation, testing, and pull-request flow.

## 4. Actual User Stories and Delivery Paths

| User Story | Path | Purpose / Result |
|---|---|---|
| Create and View Todos | Baseline | Established working application |
| Complete a Todo | SDD-assisted | Specification/refinement through implementation and review |
| Delete a Todo | SDD-assisted | Specification/refinement through implementation and review |
| Improve Todo List UX & Pagination | SDD-assisted | Structured planning and implementation for broader UX change |
| Replace Todo Action Text with Icons | Normal development | Deliberate lightweight comparison; no full SDD lifecycle |

### Consolidated Flow

```text
ADO Requirement
    |
Refinement
    |
Clarify Acceptance Criteria
    |
Ready for Sprint
    |
Sprint Planning
    |
Assess whether SDD adds meaningful value
    |-----------------------------|
   YES                            NO
    |                              |
SDD-assisted path             Normal development
Specify / Clarify                 |
Plan / Tasks                      |
Analyze / Implement             Code
    |                              |
    |----------- Test / Review ----|
                  |
                  PR
                  |
                Merge
                  |
             ADO Complete
```

## 5. End-to-End Journey

Step 1 - Backlog: User stories and acceptance criteria were maintained in ADO. ADO remained authoritative for business requirements, sprint assignment, state, and delivery tracking.

Step 2 - Refinement: Candidate stories were reviewed before sprint commitment. ADO story content was copied into repository refinement inputs so Spec Kit could consume the requirement without becoming a second business system of record.

Step 3 - Specification: /speckit.specify was used during refinement for SDD-selected work. Related stories could be grouped into a coherent capability rather than forcing one isolated specification for every tiny story.

Step 4 - Clarification: /speckit.clarify was used to expose missing decisions and ambiguity. Material questions were resolved by the human requirement owner. Where a decision changed expected behavior, the ADO discussion or acceptance criteria was updated so the business decision remained visible in ADO.

Step 5 - Ready for Sprint: Once acceptance criteria were sufficiently clear, material questions were resolved, dependencies were understood, and the specification aligned with ADO, the story could be marked Ready-For-Sprint.

Step 6 - Sprint Planning and SDD Selection: The sprint intentionally contained both SDD-assisted and normal-development work. The decision was based on whether SDD was expected to add value, rather than automatically applying it to every story.

Step 7 - Technical Planning: For SDD-selected work, the specification described what and why, while the plan captured how the solution would be implemented in the existing Flask, SQLite, and server-rendered application.

Step 8 - Tasks and Analysis: The technical plan was decomposed into actionable work. Consistency analysis was used as a pre-implementation quality gate across requirement, specification, plan, and tasks. Detailed Spec Kit tasks did not need to be mirrored one-for-one into ADO.

Step 9 - Implementation: The selected stories were implemented against the working baseline. SDD artifacts guided development but did not replace developer judgment.

Step 10 - Test, Review and PR: Changes continued through normal engineering governance: code, testing/review, pull request, review, merge, and ADO completion. SDD supplemented this process rather than replacing it.

## 6. Scenario A - Where SDD Added Value

Complete Todo, Delete Todo, and Improve Todo List UX & Pagination were used to demonstrate the SDD-assisted path. These stories provided enough behavior, interaction, or implementation impact to justify structured refinement and planning.

The important value was not document generation itself. The value was creating an explicit chain from ADO requirement to specification, clarification decisions, technical plan, executable tasks, implementation, testing, and PR evidence.

## 7. Scenario B - Where SDD Added Little Value

The lightweight story to replace Todo action text with icons was deliberately handled through normal development. Examples included Add Todo to '+', Complete to a check icon, and Delete to a cross icon.

Because the requirement was localized, obvious, low-risk, and introduced no meaningful business-rule or data-model ambiguity, a full Specify -> Clarify -> Plan -> Tasks -> Analyze lifecycle would have added process overhead without proportionate value.

The story therefore followed the normal path: ADO work item -> developer task -> code -> test -> PR -> completion.

## 8. Resulting Operating Model

SDD should sit inside the existing Scrum/ADO lifecycle as an optional engineering path. ADO continues to own the backlog, user stories, acceptance criteria, sprint, state, and delivery tracking. The team evaluates each story and chooses either an SDD-assisted path or normal development.

A practical decision hypothesis from this pilot is to consider SDD when work has meaningful ambiguity, cross-layer or data impact, multiple edge cases, coordination needs, regression risk, or likely requirement change. Normal development remains appropriate for small, localized, well-understood, low-risk changes. This is a pilot hypothesis to validate with real project teams, not yet an organization-wide policy.

## 9. Sprint Execution Learning

There is no need for a separate 'SDD sprint.' A realistic sprint can contain both SDD and non-SDD work. If committed work finishes earlier than estimated and capacity becomes available, the team can pull an appropriate Ready-for-Sprint backlog item, just as it would in a normal sprint.

The pilot also showed that SDD is better introduced during refinement for suitable stories, rather than waiting until a developer starts implementation after sprint commitment.

## 10. Key Findings

1. SDD does not replace Scrum, ADO, BA/PO decisions, QA, code review, or pull requests.

2. ADO should remain the authoritative business and sprint-tracking source; SDD artifacts support engineering reasoning.

3. Human review is essential. AI-generated specifications and plans should not automatically be treated as approved requirements.

4. Clarification is most valuable when its decisions flow back to the authoritative requirement source.

5. Related stories may be grouped into a coherent capability specification when that improves understanding; a full independent spec for every tiny story is unnecessary.

6. SDD has visible overhead. That overhead is justified only when the reasoning, clarity, coordination, or traceability gained is worth it.

7. A mixed sprint provides a better evaluation than an SDD-only demonstration because it exposes both the value and the boundary of the approach.

8. The Todo pilot validates the operating model, but real enterprise projects are still needed to validate the selection criteria under genuine complexity, dependencies, and team constraints.

## 11. Evidence Produced During the Pilot

The experiment produced evidence across multiple levels: ADO user stories and refinement discussions; Ready-For-Sprint and SDD-Candidate tagging; repository refinement inputs; Spec Kit specifications/plans/tasks; implemented Flask/SQLite application behavior; test/review work; pull requests; and a deliberately non-SDD implementation for comparison.

The working application evolved beyond Create/View to demonstrate Complete, Delete, pagination/UX improvements, and compact action icons.

## 12. Final Result

The pilot supports a selective adoption model: use SDD where additional specification, clarification, planning, and traceability provide meaningful value; use the normal development path where the work is already clear and lightweight.

The recommended next phase is not to add more arbitrary Todo functionality. It is to complete any remaining test/review/closure evidence, capture comparable effort and AI/token usage where available, document concrete clarification/value examples, and then validate the operating model with real project teams over a longer period.

## 13. Manager-Ready Closing Statement

We started with normal ADO requirements, introduced SDD during refinement for selected stories, carried those stories through planning, implementation, testing and PR, and deliberately completed a lightweight story without SDD. The comparison showed that SDD can complement our existing Scrum process, but it should be applied selectively where its additional reasoning and traceability justify the overhead.
