$rg   = "func-porcessautomation_group"
$app  = "func-porcessautomation"
$sub  = az account show --query id -o tsv


az functionapp restart -g $rg -n $app
Start-Sleep -Seconds 60


$key = az functionapp keys list -g $rg -n $app --query masterKey -o tsv
Invoke-RestMethod -Uri "https://func-porcessautomation-ctdadmhbctb8a8gx.eastus2-01.azurewebsites.net/admin/host/status" -Headers @{ "x-functions-key" = $key }


az rest --method post --url "https://management.azure.com/subscriptions/$sub/resourceGroups/$rg/providers/Microsoft.Web/sites/$app/syncfunctiontriggers?api-version=2023-12-01"


az functionapp function list -g $rg -n $app --query "[].name" -o table
