builder.Services.AddSingleton<IDocNavAccessTokenProvider>(_ =>
    new DocNavAccessTokenProviderStub(
        Environment.GetEnvironmentVariable("DOCNAV_JWT")
        ?? throw new InvalidOperationException("DOCNAV_JWT is not configured")));