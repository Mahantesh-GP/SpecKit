---
name: ado-import
description: Import an Azure DevOps User Story into the repository as a clean SDD requirement document for Spec Kit.
---

# ADO User Story Import

Import an Azure DevOps User Story and prepare it as an input requirement for Spec Kit.

## Input

The user must provide an Azure DevOps Work Item ID.

Example:

Import ADO User Story 904676

## Azure DevOps Configuration

Organization:
https://dev.azure.com/fnf

Project:
FNFI-EA

## Instructions

1. Verify that an Azure DevOps Work Item ID was provided.

2. Fetch the work item using Azure CLI:

   az boards work-item show --id <WORK_ITEM_ID> --organization "https://dev.azure.com/fnf" --output json

3. Do not ask the developer to manually copy content from Azure DevOps.

4. Verify that the returned work item is a User Story.

5. Extract only the SDD-relevant fields:
   - System.Id
   - System.Title
   - System.WorkItemType
   - System.State
   - System.Description
   - Microsoft.VSTS.Common.AcceptanceCriteria
   - System.Tags
   - System.IterationPath

6. Preserve the Description and Acceptance Criteria faithfully.
   Do not invent, rewrite, or add business requirements.

7. Convert HTML contained in Description or Acceptance Criteria into readable Markdown while preserving the meaning.

8. Create a Markdown requirement document using this structure:

   # ADO User Story <ID> - <TITLE>

   ## Traceability

   - ADO Work Item: <ID>
   - Work Item Type: <TYPE>
   - State: <STATE>
   - Iteration: <ITERATION>
   - Tags: <TAGS>

   ## User Story

   <DESCRIPTION>

   ## Acceptance Criteria

   <ACCEPTANCE_CRITERIA>

9. Store the generated requirement under the existing ADO/refinement structure.

10. Use a filename in this format:

    US-<ID>-<sanitized-title>.md

11. Do not modify application source code.

12. Do not run speckit-specify automatically.

13. After creating the file, report:
    - imported ADO Work Item ID
    - title
    - generated requirement file path
    - whether Description was found
    - whether Acceptance Criteria were found

14. Recommend using the generated Markdown file as the authoritative input for the next speckit-specify operation.