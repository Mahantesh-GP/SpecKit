11. Build the complete Discussion content first as ONE string.

    The string must contain the header and ALL clarification
    questions and decisions.

    Example content:

    [SDD CLARIFICATION]

    SDD-Q1 | RESOLVED
    Topic: Expiration State Representation
    Question: <question>
    Decision: <decision>

    SDD-Q2 | RESOLVED
    Topic: Expiration Period Configuration
    Question: <question>
    Decision: <decision>

    SDD-Q3 | RESOLVED
    Topic: Expiration Check Mechanism
    Question: <question>
    Decision: <decision>

12. Pass the ENTIRE constructed content as a single value to
    Azure CLI --discussion.

    Do NOT execute the command with only the first line/header.

    Use PowerShell variable assignment when multiline content
    is required:

    $discussion = @"
    <COMPLETE_DISCUSSION_CONTENT>
    "@

    az boards work-item update `
      --id <WORK_ITEM_ID> `
      --discussion $discussion `
      --organization "https://dev.azure.com/fnf"

13. Before executing the Azure CLI command, verify that the
    $discussion value contains every SDD-Q identifier that is
    being synchronized.

14. If 3 clarifications were identified, the discussion content
    must contain SDD-Q1, SDD-Q2 and SDD-Q3 before the command
    is executed.

15. Never report synchronization as successful unless the
    complete clarification content was submitted.