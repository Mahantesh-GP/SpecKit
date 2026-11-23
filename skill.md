AdaptiveOrchestrator + OrderIntake: Combined Azure Functions Deployment Guide
Goal
Deploy one validated Azure Functions package containing both HttpStartOrchestrator and OrderIntakeTimer to the existing Linux Function App func-processautomation in resource group func-processautomation_group.
Current status (from screenshots): dotnet publish succeeded and the published functions.metadata listed both trigger names. Azure rejected publish-combined.zip with InvalidPackageContentException: it could not find the required Azure Functions files at the ZIP root. Deployment has not succeeded.
Important: Deploying a ZIP replaces the existing Function App content; it does not merge packages. Confirm that this is the intended target and that a deployment window/rollback is available. The earlier local workflow tests do not establish successful Azure execution.
1. Work from the correct project directory
Open PowerShell in VS Code:
cd 'C:\EA Work\Spec- kit\Adaptive\ProcessAutomation\AdaptiveOrchestrator'
If your folder differs, use the actual path containing AdaptiveOrchestrator.csproj.
2. Publish the combined host
Use the combined host project that previously generated metadata for both functions:
dotnet publish .\AdaptiveOrchestrator.csproj -c Release -o .\publish-combined
If you already have a successful publish-combined output and have made no code changes, you can skip republishing.
3. Verify the publish output before zipping
Test-Path .\publish-combined\host.json
Test-Path .\publish-combined\functions.metadata
Test-Path .\publish-combined\worker.config.json

Get-Content .\publish-combined\functions.metadata |
  Select-String 'HttpStartOrchestrator|OrderIntakeTimer'
Expected: all three Test-Path commands return True, and both trigger names appear in the metadata. If not, STOP: correct the publish configuration/combined host first. Merely having DLLs for both projects is not enough.
Inspect the package directory for files that must not be deployed, especially local credentials and development-only configuration:
Get-ChildItem .\publish-combined -Force |
  Select-Object Name
Do not ship local.settings.json, credentials, private keys, or development-only secrets. Review appsettings.Development.json if present; remove it from the deployment output unless explicitly required and sanitized. Prefer Azure Function App settings / managed identity for production configuration.
4. Create a ZIP with files at the root
Run from the AdaptiveOrchestrator directory:
if (Test-Path .\publish-combined.zip) {
    Remove-Item .\publish-combined.zip -Force
}

Compress-Archive -Path .\publish-combined\* `
  -DestinationPath .\publish-combined.zip
Correct ZIP layout:
host.json
functions.metadata
worker.config.json
AdaptiveOrchestrator.dll
... other publish files and folders ...
Incorrect layout:
publish-combined/
  host.json
  functions.metadata
  ...
5. Verify the actual ZIP contents
Add-Type -AssemblyName System.IO.Compression.FileSystem
$zipPath = (Resolve-Path .\publish-combined.zip).Path
$zip = [System.IO.Compression.ZipFile]::OpenRead($zipPath)
try {
    $names = @($zip.Entries | ForEach-Object FullName)
    'host.json at root: ' + ($names -contains 'host.json')
    'functions.metadata at root: ' + ($names -contains 'functions.metadata')
    'worker.config.json at root: ' + ($names -contains 'worker.config.json')
    $names | Select-Object -First 20
} finally {
    $zip.Dispose()
}
Expected: all three root checks are True. If any is False, do not deploy. Correct the folder structure and recreate the ZIP.
6. Confirm Azure target and configuration (without exposing secrets)
az account show --query '{subscription:name,tenant:tenantId}' -o table

az functionapp show `
  --name func-processautomation `
  --resource-group func-processautomation_group `
  --query '{name:name,kind:kind,state:state,linuxFxVersion:siteConfig.linuxFxVersion}' -o json
The earlier screenshots showed kind: functionapp,linux. Confirm the target is still correct and the deployed .NET isolated runtime/architecture is supported. A null state in an earlier query is not proof the app is healthy.
Review names only for required settings:
az functionapp config appsettings list `
  --name func-processautomation `
  --resource-group func-processautomation_group `
  --query '[].name' -o table
Required settings depend on your code. Previously observed issues included missing Email__* values, OrderIntake__* values, and ProcessStorage__Blob__CreateIfNotExists being blank instead of a valid Boolean (true or false). Resolve required configuration before an end-to-end test. Do not paste secrets into chat or commit them to Git.
7. Deploy the verified ZIP
Only after Steps 3–6 pass and the target/rollback are approved:
az functionapp deployment source config-zip `
  --resource-group func-processautomation_group `
  --name func-processautomation `
  --src .\publish-combined.zip
A successful command is not sufficient proof that all functions loaded.
8. Verify both functions in Azure
az functionapp function list `
  --resource-group func-processautomation_group `
  --name func-processautomation `
  --query '[].name' -o table
Expected: both HttpStartOrchestrator and OrderIntakeTimer are listed (possibly prefixed by the app name). If the list is empty, check host startup errors and Application Insights before changing the package again. Do not assume trigger synchronization will fix invalid DI/configuration.
9. Smoke-test safely
Check Azure Functions host startup and Application Insights exceptions.
Verify the HTTP trigger is available and authorized; do not expose function keys in screenshots.
Invoke HttpStartOrchestrator with a valid, non-production test payload and inspect its Durable Functions status URL. 202 Accepted means started, not completed.
Verify orchestration status and step results, including failure paths. Previous local screenshots showed some activities reporting invalid inputs.
Confirm OrderIntakeTimer is registered and its dependencies initialize. Trigger it manually only if supported and approved; do not accidentally process real mail/orders.
Review the prior Microsoft Graph authentication failure: AADSTS90002 indicated an invalid/unrecognized tenant ID. Fix the Email__TenantId/identity configuration before claiming OrderIntake works end-to-end.
10. Definition of done
[ ]
dotnet publish succeeds.
[ ]
functions.metadata contains both trigger names.
[ ]
ZIP root contains host.json, functions.metadata, and worker.config.json.
[ ]
ZIP contains no local secrets or development-only credentials.
[ ]
Azure app settings and runtime are valid for both workloads.
[ ]
Azure deployment completes successfully.
[ ]
Both functions appear in az functionapp function list.
[ ]
HTTP orchestration runs and finishes with expected results.
[ ]
OrderIntake authentication and processing work in an approved test.
If deployment still fails
Capture the exact error text from the CLI and check whether the ZIP really contains the three required files at its root. If ZIP validation passes but startup fails, focus next on Azure Functions host logs, dependency injection, environment settings, and Microsoft Graph tenant configuration—not on repeating ZIP deployment.
Architecture note: This approach builds one deployable Azure Functions host package. Separate solutions/repositories may remain, but independently deploying two Functions host projects into the same Function App will overwrite the previous app package rather than combine them.