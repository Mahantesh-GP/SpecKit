# 01 — Start with a Clear Idea

**Greenfield guide · Step 1 of 7**  
**Next:** [02 — Constitution](02-constitution.md)

## What is this step for?

Before using Spec Kit to build a new application, agree on **what to build, why it is needed, and what to build first**.

## 1. Where are you starting?

### A. You have a new idea, but it is not approved yet

Use **Spec Kit Idea Assessment** to explore the idea and decide whether to proceed.

**Install the optional extension** in your project terminal:

```bash
specify extension add assess
```

**Run these in GitHub Copilot Chat**, one at a time. Review each response before moving on:

```text
/speckit-assess-intake "Describe your idea" slug=my-idea
/speckit-assess-research slug=my-idea
/speckit-assess-define slug=my-idea
/speckit-assess-shape slug=my-idea
/speckit-assess-decide slug=my-idea
```

Replace `"Describe your idea"` and `my-idea` with your own details. Check your installed extension's instructions if the commands differ.

**You should finish with:** A clear description of the idea, key questions, and a decision to go ahead, clarify, or stop.

### B. You already have an approved requirement or ADO story

You can **skip Idea Assessment**. Confirm these four things with the feature owner:

- **Why:** What problem are we solving?
- **What:** What should the first feature do?
- **Done:** How will we know it works?
- **Ready:** Do we have the needed access, tools, and approvals?

## 2. Pick one small feature

Start with something useful that can be built and tested on its own.

**Example — Todo app**

- **Build first:** Add a task and show it in a list.
- **Check:** A saved task appears in the list; an empty task is handled correctly.
- **Build later:** Reminders and advanced filters.

## 3. Check before moving on

- [ ] The team agrees on the first feature and its owner.
- [ ] The expected result is clear.
- [ ] Important questions are recorded.
- [ ] Repository and tools are ready.

## 4. Next step

Go to **[02 — Constitution](02-constitution.md)** to record the project's development rules.

**Remember:** Idea Assessment is optional. Use it for ideas that still need evaluation; don't repeat it for requirements that are already approved.

**Reference:** [GitHub Spec Kit](https://github.com/github/spec-kit) · [Idea Assessment](https://github.com/github/spec-kit#idea-assessment)
