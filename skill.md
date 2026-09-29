The Azure Blob access has now been verified successfully using:

az storage blob download --auth-mode login

with the same Azure CLI account.

However, BlobDocumentSource using DefaultAzureCredential still returns 403.

Review BlobDocumentSource and determine which credential DefaultAzureCredential
is likely using locally and how we can verify the exact credential/identity being
used.

Do not modify the production implementation yet.