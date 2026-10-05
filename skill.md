Client / Postman
    ↓
HttpStartOrchestrator
    ↓
WorkflowDefinitionProvider
    ↓
Resolved workflow snapshot
    ↓
ProcessOrchestrator
    ↓
ActivityRegistry / Durable activity boundary
    ↓
SubmitDocNavJobActivity
    ↓
BlobDocumentSource
    ↓
Azure Blob → PDF stream
    ↓
DocNavJobSubmissionService
    ↓
POST /api/v1/jobs/submit
    ↓
DocNav
    ↓
202 Accepted → requestId + monitorUrl
