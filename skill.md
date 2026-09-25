                 COMMITMENT TYPING
                        │
                        │ Start process
                        ▼
              HttpStartOrchestrator
                        │
                        ▼
              WorkflowDefinitionProvider
                        │
                        ▼
               commitment.base.json
                        │
                        ▼
                ProcessOrchestrator
                        │
                        │ sees:
                        │ activityKey = DocNavRequest
                        ▼
                  ActivityRegistry
                        │
                        ▼
              WorkflowActivities.cs
          (thin Azure Function wrapper)
                        │
                        ▼
              IDocNavRequestActivity
                        │
                        ▼
               DocNavRequestActivity
                │               │
                │ validate      │
                │ map input     │
                ▼               │
          IDocNavRequestService │
                │               │
                ▼               │
       RealDocNavRequestService │
                │
        ┌───────┼─────────┐
        ▼       ▼         ▼
 Access Token  APIM    Document
  Provider     Key      Source
        │       │         │
        └───────┼─────────┘
                ▼
             DocNav API
                │
                │ 202 Accepted
                ▼
        requestId = DOC-789
                │
                ▼
        DocNavSubmissionResult
                │
                ▼
        DocNavRequestActivity
                │
                ▼
        ActivityExecutionResult
                │
                ▼
          ProcessOrchestrator
                │
                ▼
      WAIT: ExternalEvent
         "DocNavCompleted"
                .
                .
                . DocNav processing
                .
                ▼
         DocNav completion
                │
                ▼
      WebSocket/callback handler
                │
                ▼
        Raise Durable Event
                │
                ▼
          ProcessOrchestrator
                │
                ▼
          NEXT WORKFLOW STEP