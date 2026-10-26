Developer adoption entry point \| Greenfield + Brownfield \| Review draft

Purpose: make it possible for a team to begin with one approved, bounded feature while respecting existing Scrum, Azure DevOps, architecture, security and PR/QA controls.

# Choose your path

| Situation                          | Start with                          | Focus                                                                               |
|------------------------------------|-------------------------------------|-------------------------------------------------------------------------------------|
| New application/service            | Greenfield guide                    | Confirm requirements, architecture and project guardrails before the first feature. |
| Existing application/service       | Brownfield guide                    | Preserve current behavior and approved rules; discover only the relevant slice.     |
| Existing Spec Kit team, next story | Relevant guide: subsequent features | Reuse verified context and recheck touched modules and tests.                       |
| Change to approved feature         | Brownfield: specification evolution | Record approved baseline, change owner, affected artifacts and authorization.       |

# First feature: practical execution

| Step                         | Who                            | Done when                                                                                          |
|------------------------------|--------------------------------|----------------------------------------------------------------------------------------------------|
| 1\. Confirm the story        | Product owner/BA + developer   | Approved scope, acceptance criteria, source link and owner.                                        |
| 2\. Confirm the workspace    | Lead developer / architect     | Repo boundary, existing constitution, architecture, local Spec Kit commands.                       |
| 3\. Discover current context | Assigned developer             | Relevant code and tests, known constraints, unknowns; read-only first.                             |
| 4\. Specify and clarify      | Developer + requirement owner  | Run installed /speckit.specify and /speckit.clarify as appropriate; review generated requirements. |
| 5\. Plan and task            | Developer + technical reviewer | Use installed /speckit.plan and /speckit.tasks; assess interfaces, dependencies and tests.         |
| 6\. Analyze and decide       | Developer + owners             | Use /speckit.analyze where available; resolve material findings and approvals.                     |
| 7\. Implement and validate   | Developer + QA/reviewer        | Use /speckit.implement only when authorized; test, inspect diffs and follow PR/ADO controls.       |

# When to stop and ask

- Requirement or acceptance criteria not approved or ambiguous.

- Shared API/schema/security change lacks a responsible reviewer.

- Constitution would introduce or override mandatory project rules.

- A generated spec conflicts with implemented behavior or an approved earlier feature.

- Tool output omits requested files or test evidence is missing.

# Validated vs proposed

Pilot observation: Greenfield .NET 8/React/SQL Server Todo exercised the Spec Kit lifecycle; Brownfield Adaptive Workflow/DocNav explored integration boundaries; Brownfield Flask Todo/Repomix generated a focused five-file context snapshot. These are not controlled productivity benchmarks.

Not validated: organization-wide token savings, delivery acceleration, adoption of community tools, or implementation of the experimental Todo AMD-001 10/20/50 pagination behavior.

# Guide navigation

Brownfield detailed guide: constitution → first-feature discovery → subsequent features → specification evolution → dependencies → large/multi-project repositories.

Greenfield detailed guide: initialization and constitution → first feature → subsequent features → Agile/ADO integration → quality and rollout.

Document status: Proposed developer guidance. Team-specific standards, authorization and actual installed Spec Kit command names take precedence.
