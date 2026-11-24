Deploy Function1 and Function2 to ONE Azure Function App
Key rule: one Function App = one deployed project. You cannot deploy two separate projects into the same app (the second deploy overwrites the first). So we merge Function2 into Function1, then deploy that single project.
Replace anything in <angle brackets> with your own values.
0. Prerequisites (one time)
[ ]
.NET SDK matching your projects (check <TargetFramework> in Function1.csproj, e.g. net8.0)
[ ]
Azure Functions Core Tools v4 (func --version)
[ ]
Azure CLI (az --version) and signed in: az login
[ ]
VS Code with the Azure Functions extension (optional, CLI works too)
[ ]
Azurite running if you test locally with UseDevelopmentStorage=true
1. Merge Function2 into Function1
Copy Function2/HelloFunction2.cs into the Function1 folder.
Open the copied file and change the namespace:
namespace Function1;   // was: namespace Function2;
Check function names are unique (both must differ):
[Function("HelloFunction")] in HelloFunction.cs
[Function("HelloFunction2")] in HelloFunction2.cs
Compare Function2.csproj with Function1.csproj. Copy any extra <PackageReference> lines into Function1.csproj.
Compare the two Program.cs files. Copy any extra builder.Services.... lines into Function1's Program.cs (default template: nothing to copy).
Compare the two local.settings.json files. Copy any extra settings into Function1's.
Stop Function2's terminal. You no longer need port 7072 or the second project.
2. Test locally
cd Function1
func start
Expected output lists both functions:
HelloFunction:  [GET,POST] http://localhost:7071/api/HelloFunction
HelloFunction2: [GET,POST] http://localhost:7071/api/HelloFunction2
Open both URLs in the browser. Each should return "Welcome to Azure Functions!".
Debug (optional): VS Code, Run and Debug, .NET Core Attach, pick the dotnet.exe worker for Function1.
If you see "Unable to access AzureWebJobsStorage": start Azurite, or set a real storage connection string in local.settings.json.
3. Create Azure resources (skip any you already have)
Use your existing Function App if you already have one (e.g. func-porcessautomation). Otherwise:
$rg   = "<resource-group>"
$loc  = "<region, e.g. centralindia>"
$sa   = "<storageaccountname>"      # 3-24 chars, lowercase letters/numbers only
$app  = "<function-app-name>"       # globally unique

az group create -n $rg -l $loc

az storage account create -n $sa -g $rg -l $loc --sku Standard_LRS

az functionapp create `
  -n $app -g $rg `
  --storage-account $sa `
  --consumption-plan-location $loc `
  --runtime dotnet-isolated `
  --runtime-version <8.0 or your version> `
  --functions-version 4 `
  --os-type Linux
Important:
Runtime version must match the TargetFramework in Function1.csproj.
Your company may require a specific plan (Premium / Flex / App Service plan) or naming. Ask your team if unsure.
Optional: create Application Insights and link it, for logs.
4. Deploy
Pick ONE way.
A. Command line (recommended)
cd Function1
func azure functionapp publish <function-app-name>
B. VS Code
Open the Function1 folder (the project that now holds both functions).
Ctrl+Shift+P, then Azure Functions: Deploy to Function App...
Pick your subscription and the Function App. Confirm the overwrite.
Success output ends with the list of deployed functions and their URLs. You should see both HelloFunction and HelloFunction2.
5. Configure app settings in Azure
local.settings.json is not deployed. Add settings in the portal: Function App, Settings, Environment variables (or Configuration), App settings.
Or via CLI:
az functionapp config appsettings set -n <function-app-name> -g <resource-group> --settings "MySetting=value"
Check these exist:
AzureWebJobsStorage (set automatically when created with a storage account)
FUNCTIONS_WORKER_RUNTIME = dotnet-isolated
Any custom settings your code reads
6. Test in Azure
Your functions use AuthorizationLevel.Function, so calls need a function key.
Get the URL with key (portal): Function App, Functions, click HelloFunction, Get function URL, copy it.
Or via CLI:
az functionapp function keys list -n <function-app-name> -g <resource-group> --function-name HelloFunction
Call it:
curl "https://<function-app-name>.azurewebsites.net/api/HelloFunction?code=<function-key>"
curl "https://<function-app-name>.azurewebsites.net/api/HelloFunction2?code=<function-key>"
Both should return "Welcome to Azure Functions!".
Where to check logs:
Portal: Function App, Functions, pick the function, Monitor (or Log stream)
Application Insights, Live Metrics / Failures
7. Troubleshooting
Problem
Fix
Only one function shows after deploy
Check the namespace and [Function("...")] names. Rebuild, redeploy from the Function1 folder.
401 Unauthorized
Missing or wrong ?code= key. Or change to AuthorizationLevel.Anonymous (testing only).
404 Not Found
Wrong route. URL is /api/<FunctionName> unless you set a Route.
500 or host not starting
Portal, Log stream. Usually a missing app setting or a runtime version mismatch.
Runtime mismatch error
Recreate the app with the runtime version that matches TargetFramework.
"Unable to access AzureWebJobsStorage"
Wrong or missing storage connection string in app settings.
Deploy overwrote my other project
Expected. One app, one project. Deploy only the merged Function1.
Alternative (if you must keep two projects)
Keep logic in separate class libraries and reference them from ONE host project. Each library exposes an extension method (for example AddFunction2(...)) that you call in the host's Program.cs. This is the pattern the AdaptiveOrchestrator uses with AddOrderIntake(...). More setup, but it keeps the code separated. Only the host project is deployed.
Checklist
[ ]
Function2 code merged into Function1, builds without errors
[ ]
func start lists both functions, both URLs work locally
[ ]
Function App exists, runtime version matches
[ ]
func azure functionapp publish succeeded and lists both functions
[ ]
App settings added in Azure
[ ]
Both Azure URLs return a response with the function key