using var content = new MultipartFormDataContent();

var fileContent = new StreamContent(request.Document.Content);
fileContent.Headers.ContentType =
    new System.Net.Http.Headers.MediaTypeHeaderValue("application/pdf");

content.Add(
    fileContent,
    "Package",
    request.Document.FileName);