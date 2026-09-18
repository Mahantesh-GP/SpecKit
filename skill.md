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

9. Determine the destination folder from the Azure DevOps
   `System.IterationPath` field.

   Example:

   System.IterationPath:
   FNFI-EA\SDD-Experiment\Sprint-3

   Extract the final segment:

   Sprint-3

10. Store the generated requirement under:

    ADO/refinement/<ITERATION>/

    Example:

    ADO/refinement/Sprint-3/

11. Do not infer the active sprint by examining existing repository folders.

12. The Azure DevOps `System.IterationPath` is the authoritative source
    for determining the destination sprint folder.

13. If the corresponding iteration folder does not exist, create it.

14. Use the iteration name exactly as returned by Azure DevOps.
    Do not rename or normalize the iteration name unless required for
    filesystem compatibility.

15. Create the requirement filename using:

    US-<ID>-<sanitized-title>.md

    Example:

    US-904676-Todo-Expiration.md

16. The resulting path should therefore be:

    ADO/refinement/<ITERATION>/US-<ID>-<sanitized-title>.md



