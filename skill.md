builder.AddAzureFunctionsProject<Projects.AdaptiveOrchestrator>("DurableFunctions")
    .WithHostStorage(storage)
    .WithReference(serviceBus)
    .WithEnvironment("DURABLE_TASK_SCHEDULER_CONNECTION_STRING", dtsConnectionString)
    .WithReference(storage)
    .WithEnvironment("DOCNAV_BASE_URL", "dummy")
    .WithEnvironment("DOCNAV_SUBSCRIPTION_KEY", "dummy")
    .WithEnvironment("DOCNAV_JWT", "dummy");