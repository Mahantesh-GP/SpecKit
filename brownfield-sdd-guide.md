Brownfield \| Developer-first execution guidance \| Proposed for team review

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

BROWNFIELD SDD ADOPTION GUIDE

GitHub Spec Kit \| Developer guidance \| Version 1.0 — Draft for Review

Audience: development teams, technical leads, QA and enterprise architecture reviewers.

Status: Working adoption guidance; not an approved enterprise policy. Project-specific controls and approvals take precedence.

# Executive summary

This guide explains how a team can adopt Spec-Driven Development (SDD) with GitHub Spec Kit in an existing codebase. Start from approved project requirements and current architecture, select a bounded feature, discover only relevant context, draft and review specifications, plan the change, analyze consistency, implement, and verify behavior with tests and normal code review. SDD complements existing Scrum and Azure DevOps processes; it does not replace them.

The six chapters cover constitution, first-feature discovery, subsequent features, specification evolution, cross-feature dependencies, and large multi-project repositories. Each chapter offers concrete developer steps and prompts. The pilot evidence appendix distinguishes observations from recommendations and unfinished experiments.

# How to use this guide

New team: read Chapters 1 and 2, then follow the quick start below. Existing Spec Kit team: use Chapter 3 for new features, Chapter 4 for changed requirements, Chapter 5 for dependencies, and Chapter 6 for large repositories. Use the relevant chapter, not all chapters for every change.

# Developer quick start — one-page workflow

1\. Choose an approved, bounded work item and identify its owner, acceptance criteria, affected area and existing ADO/issue link.

2\. Inspect repository layout, entry points, architecture, current tests, dependencies and project standards; record facts and unknowns. Do not start with a whole-repository rewrite.

3\. Confirm which repository/workspace constitution applies; preserve existing approved rules. Draft changes for review rather than silently activating new policy.

4\. Run /speckit.specify for the selected feature; review generated requirements against the approved story and existing behavior.

5\. Run /speckit.clarify when questions remain; resolve material ambiguity with the product or technical owner.

6\. Run /speckit.plan, then /speckit.tasks; review impacted APIs, data, modules, integration points and test work.

7\. Run /speckit.analyze or an equivalent consistency review supported by your workflow; address material gaps before implementation.

8\. Run /speckit.implement only after required decisions; inspect changes, execute relevant tests and regression checks, and complete normal PR/QA review.

9\. Record approvals, test evidence, unresolved risks and reusable lessons; update the constitution only for durable cross-feature rules with authorized review.

# Navigation

Chapter 1 — Brownfield Constitution

Chapter 2 — First-Feature Discovery

Chapter 3 — Subsequent Features

Chapter 4 — Specification Evolution

Chapter 5 — Cross-Feature Dependencies

Chapter 6 — Large and Multi-Project Repositories

Appendix A — Pilot findings, limits and next validation

— MAIN GUIDE BEGINS BELOW —

Chapter 1 \| Brownfield Constitution

Practical developer adoption guide • GitHub Spec Kit / SDD • Version 1.0 (guidance, not approved policy)

Purpose: help an existing-project team create a small, accurate, reviewable project constitution before using Spec Kit for feature delivery. This chapter is developer-facing guidance, not a replacement for enterprise architecture approval.

# 1.1 What a constitution is

A constitution records durable project engineering guardrails that should apply across future features: architectural boundaries, approved technologies, interface conventions, security expectations, quality checks, and governance. It guides AI-assisted specification, planning, and implementation. It is not a copy of the codebase, a feature backlog, a bug log, or a collection of speculative rules.

Brownfield difference: existing code, contracts, tests, deployment practices, and approved policies already exist. Discover them first; do not invent a new architecture or retroactively declare every current implementation non-compliant.

# 1.2 Before writing: discover the existing project

| Inspect                    | Capture / verify                                                                            |
|----------------------------|---------------------------------------------------------------------------------------------|
| Repository and modules     | Workspace/repository boundary; modules and ownership; build/test commands.                  |
| Architecture and contracts | Entry points, integrations, public APIs, data flows, established dependencies.              |
| Engineering standards      | Coding/testing/security standards, CI checks, review/approval requirements.                 |
| Delivery workflow          | ADO story/refinement, branches/PRs, QA gates, exception handling.                           |
| Existing constitution      | If present, preserve its approved principles and governance; propose changes separately.    |
| Unknowns                   | Record evidence gaps and an owner to clarify; never turn assumptions into mandatory policy. |

Practical note: one repository/workspace constitution is usually a sensible starting point for a multi-project repository. Do not initialize Spec Kit independently for every .csproj or subproject unless those areas are intentionally managed as separate workspaces.

# 1.3 What belongs in the constitution?

| Include (durable project rules)                                   | Keep elsewhere (feature-specific facts)                  |
|-------------------------------------------------------------------|----------------------------------------------------------|
| Preserve established public interfaces unless change is approved. | Feature 003 pagination default and new selector choices. |
| Follow existing security and authentication standards.            | A one-off API response or bug investigation.             |
| Use existing test and PR review gates.                            | A specific test failure and its fix.                     |
| Document who may approve exceptions or amendments.                | An experimental requirement without business approval.   |

Rule of thumb: if the statement should still govern the next ten unrelated features, it may belong in the constitution. If it only explains one story, keep it with that story.

# 1.4 Recommended first-time workflow

1\. Choose the workspace — Open the existing repository in VS Code. Confirm whether it is one governed workspace or multiple intentionally independent ones.

2\. Collect evidence — Review architecture documents, relevant source, contracts, test strategy, CI and existing approved standards.

3\. Check for an active constitution — If one exists, read it and identify immutable approved text; do not overwrite it during an experiment.

4\. Prepare a short constitution input — State confirmed guardrails, evidence sources, unresolved questions and intended approval route.

5\. Run Spec Kit constitution generation — Use /speckit.constitution (or the installed equivalent command) in the correct repository/workspace. Review the proposed changes before accepting.

6\. Validate manually — Check each MUST rule against existing evidence; remove feature-specific facts, unsupported claims and contradictions.

7\. Seek the proper review — Obtain review from the project’s authorized engineering/architecture owner before treating a proposal as active policy.

8\. Proceed to feature work — Use the approved constitution as context for specify → clarify → plan → tasks → analyze/review → implement → test.

# 1.5 Copy/paste prompt for VS Code Copilot

Use this as an input to prepare the constitution proposal. Replace bracketed items with actual project facts.

- We are adopting GitHub Spec Kit in an EXISTING repository: \[repository/workspace\].

- First inspect \[architecture docs\], \[coding/testing standards\], \[contracts\], \[CI\], and \[existing constitution if any\].

- Identify durable, already-established engineering rules supported by evidence. Cite file paths/sections for each proposed rule.

- Preserve existing approved architecture, interfaces, delivery controls, and constitution governance. Do not silently rewrite approved principles.

- Do not include feature-specific requirements, experimental changes, guessed owners, or unapproved approval gates.

- Separate confirmed rules, recommended rules needing approval, and open questions.

- Produce a concise constitution proposal and a short evidence/decision checklist. Do not modify the active constitution or application code without my explicit instruction.

# 1.6 Reusable constitution outline (illustrative, not approved policy)

The following is a structure to adapt; it is not a ready-to-ratify enterprise constitution.

A. Purpose and scope: Identify governed repository/workspace, included modules, and what this document does not govern.

B. Existing architecture guardrails: State verified module boundaries, approved dependency direction and integration constraints.

C. Requirements and traceability: Specify the approved requirement source(s), identifiers, and handling of unapproved experiments.

D. Quality and security: Reference existing tests, reviews, secrets/authentication rules, and regression expectations.

E. Change governance: Name the actual decision authority or mark as pending; document amendment, exception and versioning process.

F. Evidence and open decisions: Link sources and keep unresolved policy choices out of mandatory rules.

# 1.7 Validate before declaring it ready

- Every mandatory rule is supported by a real standard, code constraint, or authorized decision.

- Existing approved constitution wording and exception rights remain intact unless an authorized change is made.

- No experimental feature or proposed workflow is described as already approved.

- No project-specific transient fact is elevated into a universal engineering principle.

- The named owner/reviewer and approval route are verified; if unknown, the proposal remains a draft.

- The team can explain how the constitution affects planning, analysis, implementation and tests.

- Any automated Spec Kit checks are distinguished from manual review or evidence requirements.

# 1.8 Common mistakes and how to avoid them

| Mistake                                              | Better practice                                                          |
|------------------------------------------------------|--------------------------------------------------------------------------|
| Writing the constitution before repository discovery | Inspect current architecture and standards first.                        |
| Putting every Analyze finding in the constitution    | Only promote durable, cross-feature engineering rules.                   |
| Turning a failed test into a new principle           | Fix and retest the feature; update policy only for recurring guardrails. |
| Overloading with enterprise governance decisions     | Record pending decisions separately and ask authorized owners.           |
| Assuming the AI tool enforces all checks             | Identify manual review and evidence steps explicitly.                    |
| Treating generated text as approved                  | Review, confirm authority, and ratify through the existing process.      |

# 1.9 Pilot evidence and limits

Earlier internal pilot evidence supports repository-level setup, preserving existing architecture, distinguishing reusable rules from feature-specific findings, and integrating Spec Kit into existing Scrum/ADO delivery. The Adaptive Workflow / DocNav integration raised real questions about multi-project repositories, constitution scope, Analyze findings and testing defects.

The later Todo brownfield experiment exposed a separate issue: an experimental amendment to feature 003 can make the spec inconsistent with plan/tasks/contracts until downstream artifacts and approval are handled. That experiment is evidence of a workflow challenge, not approval for a new constitutional rule.

The v0.3 brownfield constitution proposal was reviewed and found unsuitable for ratification without governance decisions. It remains research material; this chapter does not activate or approve it.

# 1.10 Chapter 1 completion criteria

The developer can identify the correct repository boundary, explain the purpose of a constitution, prepare an evidence-based proposal, run the relevant Spec Kit command, distinguish confirmed rules from pending decisions, and route the result for authorized review. Only then should the team treat the constitution as an agreed input for feature work.

# What comes next

Chapter 2 — First-Feature Discovery: select a bounded existing-project story, map relevant source and tests, capture baseline behavior, and create the first feature specification without reverse-specifying the entire repository.

# Source basis

Internal project references: SpecKit_Manager_Brownfield_Adoption_Demo; SpecKit_Achievements_Context_Summary; SDD_Manager_Review_Fidelity_Theme_v3_Satish_Verified; prior v0.3 constitution review and Todo AMD-001 experiment discussed in this project. The template and checklist in this chapter are recommended guidance, not quotations from an approved enterprise policy.

Chapter 2 \| First-Feature Discovery in an Existing Project

Developer adoption guideline • Brownfield Spec Kit • Practical workflow

Objective: establish just enough verified context for a bounded first feature, without reverse-specifying the entire repository or changing code during discovery.

# 2.1 What first-feature discovery means

Brownfield discovery is a focused investigation of existing behavior, relevant modules, dependencies, tests and project constraints before creating a new feature specification. It is not a mandate to generate specifications for every existing capability. The engineer remains responsible for verifying AI-generated descriptions against code and project documentation.

Start from a real, bounded and approved work item (for example, an Azure DevOps story), not from a general instruction to “understand the whole repository.”

# 2.2 Inputs and prerequisites

- Repository access and a clean working branch or working tree; identify repository/workspace boundary.

- An existing governing constitution, if adopted, and project architecture/coding/testing documentation. Do not silently replace an approved constitution.

- Selected story: objective, acceptance criteria, owner and approval status; link to the actual work item when available.

- Ability to inspect code, configuration, tests, contracts and build instructions; access to relevant technical owners for unknowns.

- Spec Kit commands installed and available in the chosen VS Code/Copilot environment; verify exact command names for that installation.

# 2.3 First choose a bounded pilot feature

| Good first pilot                                     | Defer until the workflow is established                  |
|------------------------------------------------------|----------------------------------------------------------|
| One module or clearly owned service                  | Cross-repository architectural redesign                  |
| Observable behavior and testable acceptance criteria | Unclear business owner or missing acceptance criteria    |
| Existing tests or a practical test strategy          | Major undocumented external dependencies                 |
| Limited, identifiable contracts and consumers        | High-risk migration or security change without approvals |

A story can be small yet high risk. Selection requires engineering judgment; size alone is not an approval criterion.

# 2.4 Read-only discovery sequence

1.  Confirm repository boundary: identify solutions/projects, services, shared libraries and build/test entry points. In a multi-project repository, do not automatically initialize Spec Kit in each individual project.

2.  Identify the story’s entry point: API, UI, job, message handler or workflow activity; trace the existing call path to data storage and external dependencies.

3.  Read only the relevant architecture notes, ADRs, interfaces, configuration and neighboring feature specifications. Distinguish authoritative documents from outdated notes.

4.  Establish current behavior and regression baseline: run applicable existing tests if permitted, and record command, environment, result and date. Do not describe unrun tests as passing.

5.  Identify consumers and impacted contracts: upstream callers, downstream integrations, shared models, database schema, authorization and deployment constraints.

6.  Record gaps and questions with evidence: file paths, method names, test names and document links; ask the owner to resolve material ambiguity.

7.  Confirm discovery findings with a developer/reviewer before writing the feature specification.

# 2.4A Recommended discovery extensions and tools

Tool availability depends on the project language, company approvals and VS Code environment. These are options, not mandatory installations. Prefer approved tooling already used by the team. Discovery evidence must be verified against source code, tests, contracts and work-item records.

## Required foundation (capability, not a particular extension)

VS Code or approved IDE with repository search, Git access and language navigation; access to the source, relevant tests, architecture documentation and the approved work item. Spec Kit/Copilot commands are needed for the later SDD workflow, not for every read-only discovery step.

## Recommended when relevant

- GitHub Copilot Chat — ask scoped, read-only questions about entry points, call paths, existing behavior and likely dependencies; independently verify cited file paths and conclusions.

- Language tooling — C# Dev Kit for .NET, Python/Pylance for Python, or the team’s equivalent language server; use Go to Definition, Find References and symbol search to trace real code.

- GitLens (optional) — inspect blame, commits and history for why behavior changed; history is supporting context, not approval evidence.

- REST Client or Postman (when APIs are involved) — inspect existing requests, responses and schemas; use safe environments and never execute state-changing calls without permission.

- Azure DevOps integration (when ADO is the source of truth) — use an approved VS Code extension or Azure CLI with Azure DevOps extension to read stories, acceptance criteria and links; access and features vary by configuration.

## Optional integrations and constraints

GitHub MCP or Azure DevOps MCP can help retrieve repository/work-item context where explicitly approved and connected. They are not prerequisites; enterprise network policies may block MCP access. Never assume an integration is available.

Do not install all extensions by default. Check organizational security policy, extension publisher, licensing, data-sharing rules, repository access and least-privilege permissions first. Never paste secrets or restricted production data into prompts.

## Developer tool-selection checklist

1\. Confirm the repository language and existing approved IDE/extensions. 2. Use local navigation/search before adding integrations. 3. Connect ADO only if the story or acceptance criteria require it. 4. Use API tools only for integration-related features. 5. Record which tools were actually used and what was verified manually.

Example: for an Adaptive Workflow / DocNav feature, use C# navigation to trace the activity and DI registrations; inspect DocNav API contracts with approved API tools; check the approved ADO story; and use Copilot Chat to summarize evidence. This example describes a suggested workflow, not a claim that every extension was installed during the pilot.

# 2.5 Copy/paste Copilot prompt — discovery only

Run this in VS Code with the target repository open. Adjust the story and scope placeholders. This is a chat prompt, not a built-in Spec Kit command.

> Act as a read-only brownfield discovery assistant.  
> Selected work item: \[ADO ID / approved story link\].  
> Business intent and acceptance criteria: \[paste approved text\].  
> Scope: \[module/service/path\].  
>   
> 1. Inspect the relevant existing code, tests, architecture notes and contracts.  
> 2. Explain the current behavior and execution flow with exact file paths and symbols.  
> 3. Identify dependencies, consumers, likely change points and regression risks.  
> 4. Distinguish verified findings from assumptions and unanswered questions.  
> 5. Recommend a minimal set of files and tests to inspect before specifying.  
> 6. Produce a concise discovery report with evidence references.  
>   
> Do NOT edit files, generate a specification, modify the constitution,  
> run Spec Kit commands, change ADO work items or implement code.  
> Do NOT claim tests were run unless execution results are available.

# 2.6 Discovery output: minimum evidence record

| Field                | What to capture                                                       |
|----------------------|-----------------------------------------------------------------------|
| Story and approval   | Work-item ID/link, agreed scope, owner, approval state                |
| Repository boundary  | Repo root, relevant solution/projects and owning module               |
| Current behavior     | Entry point → relevant components → outputs, with file evidence       |
| Constraints          | Existing constitution, ADRs, API contracts, security and coding rules |
| Dependency map       | Callers, consumers, cross-feature and external-service dependencies   |
| Regression baseline  | Existing tests, exact commands, results or “not run”                  |
| Open questions       | Unknowns, decision owner, effect on specification                     |
| Discovery conclusion | Ready for /speckit.specify, or blocked with reason                    |

# 2.7 Move from discovery to the first feature

Once the scope and authoritative requirements are clear, use the project’s installed Spec Kit workflow. The following sequence is a recommended starting pattern, not a guarantee that every environment exposes identical command aliases:

8.  /speckit.specify — write the selected feature’s intended behavior and acceptance criteria from the approved story and verified discovery; do not convert speculation into requirements.

9.  /speckit.clarify — resolve ambiguous or conflicting behavior with the relevant owner; record decisions.

10. /speckit.plan — design the change within existing architecture, contracts and constitution.

11. /speckit.tasks — break the agreed plan into reviewable implementation and test tasks.

12. /speckit.analyze — inspect cross-artifact consistency and constitutional alignment; resolve material findings before implementation.

13. /speckit.implement — implement and test under the team’s usual review/approval controls; review generated code and verify regression behavior.

In some installations command names are different or steps are wrapped in custom skills. Inspect the installed commands before execution. Analyze is a consistency aid, not an approval authority or substitute for code review and testing.

# 2.8 First-feature guardrails and decision points

- Do not regenerate the entire repository as specifications merely to start one bounded story. Expand discovery when risk or dependencies demand it.

- Preserve the existing constitution and approved architecture. Propose governance changes separately; obtain authorization before activation.

- Keep approved requirements separate from experiments. An edited spec file does not establish business approval.

- If the story overlaps an existing feature, explicitly compare in-place amendment, addendum and separate change specification; do not silently choose one as enterprise policy.

- If Analyze finds a feature-specific defect or missing acceptance criterion, resolve it in feature artifacts; amend the constitution only for durable, reusable engineering rules.

- For high-risk changes or unclear owners, stop and escalate through the existing project approval process.

# 2.9 Example: existing Adaptive Workflow / DocNav pilot

The earlier brownfield pilot added a DocNav job-submission activity within an existing Durable Functions workflow framework. Discovery needed to locate the orchestration entry path, activity registration, document source, external API contract, authentication/configuration and relevant test boundaries. The feature was scoped to the reference integration, not to implementing Commitment Typing business rules. This illustrates why targeted architecture discovery is more useful than attempting to describe every repository feature.

Pilot evidence shows the lifecycle was exercised and integration troubleshooting occurred. It does not prove that every external-service scenario or organization-wide adoption rule was validated.

## 2.4B GitHub community extensions for brownfield discovery

These are real GitHub-hosted community projects, not built-in VS Code extensions or automatically approved FNF tools. Evaluate in a sandbox and confirm current versions, licenses, security, write effects, and compatibility before installation.

1\. BrownKit — Evidence-driven capability discovery, security and QA risk assessment. Repository: https://github.com/MaksimShevtsov/BrownKit . Provides /speckit.brownkit.init, .scan, .discover, .assess, .generate, .finish and optional report/scaffold/gate workflows. Best evaluated when a team needs an evidence-backed capability inventory and risk picture, not just a feature-level code walkthrough. Community extension; not validated in our pilot.

2\. Brownfield Bootstrap (spec-kit-brownfield) — Existing-code scan, constitution/template bootstrap, validation and reverse-migration of existing features. Repository: https://github.com/Quratulain-bilal/spec-kit-brownfield . Commands documented include /speckit.brownfield.scan, .bootstrap, .validate, .migrate. Best evaluated for a repo with little or no SDD documentation. Generated constitution/specifications must be reviewed against real architecture and approved rules. Not validated in our pilot.

3\. Spec Kit Discovery Extension — Community extension for feasibility studies, technology selection, legacy codebase assessment, implementation understanding and proof-of-concept validation. Discover through the official community catalog: https://github.github.io/spec-kit/community/extensions.html . Inspect its current repository and command documentation before use. Not validated in our pilot.

4\. Blueprint Index — Community extension for a living architecture map checked against code and specifications. Catalog: https://github.github.io/spec-kit/community/extensions.html . Useful candidate for ongoing architecture drift in larger repositories; it is not a substitute for initial feature discovery. Not validated in our pilot.

5\. Reverse-engineering agents — Community example referenced in the Brownfield Bootstrap discussion: https://github.com/GeoloeG-IsT/agents-reverse-engineer . Treat as an exploratory third-party alternative; review scope and output before adopting. Not validated in our pilot.

Suggested evaluation sequence (do not run directly against the active enterprise repository): create a disposable branch or clone; capture current behavior and tests; run one discovery approach; review files written and permissions requested; verify every claimed capability against code/tests; compare accuracy, effort and output usefulness with plain Copilot discovery; record findings before recommending adoption.

Selection guide: focused first feature → start with Copilot + language tooling; unknown architecture or undocumented codebase → evaluate Brownfield Bootstrap; broad capability/security/QA inventory → evaluate BrownKit; feasibility/POC questions → evaluate Spec Kit Discovery; ongoing architecture drift → consider Blueprint Index. These are candidate tools, not a mandatory stack.

Official sources: Spec Kit community catalog https://github.github.io/spec-kit/community/extensions.html ; BrownKit repository https://github.com/MaksimShevtsov/BrownKit ; Brownfield Bootstrap repository https://github.com/Quratulain-bilal/spec-kit-brownfield . Verified as public project references on 23 Oct 2026; repository contents and versions may change.

## 2.4C Tool selection, Repomix and token-efficiency guidance

Choose the smallest tool and context scope that answers the current question. The estimates below are qualitative—not measured costs. “Tokens” means text sent to or produced by an AI model; scanning files locally is not necessarily an AI token charge.

| Tool                                           | Best for                                                                                                    | Token usage / cost consideration                                                                   | Practical recommendation                                                                |
|------------------------------------------------|-------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| Copilot + repository search / language tooling | One bounded feature; trace references, call flow and tests                                                  | Low–medium if paths/files are scoped; high if whole repository is attached                         | Best default starting point; verify claims against code                                 |
| Repomix                                        | Pack selected source files into AI-readable context; inspect repository token distribution                  | Local packing does not itself consume LLM tokens; sending a large pack can be very high            | Use include/ignore filters and token-count-tree before sharing context                  |
| Brownfield Bootstrap                           | Discover stack and structure; bootstrap Spec Kit configuration; reverse-engineer existing feature artifacts | Variable; potentially medium–high across scan/bootstrap/migrate; no published comparable benchmark | Good for initial SDD onboarding; scan/validate read-only, bootstrap/migrate write files |
| BrownKit                                       | Evidence-led capability inventory with security/QA risk assessment                                          | Variable; broad discovery and multiple passes can be high; no verified benchmark                   | Good for high-risk legacy systems and architecture/risk inventory                       |
| Spec Kit Discovery Extension                   | Feasibility, technical investigation and proof-of-concept assessment                                        | Variable by investigation scope and repeated model calls                                           | Good for unanswered technical decisions, not automatically needed for every feature     |
| Blueprint Index / Repository Index             | Architecture map or repository overview and drift visibility                                                | Variable; indexing method and refresh scope matter; no verified benchmark                          | Good for large repos and repeated cross-module discovery                                |
| Token Budget / Token Consumption Analyzer      | Reduce context bloat or measure workflow token consumption                                                  | Savings and overhead must be measured; no guaranteed percentage                                    | Good for comparing real tool costs in a controlled pilot                                |

### Repomix — practical usage

Repository: https://github.com/yamadashy/repomix \| Documentation: https://repomix.com/guide/usage

Example (run locally on an approved test repository; verify the installed CLI version and syntax):

npx repomix --token-count-tree

Use Repomix include/ignore options to select only the target module, its interfaces, tests and relevant configuration. Inspect its output before pasting or uploading it to Copilot: exclude secrets, credentials, private keys, large generated files and irrelevant vendor/build folders. Never assume a whole-repository pack will fit the model context window.

### How to compare token usage fairly

1\. Choose one fixed feature and identical source-code baseline for all tools (for example, Todo feature 003 before AMD-001).

2\. Record repository size, included files, context-pack token estimate, model/provider, prompt, number of iterations and output artifacts.

3\. Capture actual input/output token usage from the AI provider or available instrumentation when supported. A Repomix token count is an estimate of pack size, not total session billing.

4\. Compare factual discovery accuracy, missed dependencies, review time, files changed and human corrections alongside tokens—not tokens alone.

5\. Run each approach in an isolated copy; obtain organizational security approval before installing community extensions or sharing source code.

Illustrative example only: If a full repository pack is estimated at 120,000 tokens and a scoped pack at 15,000 tokens, the scoped input is 87.5% smaller. This is arithmetic, not a measured reduction in Copilot billing or end-to-end tool usage.

Decision rule: For a small feature, start with Copilot + targeted code search. For an unknown codebase, trial Repomix token-tree and/or Brownfield Bootstrap scan. For broad security/QA discovery, evaluate BrownKit. For repeated architecture drift, assess a repository/blueprint index. Measure before claiming any tool saves tokens.

Source and status note: Repomix token-count-tree is documented by Repomix; BrownKit and Brownfield Bootstrap capabilities are described in their repositories; community extension listings are not security endorsements. No measured token-cost comparison has yet been performed in our pilot. References: https://repomix.com/guide/usage ; https://github.com/MaksimShevtsov/BrownKit ; https://github.com/Quratulain-bilal/spec-kit-brownfield ; https://github.github.com/spec-kit/community/extensions.html

## 2.4D Optional GitHub community approaches — concise project and feature context

Purpose: Share alternative ways teams can preserve relevant project knowledge between features. These are optional examples for evaluation, not required steps or approved enterprise standards.

### Example 1 — Persistent repository instructions (community brownfield guidance)

A community guide on adapting existing projects suggests optionally keeping durable repository facts in .github/copilot-instructions.md, AGENTS.md or another agent-specific instructions file. Capture stable conventions, architecture boundaries and test commands; avoid copying the entire codebase. Source: https://github.com/jbarbosa26/SpecKit/blob/main/docs/04-adapting-existing-projects.md

### Example 2 — Evidence-qualified repository context (community preset)

The Spec Kit community lists a “Verified Codebase Context” preset for generating evidence-qualified repository context and reusing it during planning, tasks, analysis and implementation. This is a candidate to evaluate, not evidence that our team has tested it. Source: https://github.github.com/spec-kit/community/presets.html

### Example 3 — Focused reference loading (community extension)

The community “Spec Reference Loader” extension loads only documents named in the feature specification’s References section. This illustrates a selective-context approach: a feature points to relevant architecture/API notes rather than pasting every document into each prompt. Source: https://github.github.com/spec-kit/community/extensions.html

### Example 4 — Brownfield feature walkthroughs (community examples)

Officially indexed community walkthroughs show adding bounded features to large existing .NET and Java repositories without first reverse-specifying the entire application. They demonstrate scoped adoption; they do not prove that a particular concise-report format is mandatory or used by every project. Source: https://github.com/github/spec-kit/blob/main/docs/community/walkthroughs.md

### Suggested lightweight practice for our teams (proposed, not a verified community standard)

Keep one short project-context note for durable facts and one concise feature-context note per significant feature. Link to source files and decisions rather than duplicating them. Update the note when architecture, contracts or relevant behavior changes.

PROJECT-CONTEXT.md — suggested contents: repository purpose; module map; key execution paths; shared contracts; build/test commands; architecture decisions; known constraints; links to authoritative sources.

FEATURE-CONTEXT.md — suggested contents: feature/ADO reference and approval status; existing behavior; entry points and file paths; impacted APIs/data; dependencies; test baseline; open questions; links to spec.md, plan.md and code.

Example — Todo pagination: FEATURE-CONTEXT.md could link to feature 003, note the current ten-item pagination baseline, list the affected route/template/tests and the dependency from feature 004, and explicitly mark AMD-001 as an unapproved experiment. It should not present the experimental 10/20/50 selector as delivered functionality.

### Token usage and freshness

A concise context note may reduce repeated broad repository reading, but savings are not guaranteed. Measure input/output tokens and number of tool calls in comparable runs. Repomix can estimate the size of a packed file set; that estimate is not the total token cost of an agent workflow. Prefer links and focused file selection; periodically verify notes against current code and approved decisions.

Decision rule: use direct code search for a small change; add a concise feature note when context must be reused; use a repository index or packing tool when broader discovery is justified. Never treat an AI-generated context summary as an approved requirement or substitute for code/test verification.

# 2.10 Completion checklist — Chapter 2

☐ Story scope and approval source identified

☐ Repository and relevant project boundaries confirmed

☐ Current execution path documented with evidence

☐ Impacted interfaces, dependencies and regression risks listed

☐ Baseline test status recorded accurately

☐ Unknowns assigned to decision owners

☐ Reviewer agrees discovery is sufficient to start /speckit.specify

# 2.11 What moves to Chapter 3

Chapter 3 covers subsequent features: reusing verified repository context, avoiding repeated discovery, keeping shared guidance current, and deciding when a new story needs a fresh feature specification versus an update to existing feature artifacts.

# Chapter 2 source and status note

This chapter combines practical guidance with lessons from the Todo and Adaptive Workflow/DocNav pilots. It is a proposed developer adoption guideline, not a ratified enterprise policy. Project-specific requirements, approvals and actual tool behavior take precedence.

Chapter 3 \| Subsequent Features in an Existing Project

Purpose: help developers start feature 2 and beyond using established repository context, without blindly copying old assumptions or repeating a full discovery exercise.

Audience: developers, technical leads and reviewers working within an existing Scrum / Azure DevOps delivery process.

# 3.1 What changes after the first feature?

The repository constitution, architectural map, coding/testing conventions and known integration points should already exist. A subsequent feature reuses this verified baseline, then investigates only what is new or affected. Each story still needs its own agreed requirements, impact assessment, implementation plan, tests and review evidence.

Important distinction: a genuinely new feature is the focus here. Changing already approved or implemented behavior, such as the experimental Todo pagination amendment AMD-001, belongs to Chapter 4 (Specification Evolution).

# 3.2 Reusable inputs — and what must be rechecked

| Reuse as starting point                  | Recheck for this story                                                     |
|------------------------------------------|----------------------------------------------------------------------------|
| Repository/workspace constitution        | Is it current, approved and applicable to this change?                     |
| Architecture / module map                | Which modules, services, data paths and external contracts are touched?    |
| Existing specs, plans and decisions      | Are referenced behaviors still implemented and approved?                   |
| Build, test and local-run instructions   | Do they still work on this branch? Which relevant tests cover this change? |
| Approved work item / acceptance criteria | Is scope clear, current and linked to the right owner?                     |
| Existing Copilot instructions or skills  | Are they accurate for the current repository and authorized for use?       |

Do not assume a previous AI-generated artifact is authoritative merely because it exists. Cross-check important claims against code, tests, current contracts and approved requirements.

# 3.3 Practical workflow for feature 2 onward

14. Select the next approved or explicitly experimental story. Record its source (e.g., ADO ID), owner, acceptance criteria and whether it adds new behavior or modifies existing behavior.

15. Read the active constitution and the relevant existing specifications. Reuse the first-feature discovery record as a navigation aid, not as proof of current behavior.

16. Perform focused, read-only impact discovery: affected modules, entry points, contracts, data models, dependencies, tests, and any overlapping in-flight work.

17. Confirm feature boundary. For a new independently scoped story, create its own feature specification; if it changes an existing feature, use the Chapter 4 decision process before editing an approved spec.

18. Run /speckit.specify for the agreed new feature; review and correct the output against the actual story and code. Run /speckit.clarify where ambiguities remain.

19. Run /speckit.plan, then /speckit.tasks. Check that the design respects the current architecture and that tasks include necessary tests and contract changes.

20. Run /speckit.analyze (or the configured equivalent review) after tasks exist. Resolve material findings with the responsible owner; record unresolved items and approvals rather than silently overriding rules.

21. Run /speckit.implement only after applicable checks and approvals. Review diffs, run relevant unit/integration/regression tests, and use the normal PR/QA process.

22. Capture actual results and update shared discovery notes only when reusable facts changed. Do not amend the constitution for a one-off defect or feature requirement.

Command names shown are the common Spec Kit workflow used in our pilots; installed slash commands, skill wrappers and automated gates can differ. Check the actual workspace setup before following them.

# 3.4 Copy/paste prompt — focused discovery for the next feature

Use this in VS Code Copilot before /speckit.specify. It is a read-only investigation prompt, not a command to implement.

We are preparing the NEXT feature in an existing repository using Spec Kit.

Do READ-ONLY discovery. Do not edit code, specs, constitution, tasks, or ADO items.

Feature/work item: \[ID and approved description\].

Acceptance criteria: \[paste\].

1\. Read the current constitution and relevant existing specs/architecture notes.

2\. Trace the feature's existing entry points, modules, interfaces, data flow, and tests.

3\. Identify cross-feature dependencies, external contracts, and in-flight overlaps.

4\. Separate VERIFIED repository facts from assumptions and open questions.

5\. State what prior discovery can be reused and what needs revalidation.

6\. Recommend a feature boundary; flag if this is actually a change to an existing approved feature.

7\. Propose regression areas and the minimum decisions needed before /speckit.specify.

Return a concise evidence table with file paths and a list of unanswered questions.

Stop after the discovery report; do not run Spec Kit commands.

# 3.5 How to decide whether to rerun discovery

| Change situation                                      | Recommended discovery depth                                                             |
|-------------------------------------------------------|-----------------------------------------------------------------------------------------|
| New UI behavior in an understood module               | Verify UI route, handler, persistence and related tests; no whole-repo rediscovery.     |
| New external API or integration                       | Trace authentication, contracts, failure handling, configuration and integration tests. |
| Cross-module or data-model change                     | Expand to dependent consumers, migration/compatibility and regression impact.           |
| Architecture or dependency changed since last feature | Refresh affected architecture facts and validate old assumptions.                       |
| Unknown legacy area or insufficient tests             | Perform deeper characterization and seek owner review before implementation.            |

# 3.6 Reusing Copilot instructions, commands and skills

- Keep stable repository guidance (architecture boundaries, coding conventions, test commands) in the team’s chosen version-controlled guidance location, subject to review.

- Use a reusable discovery prompt or skill when the same evidence-gathering steps recur across stories; include read-only boundaries and explicit source verification.

- Keep business requirements and acceptance criteria in feature-level artifacts, not in a global skill or constitution.

- Before reusing a custom skill, confirm what files it reads, what it writes, whether it can invoke external tools, and whether it requires approval.

- Do not assume custom commands synchronize ADO, regenerate downstream artifacts, or enforce approvals unless those behaviors have been tested in this workspace.

# 3.7 Worked example — adding a second feature

Illustrative example only: assume the existing Todo application has an approved, implemented list/pagination feature. A new story requests a distinct due-date indicator on Todo items. Before writing a new spec, inspect the existing Todo model, routes/templates, list queries and tests; identify whether due-date storage or display already exists; check dependencies on the list feature; and confirm acceptance criteria with the story owner.

If the due-date indicator is a new independent story, create a separate feature spec and link impacted existing specs. If the request instead changes the approved pagination behavior, do not call it a new independent feature merely to avoid revising old artifacts; follow Chapter 4.

This example demonstrates a decision process, not evidence that this due-date feature was implemented or approved.

# 3.8 Completion checklist for each subsequent feature

- [ ] Story source, scope and owner recorded; approved vs experimental status explicit.

- [ ] Constitution and reused discovery facts checked for currency.

- [ ] Impacted code, contracts, dependent features and regression tests identified.

- [ ] Correct feature boundary selected; existing-feature changes routed to Chapter 4.

- [ ] Specification, clarification, plan and tasks reviewed for consistency.

- [ ] Material analyze/review findings resolved or explicitly owned.

- [ ] Code, test results, PR review and approvals recorded in existing delivery process.

- [ ] Reusable lessons captured without inflating the constitution.

# 3.9 Known limitations and handoff

The pilot experience supports a repository-level governing constitution, focused discovery and the feature lifecycle. It does not establish that Spec Kit automatically detects every dependency, enforces every task-level gate, or keeps an amended spec synchronized with plans and tasks. Human review remains essential. Chapter 4 will compare living-spec amendments, addenda and separate change specifications for evolving approved behavior.

Chapter status: developer adoption guidance based on prior pilot experience and documented review findings. This chapter is not an approved enterprise policy and does not claim new experiments were run.

CHAPTER 4 \| SPECIFICATION EVOLUTION

Changing an Existing Feature: Living Spec or Separate Change Spec?

Audience: developers, technical leads and reviewers • Status: adoption guidance, not ratified enterprise policy

Goal: choose a traceable, reviewable way to change behavior already described by an existing specification—without treating a draft or AI-generated update as business approval.

# 4.1 What is specification evolution?

In a brownfield repository, a delivered feature may already have an approved story, specification, implementation, tests and dependent features. When behavior changes, developers must preserve the original decision trail while expressing the new intended behavior. A specification edit is a documentation change—not proof of approval, implementation or test completion.

Example used in this chapter: Todo feature 003 currently uses 10-item pagination. AMD-001 proposes a page-size selector (10, 20 or 50; default 10). This is an experiment without a recorded approval or ADO work-item ID. Do not treat AMD-001 as an authorized requirement or completed implementation.

# 4.2 Decide the change boundary before running commands

| Approach                                  | Best-fit situation                                                                                                                | Primary trade-off                                                                                                |
|-------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------|
| Living specification — in-place revision  | Approved feature remains the authoritative description of current behavior; change history and approval are explicitly maintained | Easy current-state reading, but risks obscuring the original approved baseline                                   |
| Living specification — versioned addendum | Keep approved original text intact and attach a clearly dated, approved amendment                                                 | Preserves history, but readers must resolve base spec + amendments                                               |
| Separate change specification             | New approved change story affects a previously delivered feature, or needs independent ownership, testing and traceability        | Clean audit trail, but requires explicit links to the original feature and eventual current-state reconciliation |

No approach is universally mandated by this guide. The project’s approved governance and story ownership determine the final choice. Where ownership or approval is unresolved, keep the change clearly labeled experimental and do not proceed as if it were approved.

# 4.3 Developer workflow — eight steps

## 1. Confirm the source of change

Record the story/defect/change request, requester, intended behavior, approval status and approver if known. If experimental, label it as such; do not fabricate an ADO ID.

## 2. Establish the approved baseline

Read the existing spec, related plan/tasks/contracts, code and tests. Identify the last approved behavior and any unapproved edits already present.

## 3. Map impact

Identify affected requirement IDs, API/UI contracts, persistence or URL semantics, tests, dependent features and downstream documentation. Do not assume only one FR changes.

## 4. Compare documentation strategies

Consider in-place revision, versioned addendum and separate change spec. Record the reason for the chosen approach and who must approve it.

## 5. Draft without silently overwriting approval history

Prepare proposed requirements and acceptance scenarios; preserve traceability to the original spec. Distinguish proposed behavior from implemented behavior.

## 6. Reconcile planning artifacts

After authorization, update or link plan, research, data model, contracts and tasks as applicable. Check that requirement IDs and test scenarios still align.

## 7. Review before implementation

Run /speckit.clarify and /speckit.analyze where supported by the installed workflow; inspect findings manually. Analyze is a consistency aid, not an approval engine.

## 8. Implement, test and close

Only after required approvals, implement and run relevant new and regression tests; review the PR, record outcomes and ensure the final current-state documentation is discoverable.

# 4.4 Command guidance: use commands for the right purpose

| When                             | Suggested action                                                                                                                           | Human checkpoint                                                            |
|----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| Discovery / comparison           | Ask Copilot to inspect existing spec, plan, tasks, contracts, code and tests in read-only mode                                             | Confirm facts and approval baseline                                         |
| Separate change spec selected    | Use /speckit.specify for the new change story; then /speckit.clarify                                                                       | Check new feature boundary and explicit links to original feature           |
| Living spec or addendum selected | Draft the proposed revision/addendum using the project’s approved method; do not assume /speckit.specify safely updates an existing folder | Review diff and preserve prior approved text                                |
| Design and work breakdown        | Use /speckit.plan and /speckit.tasks for the approved change, where appropriate                                                            | Confirm affected contracts, tests and dependencies                          |
| Consistency review               | Use /speckit.analyze or equivalent review                                                                                                  | Resolve material findings; log unresolved decisions                         |
| Delivery                         | Use /speckit.implement only when authorized and ready                                                                                      | Verify actual tests and code review; task checkboxes alone are insufficient |

Command names and behavior can differ with installed Spec Kit version and custom skills. Verify the local commands before following these steps. A new spec folder is not automatically required for every amendment; nor should a living spec be rewritten without an agreed change process.

# 4.5 Copy/paste Copilot prompt — read-only comparison

> Assess a proposed change to an existing brownfield feature. Read-only: do not edit files, execute Spec Kit commands, change code, or update Azure DevOps.
>
> Baseline feature: specs/003-todo-list-ux-improvement. Proposed experiment AMD-001: selectable page sizes 10/20/50, default 10. Treat AMD-001 as unapproved.
>
> 1\. Identify the last approved requirements and separate them from experimental text.
>
> 2\. List impacted requirement IDs, plan/tasks, research, data model, contracts, implementation, tests, and dependent feature 004. Mark each impact as verified or inferred.
>
> 3\. Compare three approaches: in-place living-spec revision, versioned addendum, and separate change specification.
>
> 4\. For each, show traceability, approval-history preservation, reviewer effort, and risks.
>
> 5\. Recommend a candidate for discussion, but do not declare enterprise policy or approval.
>
> 6\. Produce a short decision record with unresolved questions and next validation steps. Stop after analysis.

# 4.6 AMD-001: evidence versus unanswered questions

| What we can say                                                                            | What we cannot yet claim                                                  |
|--------------------------------------------------------------------------------------------|---------------------------------------------------------------------------|
| A proposed change was added to feature 003 spec.md in the living-spec experiment.          | That the change is approved or represents the final business requirement. |
| Existing baseline tests (27) passed during the spec-only experiment.                       | That selectable page sizes were implemented or tested.                    |
| Plan, tasks and other artifacts require impact review; feature 004 depends on feature 003. | That all dependencies are fully reconciled or regression risk eliminated. |
| The constitution review surfaced approval/traceability concerns.                           | That a particular specification-evolution policy has been ratified.       |

# 4.7 Acceptance checklist before closing a change

- Original approved behavior and proposed change are distinguishable.

- Story/source, owner, decision status and any required approval are recorded.

- Chosen documentation strategy and links to prior requirements are recorded.

- Impacted specs, plan, tasks, contracts, dependencies and tests are reconciled.

- Material analyze/reviewer findings have a resolution or named decision owner.

- New behavior and regression tests are executed and results captured (not assumed).

- PR/review evidence is available and the discoverable current-state specification is clear.

# 4.8 What to report to the adoption owner

Record the time and manual effort for each strategy, artifacts touched, traceability clarity, conflicts found, regression evidence and unresolved governance decisions. Compare strategies on the same change before recommending an enterprise default. The AMD-001 exercise currently supports a limitation finding, not a final winner.

Chapter status: guidance based on the Todo feature 003 / AMD-001 experiment and the reported constitution review. The separate-change-spec comparison and end-to-end behavior validation remain pending. Chapter 5 will cover cross-feature dependencies in more depth.

Chapter 5 \| Cross-Feature Dependencies

Developer guidance • Brownfield SDD adoption • Working guidance, not an approved enterprise policy

# 5.1 Why cross-feature dependency analysis matters

In an existing system, a small change may affect other features through shared code, routes, data, APIs, configuration, workflows, or tests. A feature specification alone cannot prove that those relationships are safe. Before implementation, developers should identify likely consumers and owners, verify impacts with evidence, and record unresolved questions.

Goal: assess the impact of the selected change, not reverse-document the entire repository.

# 5.2 What counts as a dependency?

| Dependency type        | What to inspect                                                | Typical impact                                        |
|------------------------|----------------------------------------------------------------|-------------------------------------------------------|
| Feature / behavior     | Existing specifications, acceptance criteria, routes, UI flows | One feature changes behavior expected by another      |
| Shared code            | Services, libraries, helpers, interfaces, dependency injection | Multiple callers rely on a changed method or contract |
| API / events           | OpenAPI, request/response models, event schemas, versioning    | Consumers may break when payloads change              |
| Data / persistence     | Tables, migrations, indexes, queries, data ownership           | Existing data or queries need compatibility checks    |
| Workflow / integration | Activity registry, orchestrators, retries, external clients    | Downstream steps or external services are affected    |
| Quality / operations   | Unit/integration tests, observability, config, deployment      | Regression coverage or rollout steps need updating    |

# 5.3 Read-only impact assessment: practical sequence

23. Start with the approved story or change request. Record its ID, owner, intended behavior, acceptance criteria and current approval status. An experiment without approval remains an experiment.

24. Locate the primary entry points and touched modules. Follow callers and callees; identify shared interfaces, endpoints, schemas and persistence changes.

25. Inspect existing specifications and implementation artifacts for related features. Check references in plans, contracts, data models and tasks; do not assume generated artifacts are current.

26. Identify direct and indirect consumers, including other modules, teams, integrations and in-flight features. Ask an owner when ownership or usage cannot be established from the repository.

27. Locate baseline and regression tests. Record what is covered, what is not covered, and whether test results are observed or only expected.

28. Classify each impact as confirmed, likely/unverified, or no impact with evidence. Resolve material unknowns before implementation.

29. Update the selected feature’s spec/plan/tasks and related artifacts through the agreed review and approval process. Do not silently rewrite another feature’s approved baseline.

# 5.4 Minimal dependency-impact record

| Field             | Required entry                                                         |
|-------------------|------------------------------------------------------------------------|
| Change reference  | ADO/story ID or clearly labeled experimental ID; approval status       |
| Affected area     | Feature IDs, repository paths, module/service names                    |
| Dependency        | Consumer, shared contract, schema, workflow or behavior                |
| Evidence          | File/path and symbol, specification clause, test or owner confirmation |
| Impact confidence | Confirmed / likely (unverified) / none found                           |
| Required action   | Change, compatibility measure, test, consultation or no action         |
| Owner / decision  | Responsible reviewer and open approval/decision                        |
| Verification      | Test name/result, contract check or explicit not-yet-tested status     |

# 5.5 Copy/paste Copilot prompt — dependency discovery (read-only)

Use in VS Code with the relevant feature and repository open. Replace bracketed placeholders.

PROMPT START  
Review cross-feature dependencies for \[FEATURE / CHANGE ID\] in this existing repository. Read only: do not edit files, run Spec Kit implementation commands, change tests, or interact with Azure DevOps.  
1. Summarize the requested behavior and distinguish approved requirements from experimental proposals.  
2. Find entry points, touched code, callers, shared interfaces, API/event contracts, data models, configuration and external integrations.  
3. Identify related feature specifications, plans, tasks and any in-flight dependent work.  
4. Produce a dependency matrix: affected feature/module, exact evidence path/symbol, expected impact, confidence (confirmed / likely / unknown), owner, needed action and regression tests.  
5. Separate observed facts from hypotheses. Never claim a test ran unless there is execution evidence.  
6. Identify missing evidence and questions requiring an authorized decision.  
7. Recommend the smallest safe review/test scope, including broader regression where justified.  
Stop after the read-only assessment.  
PROMPT END

# 5.6 Example from our Todo experiment: feature 003 and feature 004

Observed pilot context: feature 003 contains approved pagination behavior; experimental AMD-001 proposes selectable page sizes of 10, 20 and 50, defaulting to 10. Feature 004 (due-date tracking) depends on feature 003. The amendment was made to spec.md only and was not an approved implementation. Existing 27 baseline tests passed; that does not validate the new page-size behavior.

| Check                  | Evidence / question                                                                                                       | Status                                                |
|------------------------|---------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------|
| Feature 003 behavior   | Confirm which pagination requirements AMD-001 changes and whether URL/query behavior affects navigation                   | Impact identified; reconciliation pending             |
| Feature 004 dependency | Inspect feature 004 references to feature 003 and any assumptions about page size                                         | Dependency known; actual behavioral impact unverified |
| Contracts and tasks    | Compare 003 spec with plan, research, contracts, data model and tasks                                                     | Inconsistencies identified; not yet reconciled        |
| Regression             | Keep existing pagination tests; design new tests for 10/20/50, invalid values, page reset, empty list and URL persistence | New behavior not implemented/tested                   |
| Approval               | Do not present AMD-001 as approved or Done without authorized requirement decision                                        | Unapproved experiment                                 |

Developer takeaway: a passing baseline suite does not mean the amended requirement is implemented, approved or safe for dependent features.

# 5.7 Example from Adaptive Workflow / DocNav

In the brownfield DocNav integration, a new workflow activity had to respect existing orchestration boundaries, activity registration, shared workflow input, correlation context and external API behavior. This illustrates why dependency analysis should inspect framework interfaces and integrations before changing an activity. The example is not evidence that all possible downstream consumers were exhaustively assessed.

# 5.8 How Spec Kit fits into dependency review

| Stage                                 | Developer action                                                                                         |
|---------------------------------------|----------------------------------------------------------------------------------------------------------|
| Before /speckit.specify               | Identify existing behavior and related features; clarify the scope of the new change.                    |
| /speckit.specify and /speckit.clarify | State external behavior, constraints, dependencies and unresolved questions.                             |
| /speckit.plan                         | Document touched modules, interface compatibility, migration and regression strategy.                    |
| /speckit.tasks                        | Create explicit cross-feature checks, coordination and test tasks.                                       |
| /speckit.analyze                      | Use as a consistency check across available artifacts; supplement with code review and owner validation. |
| /speckit.implement                    | Implement only within approved scope; execute tests and record actual outcomes.                          |

Important limitation: Spec Kit analysis is not an exhaustive dependency graph, automated approval, or proof of regression safety. It cannot replace code search, architecture knowledge, consumer confirmation or test execution.

# 5.9 Risk-based regression and change control

- Run the relevant existing tests before modifying code where feasible; preserve results as a baseline.

- Add or update tests for changed behavior, boundary conditions and affected consumers. Include integration/contract tests when interfaces change.

- Expand to the full suite or wider integration environment when shared components, data schemas or broad contracts justify it; follow the project’s existing testing policy.

- If a dependency belongs to another team, record its owner and coordinate before changing a shared contract.

- Treat missing test coverage and unverified downstream assumptions as risks, not as passing checks.

- Use existing ADO/PR approval paths. A generated document or Copilot recommendation is not business approval.

# 5.10 Developer completion checklist

☐ The requested change and approval status are clear.

☐ Direct and important indirect dependencies are listed with evidence.

☐ Related feature specs and in-flight work have been checked.

☐ Shared contracts, data changes and external consumers have been reviewed.

☐ Open questions have an owner and decision path.

☐ The plan and tasks include dependency and regression work.

☐ Tests were executed or clearly marked not executed; results are recorded.

☐ Affected reviewers have acknowledged material cross-feature changes.

# 5.11 Adoption findings and handoff to Chapter 6

Recommended adoption practice: maintain a lightweight impact matrix per material change, rather than forcing developers to produce a full dependency catalog for every story. Reuse repository documentation and previous feature evidence, but revalidate anything touched by the current change. The degree of analysis should reflect risk and existing project policy; enterprise-wide thresholds and approvers are not decided by this guide.

Chapter 6 will address large and multi-project repositories: repository boundaries, context selection, shared governance, performance and coordination across teams.

Source/status note: This chapter combines developer guidance with pilot facts discussed in Chapters 1–4. The AMD-001 example remains an unapproved experiment; statements about new behavior are not test results. This chapter is adoption guidance, not ratified policy.

Chapter 6 \| Large and Multi-Project Repositories

Developer guidance • Brownfield SDD adoption • Working guidance, not an approved enterprise policy

# 6.1 Objective and boundary

Help a development team use Spec Kit safely in a large existing repository containing several applications, services, libraries, or independently owned modules. Select a clear workspace boundary, give Copilot focused context, and preserve existing architecture, approvals and tests. Do not generate specifications for every project as a prerequisite to the first feature.

This chapter is recommended operating guidance derived from pilot experience; the correct boundary and ownership model must be confirmed for each repository.

# 6.2 Repository, project, and feature are different levels

- Repository/workspace: the source-control and Spec Kit working boundary; it may contain many projects.

- Project/module: an application, library, service or deployable component within that workspace (for example, multiple .csproj files).

- Feature/change: one bounded business requirement or change, with its own specification and implementation artifacts.

- Team/ownership boundary: who can approve changes, shared contracts, and exceptions; this does not always match the folder structure.

Default starting point: initialize Spec Kit once for a shared repository/workspace, not once per .csproj. If teams use truly independent repositories or separately governed workspaces, assess each boundary individually. Do not assume one constitution automatically governs unrelated repositories.

# 6.3 Before initialization: a 30–60 minute repository map

30. Identify repository root, solution files, build entry points, deployment units and major folders.

31. Locate architecture notes, ADRs, coding standards, security rules, API schemas, data contracts and CI pipelines.

32. List module ownership and identify shared libraries, shared databases, event contracts and external services.

33. Confirm where current business requirements and approvals live (for example, ADO stories, PRs, architecture decisions).

34. Identify the smallest suitable first feature and its likely touch points; record uncertain ownership or missing documentation.

35. Agree where Spec Kit files and feature specs will live, who reviews them, and how generated changes enter normal PR review.

Output: a short repository map and a scoped feature-context note, not a comprehensive reverse-engineered design document.

**Discovery tooling:** Apply the Chapter 2 §2.4A tool-selection guidance per language/module. A large repository does not require installing every extension or connecting every service.

# 6.4 Selecting the Spec Kit workspace boundary

36. If one repository has several related projects and a common engineering process, start at the repository root and use one shared governing constitution.

37. If a monorepo contains independently operated domains, keep the shared rules minimal; capture domain-specific guidance in relevant project documentation or instructions. Escalate any proposal for separate governing constitutions to the repository owners before changing the structure.

38. If services are in separate repositories, treat each repository as its own adoption decision; link dependencies across repos rather than pretending one workspace contains all source.

39. Do not reinitialize Spec Kit inside each project just because the solution has multiple project files. Verify actual folder conventions and command behavior in the installed Spec Kit version before changing anything.

# 6.5 Context strategy: broad orientation, narrow feature scope

Large repositories can exceed practical context limits. Instead of feeding Copilot every file, use a staged discovery approach:

40. Orientation: provide repository map, active constitution, approved architecture references and the selected ADO story.

41. Feature slice: identify relevant entry points, interfaces, data models, tests and known consumers.

42. Dependency expansion: follow only the shared modules and downstream services implicated by evidence.

43. Validation: confirm findings by opening source files, checking references, running available tests and asking the responsible owners.

Maintain a concise feature-context note: purpose, code paths, key interfaces, constraints, dependencies, tests, unknowns, and source links. Mark facts as verified or inferred. Never treat an AI-generated repository summary as an authoritative architecture decision.

# 6.6 Practical command sequence for a selected feature

The actual command aliases depend on the installed Spec Kit version and local customization. A common sequence is:

44. Confirm repository boundary and review the active constitution; use /speckit.constitution only when creating or formally amending agreed reusable rules.

45. Use a read-only Copilot discovery prompt to map the chosen feature and its dependencies.

46. Run /speckit.specify for the approved feature scope, then /speckit.clarify to resolve material ambiguities.

47. Run /speckit.plan and review the proposed changes against architecture, shared interfaces and module owners.

48. Run /speckit.tasks; ensure tasks include tests, integration checks and any cross-team coordination.

49. Run /speckit.analyze (or an equivalent structured consistency review) and resolve important findings before implementation.

50. Run /speckit.implement only after required approvals; review diffs, execute relevant tests and follow normal PR/ADO processes.

Avoid repeatedly running constitution generation for each feature. Constitution rules should change only when a durable repository-wide rule is intentionally approved.

# 6.7 Copy/paste Copilot prompt — multi-project discovery (read-only)

PROMPT START  
We are adopting Spec Kit for one change in an existing multi-project repository. This is READ-ONLY discovery: do not edit files, initialize tools, run implementation commands, or change Azure DevOps.  
Selected story: \[ADO ID / approved change description\].  
1. Map repository root, solution/projects, deployable services, shared libraries and test projects. Cite exact paths.  
2. Identify the selected feature’s entry points, code paths, contracts, storage, configuration, external services and likely consumers.  
3. Read relevant existing architecture/ADRs, the active constitution, and feature specs; distinguish approved sources from drafts.  
4. Classify each dependency as confirmed (with file evidence), possible/unverified, or out of scope.  
5. List existing tests, missing regression coverage, module owners to consult and questions requiring decisions.  
6. Propose the smallest set of files/docs to give Spec Kit as context; do not scan unrelated modules without a reason.  
7. Return a concise repository map, feature-context note, and open-questions table. Do not claim approval or test execution.  
PROMPT END

# 6.8 Practical example: Adaptive Workflow and DocNav

The Adaptive Workflow Engine is a reusable Durable Functions orchestration framework. Commitment Typing is a consuming use case, and DocNav is a separate external service/repository. For the DocNav job-submission reference work, the feature belonged in the relevant workflow activity/integration layer; the orchestrator architecture and Commitment Typing business logic were not to be redesigned.

Developer lesson: when one solution contains several projects, use the existing activity registry, service abstractions, configuration and contracts to find the correct integration point. Map external-service ownership separately from local source-code ownership. The example illustrates scoped discovery; it is not evidence that all monorepo layouts should use the same conventions.

# 6.9 Common pitfalls and decisions

- Pitfall: running Spec Kit separately in every project. Response: agree the repository/workspace boundary first.

- Pitfall: supplying the whole monorepo to Copilot. Response: start with a repository map and expand context only as dependencies require.

- Pitfall: treating one feature spec as architecture for all modules. Response: keep reusable constraints in architecture/constitution and feature behavior in its spec.

- Pitfall: changing a shared contract without involving consumers. Response: record owner, impact, versioning and regression obligations.

- Pitfall: assuming /speckit.analyze proves runtime compatibility. Response: still perform build, tests, contract checks and human review.

- Open policy questions: independent governance for monorepo domains, ownership of shared rules, mandatory test scope and cross-team approvals. Resolve with authorized owners; do not invent enterprise policy.

# 6.10 Developer readiness and completion checklist

☐ Repository/workspace boundary is agreed and documented.

☐ Existing architecture and constitution have been reviewed, not silently overwritten.

☐ Selected feature has an approved source and bounded scope.

☐ Relevant projects, shared components and external dependencies are mapped.

☐ Feature context is concise, evidence-backed and distinguishes assumptions.

☐ Module and contract owners have been identified for material changes.

☐ Plan/tasks include appropriate build, test and regression work.

☐ Analyze findings and unresolved decisions are recorded with owners.

☐ Code changes follow existing PR, ADO and approval processes.

# 6.11 Chapter outcome and transition

Recommended practice: one intentional workspace boundary, one shared baseline of durable rules where appropriate, and focused feature-by-feature discovery. Large-repository adoption succeeds through scoping and ownership, not through generating a complete specification of every module.

Pilot evidence: the multi-project setup and Adaptive Workflow / DocNav integration informed this guidance. The detailed repository-specific conventions and governance decisions must be validated by the adopting team. Chapters 1–6 together form developer guidance, not an enterprise-ratified standard.

Appendix A — Pilot Findings, Limitations and Validation

Evidence classification: Verified = supported by recorded execution or test; Observed = documented during a pilot/review but not independently re-tested here; Proposed = recommended practice not yet established as policy; Pending = needs further experiment. This appendix is not an approval record.

| Area                                  | Evidence / current understanding                                                                                                                                                                           | Status                   |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| Greenfield Todo lifecycle             | Earlier .NET 8 / React / SQL Server Todo sample exercised Spec Kit lifecycle. This supports command familiarity, not enterprise-scale productivity claims.                                                 | Observed                 |
| Brownfield Adaptive Workflow / DocNav | Existing Durable Functions architecture was extended with a DocNav job-submission activity; pilot notes record lifecycle, build and integration troubleshooting. Full enterprise rollout not demonstrated. | Observed                 |
| Multi-project workspace               | Pilot raised questions on workspace boundary and governing constitution. One workspace-level constitution is a practical default, not a universal rule.                                                    | Proposed                 |
| Todo feature 003 baseline             | Pagination feature has an existing approved story; 27 existing tests passed in the recorded experiment. Those tests do not verify the new page-size behavior.                                              | Verified (baseline only) |
| AMD-001 living-spec amendment         | Feature 003 spec was experimentally edited to allow 10/20/50 page sizes; amendment has no approved ADO story/approver and downstream artifacts remain inconsistent.                                        | Observed / unapproved    |
| Living spec vs separate change spec   | Both approaches remain candidates; no completed head-to-head test with matched baseline is documented.                                                                                                     | Pending                  |
| Constitution v0.3 review              | Read-only architecture review identified four blockers and other issues; proposal is not ratified and is not an enterprise standard.                                                                       | Observed / draft         |
| Dependency and regression safeguards  | Focused dependency review, approval traceability, baseline/new tests and PR review are recommended. Automated completeness is not established.                                                             | Proposed                 |

# What teams must not infer

- A generated or amended specification is not automatically approved.

- Passing existing tests is not evidence that a newly proposed feature works.

- /speckit.analyze does not replace reviewer judgment, build validation, integration tests or regression tests.

- A draft constitution or experimental rule must not be presented as ratified enterprise governance.

- Pilot outcomes do not establish a quantified time saving or universal recommendation for all project sizes.

# Recommended next validation

51. Run a controlled comparison of living-spec updates, addenda and separate change specifications using the same approved baseline and requirement.

52. For each approach, record affected artifacts, approval traceability, dependencies, reviewer effort, implementation/test impact and unresolved gaps.

53. Pilot one bounded real sprint story with developer and QA participation; record time/effort against the existing team process without claiming gains in advance.

54. Have the relevant project and enterprise architecture owners review the guidance and any proposed governance rules before broad adoption.

# Review and release note

Version 1.0 — Draft for Review. The six instructional chapters are retained from the working master. This edition adds a front-page orientation, quick-start workflow, and evidence/limitations appendix. Reviewers should verify command names against their installed Spec Kit version and align the guidance with local governance, tool configuration, and approved project practices.
