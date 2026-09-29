az storage blob download `
  --account-name dsgblobstorage `
  --container-name test-container `
  --name "API Reference - DocNav API Documentation.pdf" `
  --file "$env:TEMP\docnav-test.pdf" `
  --auth-mode login