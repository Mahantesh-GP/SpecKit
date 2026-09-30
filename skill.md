### DocNav Submit Job Test
POST {{AdaptiveOrchestrator_HostAddress}}/api/HttpStartOrchestrator
Content-Type: application/json

{
  "process": "docnavtest",
  "variant": "base",
  "documentReference": "YOUR_BLOB_PDF_URL"
}