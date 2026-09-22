---
name: "ado-toolkit"
description: "Azure DevOps toolkit for Spec Kit workflow. /ado-import pulls User Stories from ADO into the local repo. /ado-clarify-sync posts Spec Kit clarification Q&A back to ADO work item discussions."
---

# ADO Toolkit

Two commands for integrating Azure DevOps with the Spec Kit workflow.

| Command | Purpose |
|---|---|
| `/ado-import <ID> [ID...]` | Pull User Stories from ADO into the local repo |
| `/ado-clarify-sync <ID> [ID...]` | Post Spec Kit clarification Q&A to one or more ADO story discussions |


## Shared: Config Resolution

Both commands use this config resolution. Resolve `ADO_ORG` and
`ADO_PROJECT` before any ADO API call.

### 1. Local repo config file (preferred)

Look for `ADO/ado.config.json` in the repository root.

Expected format:

```json
{
  "organization": "fnf",
  "project": "FNFI-EA"
}
```

If the file exists, read:
- `organization` → `ADO_ORG`
- `project` → `ADO_PROJECT`

### 2. Azure CLI defaults (fallback)

If `ADO/ado.config.json` does not exist, run:

```bash
az devops configure --list
```

Parse the output for `organization` and `project` values.

Strip the full URL prefix if present:

```
https://dev.azure.com/fnf  →  fnf
```

### 3. Prompt the user (last resort)

If neither source provides the values, stop and report:

```
Could not resolve ADO organization and project.

Create ADO/ado.config.json with:
{
  "organization": "<your-org>",
  "project": "<your-project>"
}

Or run:
az devops configure --defaults organization=https://dev.azure.com/<org> project=<project>
```

Do not proceed without both values.


---

## Command: /ado-import

Import one or more Azure DevOps User Stories into the local repository
for use with the Spec Kit workflow.

### Input

Accept one or more Work Item IDs separated by spaces.

```
/ado-import 904676
/ado-import 904676 904677 904678
```

Extract all numeric Work Item IDs from the supplied arguments.
Do not treat multiple IDs as a single value.
If no valid Work Item ID is provided, stop and ask the user for one.

### Processing

Process each Work Item ID independently.

For each Work Item ID:

1. Retrieve the work item:

   ```bash
   az boards work-item show --id <WORK_ITEM_ID> \
     --organization "https://dev.azure.com/<ADO_ORG>" \
     --output json
   ```

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

6. Do not invent, infer, or add requirements not present in the ADO User Story.

7. Determine the destination folder from System.IterationPath:

   ```
   FNFI-EA\SDD-Experiment\Sprint-3  →  ADO/refinement/Sprint-3/
   ```

8. Create the destination folder if it does not already exist.

9. Create one Markdown file per User Story:

   ```
   US-<WORK_ITEM_ID>-<sanitized-title>.md
   ```

   Example: `US-904676-Todo-Expiration.md`

10. Never combine multiple User Stories into a single Markdown file.

11. Do not automatically run speckit-specify after importing.

### Batch Processing Rules

- Process sequentially; treat every Work Item ID independently.
- One failed Work Item must not stop the remaining imports.
- Do not combine Description or Acceptance Criteria across User Stories.
- Each User Story must use its own System.IterationPath for the destination folder.
- Do not assume all supplied User Stories belong to the same sprint.
- Do not overwrite an existing file without informing the user.

### Failure Handling

If a Work Item cannot be retrieved: mark as failed, record the reason, continue.

If a Work Item exists but is not a User Story: mark as skipped, report its
actual Work Item Type, continue.

### Output

Single import:

```
ADO Import Complete

Org      : <ADO_ORG>      (source: config file | az defaults)
Project  : <ADO_PROJECT>  (source: config file | az defaults)
Work Item: <ID>
Title    : <TITLE>
Iteration: <ITERATION>
File     : <GENERATED_FILE_PATH>
Description        : Found / Not Found
Acceptance Criteria: Found / Not Found
```

Multiple imports:

```
ADO Import Complete

Org     : <ADO_ORG>      (source: config file | az defaults)
Project : <ADO_PROJECT>  (source: config file | az defaults)

Work Item | Status   | File
904676    | Imported | ADO/refinement/Sprint-3/US-904676-Todo-Expiration.md
904677    | Imported | ADO/refinement/Sprint-3/US-904677-Example.md
904678    | Failed   | Work Item not found
904679    | Skipped  | Work Item Type: Bug

Summary:
2 Imported
1 Failed
1 Skipped
```


---

## Command: /ado-clarify-sync

Post Spec Kit clarification questions and decisions back to Azure DevOps
work item discussions so the PO and stakeholders can see and respond.

Supports single-story and multi-story (combined feature) scenarios.
When multiple Work Item IDs are provided, the same clarification comment
is posted to every story — this is the correct pattern when two or more
stories were refined together as one feature.

### Input

```
/ado-clarify-sync <ID>
/ado-clarify-sync <ID1> <ID2> [ID3...]
```

Examples:

```
/ado-clarify-sync 904676
/ado-clarify-sync 900328 900332
```

Extract all numeric Work Item IDs from the supplied arguments.
If no valid Work Item ID is provided, stop and ask the user for one.

### Source File Resolution

The clarification content is the SAME for all provided Work Item IDs.
Find it once, then post it to each story.

Search for the clarification source in this order:

**1. Feature-level spec folder (multi-story scenario)**

When multiple IDs are supplied, search the `specs/` folder for a file
containing clarifications that references any of the supplied IDs:

```
specs/**/clarifications.md
specs/**/spec.md
specs/**/*.md
```

Read each candidate and look for a clarifications section with a heading such as:
- `## Clarifications`
- `## SDD Clarifications`
- `## Outstanding Questions`

Use the first file found that contains clarification content.

**2. Individual story file (single-story scenario)**

If no feature-level file is found, search for the imported User Story file:

```
ADO/refinement/*/US-<WORK_ITEM_ID>-*.md
```

Example:

```
ADO/refinement/Sprint-3/US-904676-Todo-Expiration.md
```

Read the file and locate the clarifications section.

**3. No clarifications found**

If neither search finds a clarifications section, stop and report:

```
No clarifications found for Work Item(s) <IDs>.
Run speckit.clarify first to generate clarification questions.
```

### Comment Format

Build the ADO Discussion comment as **HTML** (not Markdown).
The ADO Comments API renders HTML — Markdown syntax will appear as
raw text if posted directly.

Use this HTML structure:

```html
<h1>SDD Clarification</h1>
<p>
  <strong>Source:</strong> Spec Kit<br>
  <strong>ADO Work Item(s):</strong> <WORK_ITEM_ID(s)>
</p>
<hr>

<h2>SDD-Q1 &mdash; RESOLVED</h2>
<p><strong>Topic:</strong> <topic></p>
<p><strong>Question:</strong><br><question></p>
<p><strong>Decision:</strong><br><decision></p>
<hr>

<h2>SDD-Q2 &mdash; OUTSTANDING</h2>
<p><strong>Topic:</strong> <topic></p>
<p><strong>Question:</strong><br><question></p>
<p><strong>Decision:</strong><br>Awaiting business clarification.</p>
<hr>

<p><strong>Summary:</strong> <resolved-count> Resolved | <outstanding-count> Outstanding</p>
```

Rules:
- Include every clarification found in the source file.
- Preserve stable identifiers: SDD-Q1, SDD-Q2, SDD-Q3, etc.
- Do not invent a Decision for OUTSTANDING items.
- Do not modify the ADO Description or Acceptance Criteria.
- The comment body is identical for every Work Item ID in the batch.

### Posting the Comment

Do NOT pass a multiline string directly to `az boards`. Use `az rest`
with a temporary JSON file to avoid truncation.

#### For each Work Item ID, repeat these steps:

**Step 1 — Write comment to temp JSON file:**

```
.ado-comment-temp.json
```

```json
{
  "text": "<escaped-html-body>"
}
```

**Step 2 — Post using az rest:**

```bash
az rest \
  --method POST \
  --uri "https://dev.azure.com/<ADO_ORG>/<ADO_PROJECT>/_apis/wit/workItems/<WORK_ITEM_ID>/comments?api-version=7.1-preview.3" \
  --headers "Content-Type=application/json" \
  --body @.ado-comment-temp.json
```

Capture the full JSON response.

**Step 3 — Verify the posted comment:**

Extract `id` from the POST response, then read it back:

```bash
az rest \
  --method GET \
  --uri "https://dev.azure.com/<ADO_ORG>/<ADO_PROJECT>/_apis/wit/workItems/<WORK_ITEM_ID>/comments/<COMMENT_ID>?api-version=7.1-preview.3"
```

Confirm the returned `text` field contains every expected identifier
(SDD-Q1, SDD-Q2, etc.).

**Step 4 — Clean up:**

Delete `.ado-comment-temp.json` after ALL stories are processed,
or if posting fails. Never leave this file behind.

### Output

Single story:

```
ADO Clarify Sync Complete

Org      : <ADO_ORG>      (source: config file | az defaults)
Project  : <ADO_PROJECT>  (source: config file | az defaults)
Source   : <SOURCE_FILE_PATH>
Work Item: <ID>
Comment ID : <ADO_COMMENT_ID>
Resolved   : <N>
Outstanding: <N>
Status   : SUCCESS / FAILED
```

Multiple stories (same clarification posted to each):

```
ADO Clarify Sync Complete

Org     : <ADO_ORG>      (source: config file | az defaults)
Project : <ADO_PROJECT>  (source: config file | az defaults)
Source  : <SOURCE_FILE_PATH>
Resolved   : <N>
Outstanding: <N>

Work Item | Status  | Comment ID
900328    | SUCCESS | 12345
900332    | SUCCESS | 12346
900333    | FAILED  | az rest error: 404

Summary:
2 SUCCESS
1 FAILED
```

### Failure Handling

- A failed POST to one Work Item must not stop posting to the remaining IDs.
- If `az rest` returns a non-2xx status, mark that Work Item as FAILED and
  record the HTTP status and response body.
- If a Work Item does not exist in ADO, mark it as FAILED and continue.
- If authentication fails on any call, stop all remaining posts and tell
  the user to run `az login` and retry. Do not ask for a PAT.
- Always delete `.ado-comment-temp.json` even when posting fails.
