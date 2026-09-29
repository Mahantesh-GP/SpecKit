/speckit.constitution

Create a concise constitution for the WorkflowActivities project.

Principles:
- WorkflowActivities is the Spec Kit scope; changes outside it require explicit justification.
- Follow and reuse existing architecture, contracts, models, and project conventions.
- Keep activities focused and separate external integrations behind appropriate abstractions using dependency injection.
- Activities and integrations must be independently testable; support mocks/stubs for external dependencies.
- Never hard-code secrets or environment-specific configuration.
- Keep changes minimal and feature-focused; avoid unrelated refactoring.
- Follow specification → plan → tasks → implementation with developer review.
- Follow existing .NET/C# standards and require appropriate build and test validation.

Keep the constitution project-level and feature-independent. Do not include DocNav-specific requirements or detailed implementation decisions.