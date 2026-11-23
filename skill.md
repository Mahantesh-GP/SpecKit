Compress-Archive -Path .\publish-combined\* `
  -DestinationPath .\publish-combined.zip -Force


az functionapp deployment source config-zip `
  --resource-group func-processautomation_group `
  --name func-processautomation `
  --src .\publish-combined.zip