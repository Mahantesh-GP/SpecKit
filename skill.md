/speckit.constitution

Create a constitution for this existing brownfield Adaptive Workflow Engine.

Establish engineering principles that:
- preserve the existing configuration-driven and Azure Durable Functions architecture;
- maintain clear separation between workflow orchestration, activities, and external service implementations;
- require external integrations to use interface-based contracts and remain independently testable through stubs/mocks;
- preserve asynchronous external-event patterns, including correlation, idempotency, timeout, retry, and failure handling;
- favor reuse of existing generic workflow infrastructure over introducing parallel or process-specific mechanisms;
- keep credentials, endpoints, authentication, and environment-specific configuration externalized;
- require appropriate unit, workflow, and integration testing;
- require Spec Kit generated designs and implementations to respect verified repository architecture and avoid inventing unconfirmed requirements.

Derive project-specific details from the existing repository. Keep the constitution concise, durable, and applicable to future features and integrations, not only DocNav.