Continue Experiment C-A for Feature 003.

The living specification has been updated with AMD-001.

Now execute the downstream Spec Kit workflow:

1. Verify the active feature directory points to 003-todo-list-ux-improvement.
2. Preserve the existing artifacts using a Git checkpoint.
3. Run /speckit.plan to update the technical design.
4. Run /speckit.tasks to update implementation tasks.
5. Run /speckit.analyze to validate consistency.

Requirements:

- Preserve existing functionality and historical decisions.
- Do not lose or overwrite previously completed tasks.
- Incorporate AMD-001 across the relevant artifacts.
- Keep the enhancement marked experimental, not ADO-approved.
- Do not implement application code.
- Do not publish anything to Azure DevOps.
- Stop and request approval before any destructive regeneration.

Finally, report which artifacts were updated, what was preserved, and any manual corrections required.