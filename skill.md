/speckit.constitution

Create a concise constitution for the WorkflowActivities project with these principles:

1. WorkflowActivities is the extension layer for implementing activities used by the Adaptive Workflow Engine. Feature development should remain within this boundary unless a justified change to shared components is required.

2. Follow existing project architecture and contracts. Reuse OrchestratorModels and existing activity contracts where possible rather than introducing unnecessary abstractions.

3. Keep activities focused. External system or business-specific logic should be implemented through clear interfaces and service implementations, using dependency injection.

4. Activities and external integrations must be independently testable. Use mocks/stubs when real external dependencies are unavailable.

5. Never hard-code credentials, tokens, URLs, connection strings, or other environment-specific secrets.

6. Keep changes minimal and feature-focused. Avoid unrelated framework changes or refactoring.

7. Every feature must follow Spec-Driven Development: specification → plan → tasks → implementation, with developer review and appropriate testing at each stage.

8. Generated code and artifacts must follow existing .NET/C# conventions, build successfully, and be reviewed before acceptance.