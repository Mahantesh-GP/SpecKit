builder.Services.AddSingleton(new DocNavServiceOptions
{
    BaseUrl = Environment.GetEnvironmentVariable("DOCNAV_BASE_URL")
        ?? throw new InvalidOperationException("DOCNAV_BASE_URL is not configured"),

    SubscriptionKey = Environment.GetEnvironmentVariable("DOCNAV_SUBSCRIPTION_KEY")
        ?? throw new InvalidOperationException("DOCNAV_SUBSCRIPTION_KEY is not configured")
});