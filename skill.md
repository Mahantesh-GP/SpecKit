az functionapp config appsettings list `
  --name func-processautomation `
  --resource-group func-processautomation_group `
  --query "[].name" `
  -o table