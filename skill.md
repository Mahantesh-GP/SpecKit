using System.Text.Json;
using Microsoft.Extensions.Logging.Abstractions;
using com.fnfi.awe.AdaptiveOrchestrator.OrchestratorModels;
using com.fnfi.awe.AdaptiveOrchestrator.WorkflowActivities.Services.DocNavRequestIntegration;

var stub = new DocNavRequestStubService();

var activity = new DocNavRequestActivity(
    stub,
    NullLogger<DocNavRequestActivity>.Instance);

var businessInput = JsonSerializer.SerializeToElement(new
{
    configurationId = "018e1234-5678-7abc-def0-123456789abc",
    documentReference = "blob://docnav-inbox/invoices/invoice-0001.pdf",
    sourceMetadata = "batch-2026-09-25"
});

var input = new WorkflowActivityInput
{
    WorkflowId = "demo-workflow",
    WorkflowVersion = "1.0.0",
    DefinitionHash = "demo-hash",
    CorrelationId = "demo-correlation",
    OrchestrationInstanceId = "demo-instance",
    StepId = "step-1",
    ActivityKey = "DocNavRequestActivity",
    IdempotencyKey = "demo-idempotency-key",
    BusinessInput = businessInput,
    StepResults = new Dictionary<string, ActivityExecutionResult>()
};

var result = await activity.ExecuteAsync(input);

Console.WriteLine($"Succeeded: {result.Succeeded}");
Console.WriteLine($"Message: {result.Message}");
Console.WriteLine($"Output: {result.Output}");