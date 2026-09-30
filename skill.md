{
  "workflowId": "DOCNAV TEST",
  "variantKey": "base",
  "version": "1.0",
  "enabled": true,
  "steps": [
    {
      "id": "submit-doc-nav-job",
      "type": "Activity",
      "name": "SubmitDocNavJob",
      "activityKey": "SubmitDocNavJob",
      "enabled": true,
      "inputs": {
        "fields": ["documentReference"]
      }
    }
  ]
}