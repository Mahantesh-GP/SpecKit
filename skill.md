Continue Experiment C-A (Living Specification approach).

I have reviewed the proposed revision plan for Feature 003.

Use the following experimental decisions:

1. Supported page sizes: 10, 20, 50; default 10.
2. Preserve the selection through URL/request parameters only. No session, cookies or database persistence.
3. When page size changes, navigate to page 1.
4. Unsupported or invalid page-size values fall back to 10.
5. Show the selector even when the Todo list is empty.
6. Preserve the existing no-JavaScript constraint. Use a dropdown with a submit button.
7. Define SC-007 as: Users can select 10, 20 or 50 items per page, and the displayed list and page count match the selected size.
8. This is an experimental requirement, not an approved ADO story. Do not publish or synchronize anything to ADO.
9. Do not alter the existing checklist statuses. Report them as a separate governance concern before implementation.
10. You may correct the local Spec Kit feature pointer to Feature 003, but do not modify other feature directories.

Now perform only the specification revision phase:

- Update the existing "specs/003-todo-list-ux-improvement/spec.md" using the Living Specification approach.
- Preserve original requirements, clarification history and acceptance scenarios.
- Add a clearly marked experimental amendment AMD-001.
- Add US2, FR-012 through FR-014, and SC-007 as appropriate.
- Maintain traceability to existing FR-003 and dependent requirements.
- Do not modify plan.md, research.md, data-model.md, contracts, quickstart.md, tasks.md, application code or tests.
- Do not run "/speckit.specify", "/speckit.plan", "/speckit.tasks" or "/speckit.implement".
- After editing, show the exact Git diff and summarize what changed, what was preserved, and any unresolved contradictions.

Stop after the specification revision for my review.