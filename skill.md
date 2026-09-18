## Azure DevOps Discussion Formatting

Create a readable Markdown-formatted clarification comment.

The final ADO Discussion comment must follow this structure:

# SDD Clarification

**Source:** Spec Kit  
**ADO Work Item:** <WORK_ITEM_ID>

---

## SDD-Q1 — RESOLVED

**Topic:** <topic>

**Question:**  
<question>

**Decision:**  
<decision>

---

## SDD-Q2 — OUTSTANDING

**Topic:** <topic>

**Question:**  
<question>

**Decision:**  
Awaiting business clarification.

---

**Summary:** <resolved-count> Resolved | <outstanding-count> Outstanding

## Posting Rules

1. Include every clarification found in the current specification.

2. Preserve the stable identifiers:
   SDD-Q1, SDD-Q2, SDD-Q3, etc.

3. Do not invent an answer for an OUTSTANDING clarification.

4. Do not modify the ADO User Story Description or Acceptance Criteria.

5. Post the complete formatted content as ONE ADO Discussion comment.

6. Because Azure CLI `--discussion` command-line multiline handling can
   truncate content, do NOT pass a PowerShell multiline string directly
   to `--discussion`.

7. Use the Azure DevOps Work Item Comments API for formatted multiline
   comments.

8. Use the existing Azure CLI authentication/session to call Azure DevOps.
   The developer must not be asked for a PAT.

9. Send the complete comment as JSON with the comment text in the `text`
   property and Markdown formatting enabled.

10. After posting, read the created comment back from Azure DevOps.

11. Verify that the persisted comment contains every expected
    clarification identifier.

    Example:
    SDD-Q1
    SDD-Q2
    SDD-Q3

12. Report SUCCESS only after the persisted ADO comment has been verified.

13. If any expected clarification identifier is missing, report FAILED
    instead of SUCCESS.