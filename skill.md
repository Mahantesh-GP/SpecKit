dotnet publish .\AdaptiveOrchestrator\AdaptiveOrchestrator.csproj `
  -c Release `
  -o .\publish-combined



Get-Content .\publish-combined\functions.metadata |
  Select-String "HttpStartOrchestrator|OrderIntakeTimer"




az functionapp show `
  --name func-processautomation `
  --resource-group func-processautomation_group `
  --query "{Name:name,State:state,Kind:kind}" `
  -o json