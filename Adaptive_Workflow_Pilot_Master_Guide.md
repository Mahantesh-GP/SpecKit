# Adaptive Workflow Engine -- Pilot & Handover Master Guide

## 1. Purpose of This Document

This document is the working master reference for the Adaptive Workflow
Engine pilot and handover approach.

It consolidates the latest understanding from: - Satish's technical
walkthrough of the Adaptive Workflow/Aspire implementation. - Earlier
discussions with Mani and Satish about the pilot, activity
implementation, external-service contracts, local testability, and Spec
Kit. - The current Adaptive Workflow Engine repository structure
reviewed during the pilot. - DocNav documentation reviewed only to
understand the external-service boundary.

Where an earlier assumption conflicts with Satish's technical
walkthrough or the current repository, the walkthrough and repository
evidence take precedence.

------------------------------------------------------------------------

## 2. Correct Project Boundary

There are three distinct concerns.

### 2.1 Adaptive Workflow Engine

The Adaptive Workflow Engine is a reusable, configuration-driven
orchestration framework.

Its responsibility is to provide capabilities such as: - Workflow
startup. - Workflow-definition resolution. - Workflow snapshot
creation. - Durable Functions orchestration. - Validation of workflow
configuration. - Step execution. - Activity resolution and invocation. -
Conditions. - Sequential processing. - Parallel processing. -
External-event waiting and continuation. - State/result propagation
between workflow steps. - Common framework-level observability and
exception-handling patterns.

It must remain generic and reusable by multiple consuming teams.

### 2.2 Commitment Typing

Commitment Typing is one consuming business application/team.

The Commitment Typing team owns: - Its document-processing business
requirements. - Its document types. - Search notes and interpretation
rules. - Fields that need to be extracted. - Classification/extraction
requirements. - Business rules. - Real external-service integration
required by its activities. - Mapping of external-service results into
its business workflow.

Commitment Typing is being used as an initial concrete example for
adopting the Adaptive Workflow Engine, but its business logic must not
be moved into the generic orchestration framework.

### 2.3 DocNav

DocNav is a separate service/project owned by a separate team.

DocNav provides configurable document-processing capabilities. Based on
the documentation reviewed, these can include document/text processing
and optional LLM/validation processing.

Commitment Typing may call DocNav from its activity implementations.

The Adaptive Workflow Engine does **not** own DocNav and should not
contain DocNav's internal processing implementation.

------------------------------------------------------------------------

## 3. Our Role in the Pilot

Our responsibility is **enablement and initial reference setup**, not
implementation of Commitment Typing's production business logic.

We are expected to prepare a working pattern that demonstrates how a
consuming team can use the Adaptive Workflow Engine.

Our pilot/reference implementation should demonstrate:

1.  How a workflow is configured.
2.  How an `activityKey` is resolved.
3.  How a Durable Activity is invoked.
4.  How an activity delegates external/service-specific work outside the
    generic orchestration logic.
5.  How an interface/contract can isolate an external dependency.
6.  How a placeholder/stub implementation can make the flow locally
    testable.
7.  How an activity returns `ActivityExecutionResult` to the
    orchestration.
8.  How asynchronous processing can wait for an external event.
9.  How an external event resumes the workflow.
10. How results from earlier steps can be consumed by later steps.
11. How a consuming team can replace the placeholder with its real
    implementation.
12. How Spec Kit should be used by consuming teams for their activity
    implementation work.

After the initial setup/reference pattern is ready, it is handed over to
the consuming team.

------------------------------------------------------------------------

## 4. What We Are NOT Implementing

For this pilot, we should not implement:

-   Commitment Typing's real business rules.
-   Real document-specific extraction logic.
-   Real search-note interpretation.
-   Production classification logic.
-   Production extraction prompts.
-   Real DocNav authentication.
-   Managed Identity integration for DocNav.
-   APIM subscription-key handling for the consuming team's production
    integration.
-   Production DocNav request/response mapping.
-   Commitment Typing-specific document configurations.
-   Production callback/WebSocket handling unless it is explicitly added
    to the framework scope.
-   DocNav's internal Document Intelligence, Vision, Content
    Understanding, LLM, or validation implementation.

Those belong to the consuming team's later implementation or to the
DocNav team itself.

------------------------------------------------------------------------

## 5. Master Architecture

``` text
Client / Trigger
      |
      v
HTTP Starter / Service Bus Starter
      |
      v
WorkflowDefinitionProvider
      |
      +-- resolve workflow definition
      |
      v
Workflow Snapshot
      |
      +-- business input
      +-- workflow definition
      +-- correlation ID
      |
      v
ProcessOrchestrator
      |
      +-- validate definition
      +-- iterate configured steps
      +-- interpret step type
      |
      +-------------------------------+
      |                               |
      v                               v
 Activity                         ExternalEvent
      |                               |
      v                               v
ActivityRegistry                 Durable wait
      |                               |
      v                               |
Durable Activity                     |
      |                               |
      v                               |
Activity contract/service            |
      |                               |
      v                               |
Placeholder or consumer service      |
      |                               |
      +-------------------------------+
                      |
                      v
              Workflow continues
```

The framework controls orchestration.

The consuming team controls the actual business/service implementation
behind its activities.

------------------------------------------------------------------------

## 6. Activity Extension Pattern

Satish's walkthrough makes the activity boundary important.

A consumer-specific service call must not be generalized into the
Durable Functions orchestration framework.

The intended pattern is:

``` text
Workflow JSON
    |
    | activityKey
    v
ActivityRegistry
    |
    v
Durable Activity
    |
    v
Activity implementation
    |
    v
Service contract / abstraction
    |
    +--> Placeholder/stub during local/reference testing
    |
    +--> Real consumer implementation after handover
```

For example, Commitment Typing may eventually call DocNav, while another
consuming team may use an entirely different service.

Therefore, DocNav must not become a hard dependency or business concept
of the generic orchestrator.

------------------------------------------------------------------------

## 7. Commitment Typing as the Reference Scenario

The walkthrough uses a Commitment Typing-style sequence to explain the
framework.

Conceptually:

``` text
Request Intake
      |
      v
OCR / document-processing activity
      |
      v
WAIT for external completion event
      |
      v
Classification
      |
      v
WAIT for classification completion
      |
      v
Extraction
      |
      +--> activities may run in parallel where appropriate
      |
      v
Continue workflow
```

This sequence is useful to prove framework capabilities.

It does **not** mean our pilot owns the actual OCR, classification,
extraction, search-note, prompt, or DocNav business logic.

------------------------------------------------------------------------

## 8. External-Service Testing Strategy

A key pilot requirement is that external-service dependencies must not
prevent local testing.

The reference pattern should therefore use:

``` text
Activity
   |
   v
External-service contract
   |
   v
Placeholder / stub implementation
   |
   v
Predictable local response
```

This proves that: - Dependency injection works. - The activity can call
an external-service abstraction. - Results can return through the
activity. - The orchestration can continue. - The solution can be tested
without onboarding to the actual external service.

The placeholder is a testing/reference implementation only.

It should be replaceable by the consuming team's real service
implementation.

------------------------------------------------------------------------

## 9. Asynchronous External-Event Pattern

The framework already supports external-event behavior.

The reference scenario should prove:

``` text
Activity executes
      |
      v
External work is considered submitted/started
      |
      v
Activity completes
      |
      v
Workflow reaches ExternalEvent step
      |
      v
Durable orchestration waits
      |
      v
External event is raised
      |
      v
Workflow resumes
      |
      v
Next configured step executes
```

For the pilot, the completion event can be simulated through the
existing generic external-event mechanism.

We do not need to create a fake DocNav server merely to prove this
framework behavior.

------------------------------------------------------------------------

## 10. Result Propagation

The walkthrough establishes that results from completed steps are
maintained in workflow state and can be accessed by later activities.

Therefore, the reference implementation should demonstrate that an
activity can:

1.  Receive workflow/business input.
2.  Produce an `ActivityExecutionResult`.
3.  Store meaningful sample output.
4.  Allow a later step to retrieve an earlier step's result when
    required.

This is important for real consumers because a later activity may depend
on information produced by an earlier document-processing activity.

------------------------------------------------------------------------

## 11. Parallel Processing

Parallel execution is a framework capability.

For the Commitment Typing example, processing after classification may
eventually include parallel work across classified document types or
extraction activities.

Parallelism should be validated as a framework capability, but we should
not invent Commitment Typing-specific parallel business rules.

The first reference implementation should remain as small as possible
and prove the basic activity and external-event patterns before
expanding into parallel scenarios.

------------------------------------------------------------------------

## 12. Recommended Implementation Phases

### Phase 1 -- Freeze Scope

Confirm: - Framework responsibility. - Consumer responsibility. - Pilot
responsibility. - Handover boundary.

Do not write production Commitment Typing logic.

### Phase 2 -- Establish Clean Baseline

Run the existing Adaptive Workflow solution unchanged.

Verify: - Solution builds. - Aspire starts. - Functions start. - HTTP
starter works. - A sample workflow starts. - Existing placeholder
activities execute.

Record the current branch/commit before modifications.

### Phase 3 -- Select Minimal Reference Scenario

Select the smallest scenario that proves:

``` text
Workflow JSON
 -> ActivityRegistry
 -> Durable Activity
 -> Service contract
 -> Placeholder implementation
 -> ActivityExecutionResult
```

Then separately prove:

``` text
Activity
 -> ExternalEvent wait
 -> simulated event
 -> workflow resumes
```

### Phase 4 -- Spec Kit `/specify`

Create the feature specification for the reusable
consumer-activity/reference pattern.

Do not describe it as "build DocNav integration."

### Phase 5 -- Review `spec.md`

Before `/plan`, verify that the generated specification: - Keeps the
framework generic. - Uses placeholder/stub external-service behavior. -
Contains no Commitment Typing production business logic. - Contains no
real DocNav integration requirements. - Includes local testability. -
Includes handover/consumer extensibility.

### Phase 6 -- Spec Kit `/plan`

Allow Spec Kit to create the technical plan using the existing
repository structure.

The plan must reuse existing: - Workflow contracts. - Activity
services. - Activity registry. - Dependency injection. -
ProcessOrchestrator. - External-event mechanism.

It must not create another orchestration layer.

### Phase 7 -- Spec Kit `/tasks`

Generate small implementation tasks.

Expected areas may include: - Reference external-service abstraction. -
Placeholder implementation. - DI registration. - Reference activity
wiring. - Activity result propagation. - External-event demonstration. -
Local tests. - Consumer guidance.

Exact tasks should follow the approved specification and plan.

### Phase 8 -- Implement Contract + Placeholder

Implement the smallest service abstraction needed to demonstrate the
consumer extension pattern.

Avoid coupling the generic framework to DocNav.

### Phase 9 -- Wire Dependency Injection

Register the placeholder implementation through DI.

The activity should depend on an abstraction rather than a concrete
production service.

### Phase 10 -- Prove Local Execution

Verify the reference activity works without: - Real DocNav. - Managed
Identity. - APIM credentials. - External network dependency.

### Phase 11 -- Prove External-Event Continuation

Run the workflow until it waits.

Raise the existing external event manually.

Verify the orchestration resumes and executes the next step.

### Phase 12 -- Validate Additional Framework Behavior

After the basic path works, validate relevant scenarios such as: -
Invalid activity key. - Activity failure. - External-event timeout. -
Invalid/incorrect event. - Parallel branch behavior. - Result
propagation.

### Phase 13 -- Observability and Error Handling

Review existing logging, OpenTelemetry, Application Insights
integration, and exception handling before adding anything new.

Only fill actual gaps required by the pilot/reference implementation.

### Phase 14 -- Consumer Guide

Document how another team should: 1. Define/configure a workflow. 2.
Create/select an activity key. 3. Implement an activity. 4. Define an
external-service abstraction when required. 5. Implement its real
service adapter. 6. Register dependencies. 7. Register activity mapping.
8. Use external events for asynchronous operations. 9. Test locally. 10.
Use Spec Kit for implementation/change work.

### Phase 15 -- Handover

The consuming team then owns its real implementation.

For Commitment Typing, this may include: - Real DocNav calls. -
Authentication. - Configuration IDs. - Document-processing
configuration. - Prompts. - Search-note processing. - Extraction
fields. - Classification/extraction result handling. - Production
correlation/callback handling. - Business validation and rules.

------------------------------------------------------------------------

# 13. Revised Spec Kit `/specify` Prompt

Use the following as the starting feature request.

``` text
Establish a reusable reference implementation for consuming teams to implement workflow activities within the Adaptive Workflow Engine.

The Adaptive Workflow Engine must remain a generic, configuration-driven orchestration framework. Consumer-specific business logic and external-service integration must remain outside the generic orchestration logic.

The reference implementation must demonstrate how a configured workflow activity is resolved and executed, how the activity delegates external or application-specific work through a service contract/abstraction, and how the result is returned to the workflow as an activity execution result.

For local development and demonstration, provide a placeholder/stub implementation of the external-service contract so that the complete reference flow can be tested without access to a real external service.

The reference must also demonstrate the existing asynchronous external-event pattern: a workflow can execute an activity, wait for an external completion event, receive a simulated completion event through the framework's existing event mechanism, and then continue to the next configured step.

The implementation must reuse the existing Adaptive Workflow Engine architecture, including workflow configuration, workflow definition resolution, workflow snapshots, ProcessOrchestrator, ActivityRegistry, WorkflowActivities, dependency injection, workflow state/result propagation, and the existing external-event mechanism.

The reference implementation must not contain Commitment Typing business rules or a real DocNav integration. Commitment Typing is only a reference consumer/use case. After handover, consuming teams will use this pattern and Spec Kit to implement their own business activities and real external-service integrations.

The completed reference should be locally testable and documented sufficiently for another consuming team to follow the same implementation pattern.
```

------------------------------------------------------------------------

# 14. Revised Project Constitution Guidance

If the project already has a Spec Kit constitution, **do not replace it
blindly**. Review it and update only principles that conflict with the
clarified architecture.

If we are creating or revising the constitution for this pilot, use the
following prompt.

``` text
Review and update the project constitution for the Adaptive Workflow Engine based on the following architectural principles.

1. Framework Reusability
The Adaptive Workflow Engine is a reusable, configuration-driven orchestration framework intended for multiple consuming teams. Framework code must not contain business logic specific to Commitment Typing, Policy Typing, DocNav, or any other individual consumer.

2. Configuration-Driven Orchestration
Workflow sequencing and supported orchestration behavior should be expressed through the established workflow-definition model and interpreted by the existing orchestration framework. Do not create consumer-specific hard-coded orchestration when the existing configuration model can represent the behavior.

3. Clear Activity Boundary
Consumer-specific operations belong in workflow activities or services invoked by those activities. External-service calls must not be embedded into the generic ProcessOrchestrator.

4. Contract-Based External Dependencies
When an activity depends on an external or application-specific service, use an explicit contract/abstraction and keep its implementation separate from the generic orchestration logic. This allows local substitutes and consumer-specific implementations.

5. Local Testability
Reference implementations and framework changes must be testable locally without requiring access to production external services. External dependencies should support placeholder, stub, fake, or equivalent test implementations where appropriate.

6. Preserve Existing Framework Mechanisms
New work must reuse existing workflow definition resolution, workflow snapshots, ProcessOrchestrator, ActivityRegistry, WorkflowActivities, dependency injection, workflow state/result propagation, external-event handling, and other established framework capabilities rather than duplicating them.

7. Asynchronous Workflow Support
Long-running external operations should use the established Durable Functions external-event/wait pattern when asynchronous completion is required. Do not block orchestration execution while waiting for an external system.

8. Consumer Ownership
Consuming teams own their business rules, external-service adapters, document-processing configuration, prompts, extraction logic, authentication details, and production integration behavior. The framework provides extension points and orchestration capabilities, not consumer business implementations.

9. Incremental Reference Implementation
Pilot/reference work should first prove the smallest end-to-end extension pattern before adding advanced scenarios such as parallel processing, complex retries, or consumer-specific behavior.

10. Spec Kit Scope
Use Spec Kit primarily for the activity/consumer implementation areas and framework changes required to support them. Specifications must describe required behavior and boundaries without prematurely hard-coding a particular consumer's implementation details.

11. Observability and Failure Handling
Framework and reference implementations should follow the project's established logging, telemetry, exception-handling, timeout, and retry mechanisms. Extend them only when a demonstrated gap exists.

12. Handover Readiness
Reference implementations must be understandable and documented so consuming teams can replace placeholders with their real implementations without modifying the generic orchestration architecture.
```

------------------------------------------------------------------------

## 15. Decision Rule Going Forward

For every proposed change, ask:

> Is this generic orchestration/framework behavior, a reusable reference
> pattern, or consumer-specific business/integration logic?

If it is generic framework behavior, it may belong in Adaptive Workflow
Engine.

If it is a reusable reference pattern needed to demonstrate the
extension mechanism, it may belong in the pilot/reference
implementation.

If it is Commitment Typing business logic or real DocNav integration, it
belongs to the consuming team's implementation after handover.

------------------------------------------------------------------------

## 16. Current Status

Architecture understanding: **Ready**

Team/project boundaries: **Ready**

Satish technical walkthrough: **Master reference**

Repository structure: **Understood sufficiently to begin**

DocNav role: **External service, not framework-owned**

Pilot role: **Reference/setup/enablement**

Real Commitment Typing implementation: **Out of pilot scope**

Next action:

``` text
Run existing Adaptive Workflow solution unchanged
        |
        v
Confirm clean baseline
        |
        v
Run /specify using the revised feature request
        |
        v
Review spec.md before /plan
```
