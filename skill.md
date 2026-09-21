# ADO Import

Import one or more Azure DevOps User Stories into the local repository
for use with the Spec Kit workflow.

## Input

Accept one or more Azure DevOps Work Item IDs separated by spaces.

Examples:

Single User Story:

/ado-import 904676

Multiple User Stories:

/ado-import 904676 904677 904678

Extract all numeric Work Item IDs from the supplied arguments.

If no valid Work Item ID is provided, stop and ask the user to provide
at least one Azure DevOps Work Item ID.

Do not treat multiple IDs as a single value.


## Processing

Process each Work Item ID independently.

For each Work Item ID:

1. Retrieve the work item from Azure DevOps using:

   az boards work-item show --id <WORK_ITEM_ID> \
     --organization "https://dev.azure.com/fnf" \
     --output json

2. Verify that the retrieved work item is a User Story.

3. Extract:

   - System.Id
   - System.Title
   - System.WorkItemType
   - System.State
   - System.Description
   - Microsoft.VSTS.Common.AcceptanceCriteria
   - System.Tags
   - System.IterationPath

4. Preserve the original Description and Acceptance Criteria faithfully.

5. Convert Azure DevOps HTML content into readable Markdown.

6. Do not invent, infer, or add requirements that are not present in
   the Azure DevOps User Story.

7. Determine the destination folder independently for each User Story
   from System.IterationPath.

   Example:

   FNFI-EA\SDD-Experiment\Sprint-3

   becomes:

   ADO/refinement/Sprint-3/

8. Create the destination folder if it does not already exist.

9. Create one Markdown file per User Story using:

   US-<WORK_ITEM_ID>-<sanitized-title>.md

   Example:

   US-904676-Todo-Expiration.md

10. Never combine multiple User Stories into a single Markdown file.

11. Do not automatically run speckit-specify after importing.


## Batch Processing Rules

When multiple Work Item IDs are supplied:

- Process them sequentially.
- Treat every Work Item ID independently.
- One failed Work Item must not stop the remaining imports.
- Do not combine Description or Acceptance Criteria across User Stories.
- Each User Story must retain its own ADO traceability.
- Each User Story must use its own System.IterationPath when determining
  the destination folder.
- Do not assume all supplied User Stories belong to the same sprint.
- Do not overwrite an existing requirement file without informing the user.


## Failure Handling

If an individual Work Item cannot be retrieved:

- Mark that Work Item as failed.
- Record the reason.
- Continue processing the remaining Work Item IDs.

If a Work Item exists but is not a User Story:

- Do not import it as a User Story.
- Mark it as skipped.
- Report its actual Work Item Type.
- Continue processing the remaining IDs.


## Output

For a single import, report:

ADO Import Complete

Work Item: <ID>
Title: <TITLE>
Iteration: <ITERATION>
File: <GENERATED_FILE_PATH>
Description: Found / Not Found
Acceptance Criteria: Found / Not Found


For multiple imports, provide a summary such as:

ADO Import Complete

Work Item | Status | File
904676    | Imported | ADO/refinement/Sprint-3/US-904676-Todo-Expiration.md
904677    | Imported | ADO/refinement/Sprint-3/US-904677-Example.md
904678    | Failed   | Work Item not found
904679    | Skipped  | Work Item Type: Bug

Summary:
2 Imported
1 Failed
1 Skipped