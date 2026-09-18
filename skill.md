---
name: ado-clarification-sync
description: Sync Spec Kit clarification questions and decisions to the source Azure DevOps User Story Discussion.
---

# ADO Clarification Sync

## Purpose

Synchronize Spec Kit clarification results with the Discussion section
of the Azure DevOps User Story that originated the specification.

Azure DevOps remains the system of record for business clarification
decisions.

## Azure DevOps Configuration

Organization:
https://dev.azure.com/fnf

Project:
FNFI-EA

## Instructions

1. Identify the current Spec Kit feature.

2. Read its `spec.md`.

3. Determine the source ADO Work Item ID from the specification
   traceability information.

4. If an ADO Work Item ID cannot be determined, stop and report:
   "Source ADO User Story could not be identified."

5. Find clarification questions and their current resolution state.

6. Classify each clarification as:

   RESOLVED
   - A business answer/decision was provided during refinement.

   OUTSTANDING
   - The question requires a business decision that was not available.

7. Assign each clarification a stable identifier:

   SDD-Q1
   SDD-Q2
   SDD-Q3
   ...

8. Prepare one structured Discussion entry containing all
   clarification results.

   Format:

   [SDD CLARIFICATION]

   SDD-Q1 | RESOLVED

   Question:
   <question>

   Decision:
   <answer>

   ---

   SDD-Q2 | OUTSTANDING

   Question:
   <question>

   Decision:
   Awaiting business clarification.

9. Do not invent answers for OUTSTANDING questions.

10. Do not change the User Story Description or Acceptance Criteria.

11. Add the clarification information to the Azure DevOps
    User Story Discussion using Azure CLI:

    az boards work-item update \
      --id <WORK_ITEM_ID> \
      --discussion "<DISCUSSION_CONTENT>" \
      --organization "https://dev.azure.com/fnf"

12. Do not create duplicate clarification entries when the same
    clarification has already been synchronized.

13. After synchronization report:

    - ADO Work Item ID
    - number of resolved clarifications
    - number of outstanding clarifications
    - whether synchronization succeeded