$rg = "func-porcessautomation_group"
$app = "func-porcessautomation"
$sub = az account show --query id -o tsv


az functionapp config appsettings list --name $app --resource-group $rg --query "[].name" --output table

$url = "https://management.azure.com/subscriptions/$sub/resourceGroups/$rg/providers/Microsoft.Web/sites/$app/syncfunctiontriggers?api-version=2022-03-01"

az rest --method POST --url $url