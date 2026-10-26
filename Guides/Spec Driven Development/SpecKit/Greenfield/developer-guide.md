Greenfield \| Developer-first execution guidance \| Proposed for team review

STATUS: Proposed practice, not approved enterprise policy. This supplement clarifies execution and accountability; existing engineering standards, ADO requirements and approval paths take precedence.

# How to use this document

Read this operational supplement first. Then use the original detailed chapters that follow for prompts, examples, architecture considerations and pilot evidence. A developer should not have to perform every optional tool evaluation to start a feature.

# Activity ownership and cadence (proposed)

| Activity                          | Suggested owner                            | When                                             | How                               | Output                                    |
|-----------------------------------|--------------------------------------------|--------------------------------------------------|-----------------------------------|-------------------------------------------|
| Repository/workspace boundary     | Lead developer / architect                 | At adoption; revisit on structural change        | Team review                       | Confirmed scope and owner                 |
| Existing rules / constitution     | Developer drafts; authorized owner reviews | At adoption; amend only for durable changes      | Team agreement + approval         | Reviewed constitution or open decision    |
| Story and acceptance criteria     | Product owner / BA; team refines           | Each feature                                     | Refinement                        | Approved source / acceptance criteria     |
| Focused discovery                 | Assigned developer                         | Before each feature; depth risk-based            | Individual + targeted peer review | Verified paths, behavior, tests, unknowns |
| Specify / clarify                 | Assigned developer + requirement owner     | Each new feature; clarify when needed            | Draft + resolve ambiguity         | Reviewed feature specification            |
| Plan / tasks / consistency review | Developer + technical reviewer             | Per selected feature workflow                    | Review impacts and conflicts      | Reviewed plan, tasks, unresolved issues   |
| Implementation and verification   | Developer + QA/reviewer                    | Each delivered change                            | Existing PR/QA gates              | Test results, diff and review evidence    |
| Cross-team dependency assessment  | Lead / interface owner                     | When shared interfaces or ownership are affected | Coordinated review                | Impact and decision record                |

# Minimum path vs conditional work

Core: approved story → relevant code/architecture discovery → specify → clarify material ambiguity → plan → tasks → consistency review (where supported) → authorized implementation → tests/PR/ADO evidence.

Conditional: constitution amendment, full dependency workshop, broad repository mapping, persistent context notes, Repomix packaging and third-party community extensions. Use only when justified by the change and approved local tooling.

# Decision points and escalation

- If business scope or acceptance criteria are unclear: ask the authorized product/BA owner before treating generated text as a requirement.

- If existing architecture, security controls or shared contracts conflict with a generated plan: consult the appropriate technical authority.

- If the story modifies an already approved feature: identify the authoritative baseline and decide how changes will be documented before editing downstream artifacts.

- If a test has not been executed: record it as not run; passing baseline tests do not establish that proposed new behavior works.

- If a community tool or extension has not been piloted: label it an evaluation candidate, not a required recommendation.

# Evidence labels used throughout

| Label             | Meaning                                                                              |
|-------------------|--------------------------------------------------------------------------------------|
| Pilot observation | Observed in the documented experiments; avoid extrapolating beyond the evidence.     |
| Proposed practice | Suggested operating approach requiring team validation/approval.                     |
| Not validated     | A claim, capability, outcome or third-party tool not yet demonstrated in the pilots. |

# Completion checklist

☐ Story source, owner, acceptance criteria and approval status are recorded.

☐ Relevant code, contracts, architecture and regression tests have been checked.

☐ Material ambiguities and cross-feature impacts have named decision owners.

☐ Generated artifacts align with the approved requirement and current implementation.

☐ Implementation, tests and PR review are completed under existing controls.

☐ Evidence distinguishes executed results from assumptions and open questions.

Source preservation note: The detailed guide below is carried forward from the available editable draft. This supplement is new proposed guidance. The screenshots of the combined 41-page guide were used as a visual cross-check; exact equality with that locally edited combined document has not been established.

Greenfield SDD Adoption Guide

GitHub Spec Kit \| Developer guidance \| Version 0.1 — Draft for EA and project-team review

Companion to the Brownfield SDD Adoption Guide. This guide is a proposed workflow, not a ratified enterprise policy.

# Executive summary

Greenfield means building a new application or service where the team can define architecture and engineering guardrails before implementation. Spec-Driven Development (SDD) helps teams agree on behavior, plan the design, review consistency and then implement and test. It complements existing Scrum, Azure DevOps and human engineering reviews; it does not replace them.

Evidence basis: our earlier .NET 8 + React + SQL Server Todo pilot exercised the Spec Kit lifecycle. This guide uses that pilot as an illustrative reference, but does not claim quantified speed, token savings, or enterprise-wide validation.

# How to use this guide

| Reader                        | Start here                                       |
|-------------------------------|--------------------------------------------------|
| New team                      | Chapters 1 and 2, then the quick-start checklist |
| Team delivering later stories | Chapter 3                                        |
| ADO / Scrum lead              | Chapter 4                                        |
| Reviewer, QA or manager       | Chapter 5 and evidence checklist                 |

# One-page developer quick start

- Confirm approved business scope and work-item reference before development.

- Create or open the project repository and choose the workspace boundary.

- Initialize Spec Kit using its installed instructions; inspect generated folders and commands.

- Draft a concise project constitution; have the responsible project owner review it.

- Run /speckit.specify and /speckit.clarify for the selected feature.

- Run /speckit.plan and /speckit.tasks; review architecture and dependencies.

- Run /speckit.analyze (or the corresponding installed command) and resolve material findings.

- Run /speckit.implement only after approvals and a reviewed plan; inspect generated changes.

- Execute tests, code review, security checks and normal PR/ADO closure; record evidence.

# Chapter 1 — Project initialization and constitution

## 1.1 Before installing Spec Kit

- Confirm product purpose, stakeholders, initial release boundary and source of approved requirements.

- Agree technology stack, deployment target, repository ownership, branching strategy and CI expectations.

- Identify quality controls: testing, security, observability, accessibility, performance and data protection.

- Decide whether the repository is one governed workspace or several independently governed areas.

## 1.2 Initialize at the repository boundary

Create the repository and initialize Spec Kit using the official setup instructions for the installed version. Command availability varies by CLI version and editor integration. Avoid initializing separately for every project folder unless the team intentionally maintains separate workspaces.

Official references: https://github.com/github/spec-kit and https://github.com/github/spec-kit/blob/main/README.md

## 1.3 Constitution: durable rules, not feature requirements

| Include                                     | Keep elsewhere                                  |
|---------------------------------------------|-------------------------------------------------|
| Approved stack and architectural boundaries | Individual user stories and acceptance criteria |
| Testing, security and quality minimums      | A single feature’s field names or UI text       |
| Review/approval and exception mechanism     | Temporary debugging notes                       |
| Data handling and integration constraints   | Unapproved ideas or experiment outcomes         |

## 1.4 Constitution prompt (adapt, do not blindly approve)

/speckit.constitution

Create a draft constitution for a NEW application.

Use only confirmed project decisions listed below: \[stack\], \[architecture\],

\[testing\], \[security\], \[deployment\], \[review process\].

Separate confirmed decisions from open questions. Do not invent approvers,

quality thresholds, infrastructure or compliance rules.

Keep the rules reusable across features and ask for human review.

## 1.5 Review gate

The responsible project owner reviews the proposed constitution before teams rely on it as a mandatory constraint. Record who approved it, when, and how later amendments or justified deviations will be handled. If ownership is undecided, mark it open rather than inventing a role.

# Chapter 2 — First feature: from idea to working code

## 2.1 Choose a bounded first story

Prefer a vertical slice with observable behavior and testable acceptance criteria. Avoid selecting the whole platform, an undefined integration, or multiple unrelated capabilities as the first feature.

## 2.2 Example from the Todo pilot

Illustrative application: .NET 8 backend, React frontend and SQL Server persistence. A suitable first slice is creating and viewing a Todo item. Confirm the actual repository architecture before applying this example; it is not the same as the later Flask/Jinja brownfield pagination experiment.

## 2.3 Feature lifecycle

| Step      | Developer action                                       | Review output                                 |
|-----------|--------------------------------------------------------|-----------------------------------------------|
| Specify   | /speckit.specify — describe who needs what and why     | spec.md and acceptance criteria               |
| Clarify   | /speckit.clarify — resolve ambiguity, edge cases       | Explicit answers / updated specification      |
| Plan      | /speckit.plan — map feature to architecture            | plan.md, contracts / data model as applicable |
| Tasks     | /speckit.tasks — actionable delivery sequence          | tasks.md with test and dependency coverage    |
| Analyze   | /speckit.analyze — consistency review                  | Findings; decisions and resolutions           |
| Implement | /speckit.implement — generate/change code under review | Code, tests and updated tasks                 |
| Validate  | Run build, tests, review and PR process                | Recorded evidence; approval to merge          |

Slash-command spellings are examples from the established pilot workflow. Verify names in the installed Spec Kit version and project skills; do not assume every command is bundled into a single automated workflow.

## 2.4 Ready-to-use first-feature prompt

/speckit.specify

Feature: Create and list Todo items.

Users can enter a title, save a Todo, and see it in the list.

Specify expected validation, empty-state behavior, error states,

and measurable acceptance criteria.

Do not prescribe database tables or React component structure in the

business specification. Flag unanswered product decisions.

## 2.5 What developers must verify

- The specification reflects approved business intent, not merely AI suggestions.

- The plan follows the reviewed architecture and approved technology stack.

- The task list covers tests, error handling and nonfunctional requirements relevant to scope.

- Analysis findings are triaged; no material unresolved contradiction is silently ignored.

- Tests pass in the actual environment; no claims are made from generated artifacts alone.

# Chapter 3 — Subsequent features and reusable context

## 3.1 What to reuse

Reuse the approved constitution, architecture decisions, shared contracts, established test conventions, CI pipeline and lessons from the first feature. Do not recreate the constitution for every new user story.

## 3.2 What to recheck

For each new feature, inspect touched modules, APIs, data entities, security boundaries and dependencies. Project context is reusable, but it may become stale as code changes.

## 3.3 Optional concise context notes

A short PROJECT-CONTEXT.md can summarize stable architecture, module map, commands and authoritative links. A feature-specific note can list entry points, impacted contracts, baseline tests and open questions. These are optional aids—not Spec Kit requirements—and must be checked against code and approved decisions.

## 3.4 Reusable feature prompt

Before specifying \[NEW FEATURE\], read the approved constitution,

existing architecture notes, related feature specs, API contracts and

relevant code paths. Identify reuse opportunities, dependency risks,

and uncertain assumptions. Keep investigation limited to the touched

modules. Cite file paths. Do not rewrite existing specifications or

change code during discovery.

## 3.5 Avoid context overload

For a small change, focused repository search and Copilot context are often sufficient. For broader review, tools such as Repomix can package a selected file set and estimate its size. More context is not automatically better: compare usefulness, freshness, token size and review effort. Do not claim token savings without measured runs.

# Chapter 4 — Integrating SDD with Agile and Azure DevOps

## 4.1 Keep the current delivery process

Azure DevOps remains the work-item system where the team uses it. SDD artifacts supplement refinement, planning, implementation and review; they do not create a parallel sprint or bypass approvals.

| Existing activity  | Suggested SDD addition                                     |
|--------------------|------------------------------------------------------------|
| Backlog refinement | Specify intent; clarify business ambiguity                 |
| Sprint planning    | Review feasibility, plan, tasks and dependencies           |
| Development        | Implement against reviewed specification and architecture  |
| Code review / QA   | Compare code and tests against accepted criteria           |
| Story closure      | Record test evidence, review decisions and remaining risks |

## 4.2 Traceability

Link the ADO work item to the relevant feature folder and PR. Preserve requirement IDs where the team already uses them. Do not assume a particular ID naming convention is mandatory unless the organization approves it.

## 4.3 Approvals and experiment boundaries

An AI-generated specification is not automatically an approved requirement. Label experiments and proposed enhancements clearly. Any exception to mandatory project rules must follow the approved governance process; do not invent an approver.

## 4.4 Example delivery evidence

- ADO story or equivalent approved source and acceptance criteria.

- Specification and clarification decisions.

- Plan, tasks and material analysis findings with resolution.

- PR, build and test run references.

- Reviewer sign-off and known limitations.

# Chapter 5 — Quality, governance and adoption rollout

## 5.1 Quality is not delegated to the AI

Human reviewers remain accountable for correctness, security, maintainability, tests and architecture. Spec Kit analysis checks artifact consistency; it is not a substitute for compilation, execution, threat modeling, test coverage or production readiness.

## 5.2 Recommended review checkpoints

| Checkpoint            | What to confirm                                                 |
|-----------------------|-----------------------------------------------------------------|
| Before planning       | Business scope and ambiguities resolved                         |
| Before implementation | Plan and tasks fit architecture; material conflicts addressed   |
| Before merge          | Code reviewed, tests run, defects resolved or accepted          |
| After pilot           | Time, rework, defects, clarity and adoption friction documented |

## 5.3 Evidence and limitations from our pilots

| Observation                                                                                  | Evidence status                                                 |
|----------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Greenfield Todo application exercised Spec Kit lifecycle                                     | Prior pilot experience; not a controlled productivity benchmark |
| Brownfield Adaptive Workflow / DocNav feature exposed real integration and governance issues | Separate brownfield pilot; use companion guide                  |
| Tool-driven token savings or faster delivery                                                 | Not established; measure before claiming                        |
| Enterprise-wide mandatory adoption policy                                                    | Not approved by this document                                   |

## 5.4 Suggested team rollout

- EA/project leads provide examples, recorded walkthroughs and hands-on enablement rather than only forwarding a document.

- Start with one suitable story in a willing team; agree the baseline delivery process and success measures.

- Capture which steps helped, what needed manual review and where tooling failed.

- Update guidance based on verified experience; seek approval before converting recommendations into mandatory policy.

## 5.5 Adoption success measures

For comparable stories, record elapsed engineering effort, clarification cycles, number of reopened defects, regression outcomes, review burden, token usage where measurable and developer feedback. Compare cautiously: story complexity and team familiarity may differ.

# Final readiness checklist

- Constitution reviewed by authorized owner.

- At least one bounded feature taken through specification, implementation and real tests.

- ADO/PR traceability and required approvals captured.

- Material architecture and security risks reviewed.

- Open limitations and optional tools clearly labeled.

- Pilot feedback incorporated before broader rollout.

# References and companion documents

- GitHub Spec Kit: https://github.com/github/spec-kit

- Spec Kit community extensions: https://github.github.com/spec-kit/community/extensions.html

- Brownfield SDD Adoption Guide v1.4 (separate companion document).

- Internal Todo greenfield pilot and Adaptive Workflow / DocNav pilot: team-held evidence; validate against current repositories before publication.
