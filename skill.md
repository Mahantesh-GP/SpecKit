Deploy Function1 and Function2 to a Single Azure Function App
Objective
Validate that two separate .NET isolated Azure Functions projects can run in one Azure Function App using a single deployment package.
Function1 (main host) ----+
                          +--> Combined publish --> Azure Function App
Function2 (reference) ----+                         |-- Function1
                                                    +-- Function2
Prerequisites
Compatible .NET SDK and Azure Functions Core Tools v4.
Azure CLI and access to an approved test Function App with a compatible isolated runtime.
Two projects with distinct function names and compatible dependencies.
Required local and Azure application settings.
Run the following PowerShell commands from the solution root unless otherwise specified.
Step 1 — Add the project reference
dotnet add .\Function1\Function1.csproj reference .\Function2\Function2.csproj
Function1 is the main host. Function2's Program.cs will not run as a separate host; register any required services in Function1. A project reference alone does not guarantee trigger discovery.
Step 2 — Build
dotnet build .\Function1\Function1.csproj
Expected: build succeeded with zero errors.
Step 3 — Publish
dotnet publish .\Function1\Function1.csproj -c Release -o .\CombinedPublish
Use a fresh output directory to avoid stale artifacts.
Step 4 — Verify function metadata and package contents
Get-Content .\CombinedPublish\functions.metadata |
    ConvertFrom-Json | Select-Object name, entryPoint, scriptFile

Get-ChildItem .\CombinedPublish -Force
Test-Path .\CombinedPublish\.azurefunctions -PathType Container
Expected metadata (actual names and namespaces may differ):
Function1    Function1.Function1.Run
Function2    Function2.Function2.Run
Confirm host.json, functions.metadata, worker.config.json, referenced assemblies and the .azurefunctions directory are present.
Stop if either function is missing. Do not manually edit generated metadata. For a reliable fallback, place both trigger classes in the main host and keep business logic in separate referenced class libraries. Republish and repeat verification.
Step 5 — Test locally
Push-Location .\CombinedPublish
func start --no-build
Provide required local settings securely before starting. If both functions are HTTP-triggered, the host should list both endpoints, for example:
http://localhost:7071/api/Function1
http://localhost:7071/api/Function2
Test each endpoint using its actual HTTP method and payload. Stop the host with Ctrl+C. Do not deploy local.settings.json or secrets.
Step 6 — Verify Azure target
From the combined publish directory:
az login
az account show -o table
$App = 'YOUR-TEST-FUNCTION-APP'
$RG = 'YOUR-RESOURCE-GROUP'
az functionapp show --name $App --resource-group $RG -o table
Confirm the subscription, app, runtime compatibility and required application settings. Deployment replaces the existing application package, so use an approved test app.
Step 7 — Deploy the combined output once
From CombinedPublish, after successful local testing:
func azure functionapp publish $App --dotnet-isolated --no-build
Do not publish Function2 separately. Core Tools should package the combined output; if publishing fails, inspect the reported error before retrying.
Step 8 — Verify Azure deployment
az functionapp function list --name $App --resource-group $RG --query '[].name' -o table
Open Azure Portal > Function App > Functions. Confirm both functions are listed. Invoke both HTTP endpoints (using function keys if required), or verify execution logs for non-HTTP triggers.
Success criteria
[ ]
Both projects build successfully.
[ ]
Both functions appear in generated functions.metadata.
[ ]
Both execute successfully under one local host.
[ ]
One combined deployment succeeds.
[ ]
Both functions appear and execute in the same Azure Function App.
Architectural conclusion
Separate projects can contribute to a single deployable Functions host, provided trigger discovery, dependencies and configuration are validated. Two independent deployments to the same Function App do not merge. This PoC validates shared hosting, not independent deployment lifecycles.