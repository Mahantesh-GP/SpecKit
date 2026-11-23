az functionapp config appsettings list `
  --name func-processautomation `
  --resource-group func-processautomation_group `
  --query "[?starts_with(name, 'Email__')].{Setting:name,Configured:value!=null && value!=''}" `
  -o table