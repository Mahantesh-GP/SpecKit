constitution 

Update the project constitution for the Adaptive Workflow Engine with these principles:

1. Keep the engine generic, reusable, and configuration-driven. Consumer-specific business logic must not be added to the orchestration framework.

2. Consumer-specific operations and external-service calls belong behind workflow activities/services, using contracts or abstractions where appropriate.

3. Reuse existing framework mechanisms including workflow definitions, ProcessOrchestrator, ActivityRegistry, WorkflowActivities, dependency injection, state/result propagation, and external events rather than duplicating them.

4. Long-running asynchronous operations should use the existing Durable Functions external-event pattern.

5. Framework and reference implementations must support local testing without requiring access to real external services.

6. Follow established exception handling, logging, telemetry, timeout, and retry patterns.

7. Keep framework responsibilities separate from consuming-team responsibilities so consumers can implement their own business rules and service integrations without changing the generic orchestration architecture.




specify


/specify

Create a reusable reference implementation showing how a consuming team can implement an activity that interacts with an external service without coupling that service to the Adaptive Workflow Engine.

The reference should demonstrate:
- resolving and executing a configured workflow activity;
- delegating service-specific work through a contract/abstraction;
- using a placeholder implementation so the flow is locally testable without the real external service;
- returning the activity result to the workflow;
- using the existing external-event mechanism to wait for asynchronous completion and resume the workflow.

Reuse the existing Adaptive Workflow architecture and patterns.

Do not implement Commitment Typing business logic or real DocNav integration. Commitment Typing is only the reference use case; consuming teams will implement their actual service integrations after handover.