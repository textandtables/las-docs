# Using Java

[API reference](../../reference/java/latest.md)

(TTNote: Consider if an 'Installation' section is needed here for consistency, or if the below section is in lieu of that.)

## Create a client to talk to the API

```java
Credentials credentials = new Credentials(
    System.getenv("LAS_CLIENT_ID"),
    System.getenv("LAS_CLIENT_SECRET"),
    System.getenv("LAS_API_KEY"),
    System.getenv("LAS_AUTH_ENDPOINT"),
    System.getenv("LAS_API_ENDPOINT")
);

client = new Client(credentials);
```

## Upload a document to get a documentId
(TTNote: For consistency, consider including 'upload' as a subsection under the heading of 'make predictions' with verbiage as in the CLI (i.e. 'first list, next upload, finally run inference...') Or alternately, consider whether all other pages should match the syntax here instead.

```java
public void createDocument() throws IOException, APIException, MissingAccessTokenException {
    ContentType contentType = ContentType.fromString("image/jpeg");
    Path path = Paths.get("path/to/document");
    byte[] content = Files.readAllBytes(path);

    JSONObject document = client.createDocument(content, contentType);
    Assert.assertTrue(document.has("contentType"));
    Assert.assertTrue(document.has("documentId"));
}
```

## Make a prediction on a document

Suppose we wish to run inference on a document using one of the available models.
(TTNote: Also for consistency with CLI, consider if a `list` (or in this case `listModel`) is needed first and whether the description should be updated to match.)

```java
public void createPrediction() throws IOException, APIException, MissingAccessTokenException {
    String modelId = "las:model:<hex-uuid>"; 
    JSONObject prediction = client.createPrediction(documentId, modelId);
    JSONArray fields = prediction.getJSONArray("predictions");
    Assert.assertNotNull(fields);
}
```

{% hint style="info" %}
See what models are available along with their `modelId` by using the method `listModels()`
{% endhint %}
(TTNote: For consistency, consider if this info window should be part of the steps as in the CLI instead, i.e. 'first list models, next upload, finally run inference')


## Set ground truth of document

When uploading data that will be used for training and evaluation, we need to provide a ground truth:

(TTNote: Consider referencing more detailed section on ground truth from 'documents')

```java
File file = new File("myReceipt.pdf");
InputStream content = new FileInputStream(file);
JSONArray groundTruth = new JSONArray();
groundTruth.put(new JSONObject(){{ put("label", "totalAmount"); put("value", "100.00"); }});
groundTruth.put(new JSONObject(){{ put("label", "dueDate"); put("value", "2020-02-20"); }});
CreateDocumentOptions options = new CreateDocumentOptions().setGroundTruth(groundTruth);
JSONObject document = this.client.createDocument(content, ContentType.PDF, options);
```

### Update an existing document
If a prediction reveals incorrect values in the ground truth of a document, 
we can update the existing document with new ground truth values:
```java
JSONArray groundTruth = new JSONArray();
groundTruth.put(new JSONObject(){{ put("label", "totalAmount"); put("value", "199.00"); }});
groundTruth.put(new JSONObject(){{ put("label", "dueDate"); put("value", "2020-03-20"); }});
JSONObject document = this.client.createDocument("las:document:<hex-uuid>", groundTruth);
```

(TTNote: Consider if 'Create a document with a consentId', 'Get document and download document content', 'Revoke consent and delete documents' sections
are needed for consistency.)

## Create a batch and associate documents with it

Creating a batch is a way to group documents. 
This is useful for specifying batches of documents to use in improving the model later:
(TTNote: Consider a link here to the batches and consents details.)

```java
public void createBatch() throws IOException, APIException, MissingAccessTokenException {
    CreadBatchOptions options = new CreateBatchOptions()
        .setName("TrainingData")
        .setDescription("I'm gonna create a new batch, give me a batch id!");
    JSONObject response = client.createBatch(options);
    Assert.assertNotNull(response.get("batchId"));
}
```

