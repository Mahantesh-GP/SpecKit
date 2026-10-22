Using "constitution-validation-assessment.md" and "constitution-assessment.md" as the primary evidence, prepare a proposed Brownfield Constitution for this existing Todo application.

Objective: Create a practical, reusable constitution structure for adopting GitHub Spec Kit in existing enterprise projects.

Instructions:

1. Read the existing ".specify/memory/constitution.md" and preserve its valid principles.
2. Use the 13 principles from the validation assessment as the starting point. Consolidate overlapping rules where appropriate, without losing important controls.
3. Separate the constitution into:
   - Part 1: Mandatory brownfield engineering principles.
   - Part 2: Project-specific architecture and coding conventions, grounded in repository evidence.
   - Part 3: SDD workflow, validation gates, and experimental exceptions.
   - Part 4: Governance, approvals, amendments, and ownership.
4. Address existing behavior preservation, backward compatibility, regression testing, minimal change scope, cross-feature impact, specification evolution, and Definition of Done.
5. Include a clear workflow for existing projects: discovery → constitution → specify/clarify → plan → tasks → analyze → implement → validate.
6. Mark all unresolved decisions as "REQUIRES APPROVAL". Do not invent organizational policies, owners, or approval decisions.
7. Clearly distinguish general enterprise guidance from Todo-specific implementation details.
8. Include a brief mapping showing how the original constitution principles are preserved or extended.
9. Do not treat AMD-001 as an approved business requirement.

Output: Create only "docs/brownfield-constitution-proposal.md".

Restrictions:

- Do not modify ".specify/memory/constitution.md".
- Do not modify existing specs, plans, tasks, application code, tests, or Azure DevOps.
- Do not execute Spec Kit commands.
- Do not implement any feature.

Stop after creating the draft and provide a summary of proposed principles, unresolved approvals, and files changed.