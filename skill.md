Step 2 — Build the Context: Understand the Existing Application

Why this step?

Before developing a new feature, Copilot needs to understand how the existing application works, including its business rules, technical architecture and development practices.

1. What do we provide?

- Existing source code and project structure
- Business requirements, workflows and rules
- Architecture and design documents
- Existing tests, coding standards and best practices

2. What does Copilot do?

- Reviews the available code and documents
- Identifies how the application works and its business processes
- Understands the architecture, components and dependencies
- Identifies existing coding patterns, testing and security practices

3. What do we get?

A Context Report (Application Summary) containing:

- Business context: What the application does, its workflows and existing business rules
- Technical context: Architecture, technologies, components and integrations
- Development practices: Existing coding conventions, testing approaches and security practices

The team reviews and validates the findings.

Practical Example — Process Automation / DocNav

Business: The application executes workflows defined through JSON and can integrate document-processing activities.

Technical: Uses Azure Durable Functions, WorkflowActivities and external service integrations.

Existing practices: Separates workflow activities through interfaces and uses established testing patterns.

What happens next? — Step 3: Constitution

Use the reviewed technical findings and existing development practices, along with team standards, to define the engineering rules for future development.

Remember:

Build Context = Understand what already exists.

Constitution = Agree on the rules to follow when developing.