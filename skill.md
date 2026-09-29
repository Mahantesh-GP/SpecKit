/speckit.specify

Implement DocNav single-PDF job submission using the attached DocNav Job Submission API documentation as the source of truth for the API contract.

The workflow activity receives a reference to a PDF stored in Azure Blob Storage, retrieves the document, and submits it to DocNav.

On successful submission, preserve the DocNav requestId and relevant response information.

Correlation is critical: multiple workflow/activity executions may submit DocNav requests concurrently. Each returned requestId must remain associated with the exact originating workflow/activity context so that a later DocNav response or result can be correctly matched to its originating execution without cross-correlation.

Handle submission and document-access failures appropriately and do not report failed submissions as successful.

Scope this feature to single-document submission. Do not include batch submission or full status/result processing in this feature.

Use the existing project architecture and contracts where applicable. Do not assume requirements that are not defined in the attached documentation or existing project.