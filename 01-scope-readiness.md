# 01 — Idea Assessment and First-Feature Readiness

**Greenfield path — Step 1 of 7**  
**Next:** [02 — Constitution](02-constitution.md)

## Purpose

Before beginning development with GitHub Spec Kit, agree on **what to build, why it matters, and what the first manageable feature will be**.

A new application may begin with an early idea or with a requirement that is already approved. Choose the appropriate path below rather than repeating work unnecessarily.

## 1. Choose your starting path

### Path A — The idea still needs evaluation

**Use when:** The team has a product idea or problem statement, but the value, scope, or decision to proceed is not yet clear.

**Recommended:** Use the optional **Spec Kit Idea Assessment** extension to investigate and document the idea before beginning feature development.

From the project directory, install the extension:

```bash
specify extension add assess
```

In the AI coding assistant's chat, run the following **one at a time**, reviewing each result before continuing:

```text
/speckit-assess-intake "Describe the business idea or problem" slug=my-idea
/speckit-assess-research slug=my-idea
/speckit-assess-define slug=my-idea
/speckit-assess-shape slug=my-idea
/speckit-assess-decide slug=my-idea
```

Replace the example description and `my-idea` with your own. Check the installed extension's command reference if its syntax differs.

**Expected outcome:** A documented problem, intended users, important assumptions, possible approaches, and a decision to proceed, clarify, or stop. Review the assessment outputs under `.specify/assessments/<idea-slug>/` where supported by your installed version.

If the decision is to proceed, use the assessment summary to define the first feature.

### Path B — The requirement is already approved

**Use when:** The team already has an agreed business requirement, approved feature request, or Azure DevOps (ADO) story.

A separate idea assessment may not be needed. Confirm instead:

1. Who owns the requirement and can answer questions?
2. What business problem will the first feature solve?
3. What is in scope and out of scope?
4. What examples show that the feature works?
5. What dependencies, constraints, or approvals are known?
6. Are the repository, tools, access, and Spec Kit setup ready?

Record unresolved questions and confirm important decisions with the feature owner.

## 2. Choose a small first feature

Do not try to specify the entire application as a single feature. Start with one useful capability that can be developed and tested.

**Example — Todo application**

- **First feature:** Allow a user to create and view tasks.
- **Why it matters:** Users can record their work and see their task list.
- **Acceptance examples:** A valid task can be saved and displayed; an empty or invalid task is handled appropriately.
- **Later features:** Reminders, advanced filters, or other enhancements can follow separately.

## 3. Confirm readiness

Before proceeding, make sure:

- The first feature's purpose, scope, and owner are understood.
- Key assumptions and open questions are recorded.
- The team has the necessary repository and tool access.
- Relevant technology constraints and team standards are known.
- Existing planning, review, testing, and approval practices remain in place.

## 4. Continue to Constitution

When the team can explain **what it is building, why, what the first feature includes, and who owns decisions**, continue to [02 — Constitution](02-constitution.md).

The Constitution records engineering principles and development expectations for the project.

## Key takeaway

**Use Idea Assessment when a new idea needs evaluation. Use the approved requirement when that decision has already been made. Both paths should lead to a clear, manageable first feature before development begins.**

## References

- [GitHub Spec Kit repository](https://github.com/github/spec-kit)
- [Spec Kit Idea Assessment example](https://github.com/github/spec-kit#idea-assessment)

> **Guide note:** Idea Assessment is an optional extension, not a mandatory step. Commands and output locations can vary with the installed Spec Kit version. This guide describes a recommended starting approach; it is not a claim that every step was executed in the original Greenfield pilot.
