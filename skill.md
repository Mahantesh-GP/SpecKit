az functionapp config appsettings list `
  --name func-processautomation `
  --resource-group func-processautomation_group `
  --query "[?name=='AzureWebJobsStorage' || name=='DURABLE_TASK_SCHEDULER_CONNECTION_STRING' || name=='FUNCTIONS_WORKER_RUNTIME' || name=='Email__Authentication' || name=='Email__AllowedMailboxes'].{Setting:name,Configured:value!=null && value!=''}" `
  -o table