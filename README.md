# Beginner's Crash Course to Elastic Stack Series
## Part-6-Troubleshooting-beginner-level-Elasticsearch-Errors
Welcome to the Beginner's Crash Course to Elastic Stack!

This repo contains all resources shared during Part 6: Troubleshooting beginner-level Elasticsearch Errors!

Throughout the series, we have learned about nodes and shards, fine tuning the relevance of your search, full text search, aggregation, and mapping. 

As you continue your journey with Elasticsearch, you will inevitably encounter some common errors associated with the topics we have covered in the series. 

Rind out how to troubleshoot these pesky errors so you can get unstuck! 

## Resources

[Table of Contents: Beginner's Crash Course to Elastic Stack](https://github.com/LisaHJung/Beginners-Crash-Course-to-the-Elastic-Stack-Series): 

This workshop is a part of the Beginner's Crash Course to Elastic Stack series. Check out this table contents to access all the workshops in the series thus far. This table will continue to get updated as more workshops in the series are released! 

[Free Elastic Cloud Trial](https://ela.st/elastic-beginners)

[Instructions](https://dev.to/lisahjung/beginner-s-guide-to-setting-up-elasticsearch-and-kibana-with-elastic-cloud-1joh) on how to access Elasticsearch and Kibana on Elastic Cloud

[Instructions](https://dev.to/elastic/downloading-elasticsearch-and-kibana-macos-linux-and-windows-1mmo) for downloading Elasticsearch and Kibana

[Elastic America Virtual Chapter](https://community.elastic.co/amer-virtual/): 

Want to attend live workshops? Join the Elastic Americal Virtual Chapter to get the deets!

## Follow the clues! 
Whenever you perform an action with Elasticsearch and Kibana, Elasticsearch responds back with an HTTP status and a response body. 

![image](https://user-images.githubusercontent.com/60980933/123306018-fe0c1e00-d4dd-11eb-931d-6c8f7b90eb26.png)

The HTTP status of 201-success indicates that the document has been successfully indexed. The response body indicates that document with an assigned id of 1 has been created in the beginners_crash course index. 

When the request fails, the HTTP status and response body provide valuable clues as to why the request failed so you can fix the error! 

### Common HTTP Errors
#### 4XX errors
HTTP errors that start with 4 stems from client errors. When you see this, correct the request before retrying. 

## Approach
1. Does the HTTP response start with 4xx or 5xx? 
2. What does the response say?

## Trip down memory lane
Throughout the Beginner's Crash Course to Elastic Stack Series, we have covered these topics:
1. Nodes and Shards
2. CRUD operations
3. Full text search
4. Aggregations
5. Mapping

We will revisit each topic and go over common errors you may encounter as you explore these topics further. 

### Errors associated with CRUD operations
**Error 1: 404 Not Found**
In part 1: Intro to Elasticsearch and Kibana, we learned how to perform CRUD operations. Let's say we send the following request to retrieve a document with an id of 1 from the index common_errors.

Request sent: 
```
GET common_errors/_doc/1
```

Expected response from Elasticsearch:
![image](https://user-images.githubusercontent.com/60980933/123666137-2d7c9c80-d7f6-11eb-94de-6719dc562f3f.png)

Elasticsearch returns a 404-error along with cause of the error in the response body. The HTTP error starts with a 4XX, meaning that there was a client error with the request sent.

If you look at the response, Elasticsearch lists the reason as "no such index [common_errors]." Two possible explanations are:
1. The index common_errors truly does not exist or was deleted
2. We do not have the correct index name

**Cause of Error 1** 
In our example, the cause of the error is quite clear! We have not created an index called common_errors and we were trying to retrieve a document from an index that does not exist. 

Let's create one! 

Create an index called common_errors:

Syntax:
```
PUT Name-of-the-Index
```
Example:
```
PUT common_errors
```
Expected response from Elasticsearch:

![image](https://user-images.githubusercontent.com/60980933/123671494-9581b180-d7fb-11eb-864a-8577917d95ee.png)

**Error 2: 405 Method Not Allowed**
Now that we have created the index common_errors, let's index a document!

Suppose you have remembered that you could use the HTTP verb PUT to index a document and sent the following request. 

```
PUT common_errors/_doc
{
"source_of_error": "Using the wrong syntax for PUT or POST indexing request"
}
```
Expected response from Elasticsearch:
![image](https://user-images.githubusercontent.com/60980933/123680880-69b7f900-d806-11eb-91af-a05ad9298a4a.png)

Elasticsearch returns a 405-error along with cause of the error in the response body. This HTTP error starts with a 4XX, meaning that there was a client error with the request sent.

If you look at the response, Elasticsearch lists the reason as "Incorrect HTTP method for uri... allowed:[POST]." 

**Cause of Error 2 : Mixing up the request syntax for POST and PUT operations**

This error message suggests that we used the wrong HTTP method to index this document. 

You can use either PUT or POST HTTP method to index a document. Each HTTP method serves a different purpose and requires a different syntax. 

For example:

**The HTTP verb PUT is used when you want to assign a specific id to your document** 

Syntax: 
```
PUT name-of-the-Index/_doc/id-you-want-to-assign-to-this-document
{
  field_name: "value"
}
```

Let's compare the syntax to the request we just sent: 
```
PUT common_errors/_doc
{
  "source_of_error": "Using the wrong syntax for PUT or POST indexing request"
}
```

You will see that our request uses the HTTP verb PUT but it does not include the document id we want to assign to this document. If you add the id of the document as seen below, you will see that the request is carried out without a hitch!

Correct example for PUT indexing request: 
```
PUT common_errors/_doc/1
{
  "source_of_error": "Using the wrong syntax for PUT or POST indexing request"
}
```

Expected response from Elasticsearch:

![image](https://user-images.githubusercontent.com/60980933/123681933-a1737080-d807-11eb-80b6-bc67d69ca6b6.png)

**The HTTP verb POST is used when you want Elasticsearch to autogenerate an id for the document.** 

If this is the option you wanted, then you could fix the error message by sending the following request.

Syntax:
```
POST Name-of-the-Index/_doc
{
  field_name: "value"
}
```

Correct example for POST indexing request:
```
POST common_errors/_doc
{
  "source_of_error": "Using the wrong syntax for PUT or POST indexing request"
}
```
Expected response from Elasticsearch: 

![image](https://user-images.githubusercontent.com/60980933/123682487-4a21d000-d808-11eb-8dc2-fe9cae47e95d.png)

Elasticsearch will create an autogenerated id for the document that was indexed(line 4).

**Error 3: 409 Version Conflict**

When you index a document using an id that already exists, the existing document is overwritten by the new document. If you do not want a existing document to be overwritten, you can use the `_create` endpoint!

Suppose we forgot that we indexed a document with an id of 1, and tried to index the following document using the `_create` endpoint and assign it an id of 1. 

Syntax:
```
PUT Name-of-the-Index/_create/id-you-want-to-assign-to-this-document
{
  "field_name": "value"
}
```
Example: 
```
PUT common_errors/_create/1
{
  "source_of_error": "Using the _create endpoint to index a document with an existing id"
}
```
Expected response from Elasticsearch:

![image](https://user-images.githubusercontent.com/60980933/123820408-cde6c580-d8b7-11eb-9eaf-e5d44c3c0013.png)

Elasticsearch returns a 409-error along with cause of the error in the response body. This HTTP error starts with a 4XX, meaning that there was a client error with the request sent.

If you look at the response, Elasticsearch lists the reason(line 6) as "version conflict, document already exists (current version [1])." 

When you see this error after sending a request with a `_create` endpoint, it means that the document with an id of 1 already exist. As the _create endpint is specifically used to prevent the original document from being overwritten, Elasticsearch throws an error and does not index this document.  

**Error 4: 400 Failed to Parse**
Suppose you wanted to update document 1 and add the fields http_error and solution as seen below. 

Request sent:
```
POST common_errors/_update/1
{
  "doc": {
    "http_error": "405 Method Not Allowed"
    "solution": "Include doc_id a the end of the PUT request"
  }
}
```

Expected response from Elasticsearch:

![image](https://user-images.githubusercontent.com/60980933/123837576-40f83800-d8c8-11eb-884a-dff519a7190e.png)

Elasticsearch returns a 400-error along with cause of the error in the response body. This HTTP error starts with a 4XX, meaning that there was a client error with the request sent.

If you look at the response, Elasticsearch lists the error type(line 12) as "json_parse_exception" and the reason(line 12) as "...was expecting comma to separate Object entries\n at ... line: 4]." 

In Elasticsearch, if you have multiple fields in an object, you must separate each field with a comma. It tells us that we need to add a comma at the end of line 4 in our request. 

Once you add the comma and send the following request:
```
POST common_errors/_update/1
{
  "doc": {
    "http_error": "405 Method Not Allowed",
    "solution": "Include doc id at the end of the PUT request"
  }
}
```
Expected response from Elasticsearch:

You will see that document with an id of 1 has been successfully updated. 
![image](https://user-images.githubusercontent.com/60980933/123838019-bb28bc80-d8c8-11eb-95c2-a1bc2f1d490c.png)

If you send a GET request to retrieve document 1:
```
GET common_errors/_doc/1
```

Expected response from Elasticsearch:
![image](https://user-images.githubusercontent.com/60980933/123838222-ffb45800-d8c8-11eb-8edc-b4783a997ca0.png)

You will see that the fields solution and http_error have been successfully added to document 1. 

### Errors associated with sending search queries
Suppose we added a new dataset. We want to retrieve all documents in our index and also see the total number of hits. 

We send the following request:
```
GET news_headlines/_search
{
  "track total hits": true
}
```

Expected response from Elasticsearch: 
![image](https://user-images.githubusercontent.com/60980933/123839178-10190280-d8ca-11eb-9667-69e6b5e1769e.png)

Elasticsearch returns a 400-error along with cause of the error in the response body. This HTTP error starts with a 4XX, meaning that there was a client error with the request sent.

If you look at the response, Elasticsearch lists the error type(line 11) as "parsing_exception" and the reason(line 12) as "Unknown key for a VALUE_BOOLEAN in [track total hits]." 

Elasticsearch throws an error because it does not recognize the key track total hits. While the correct terms for the key are all there, it does not include underscore between each term. Elasticsearch does not recognize space therefore it throws an error. 

Add an underscore between each term as shown below and send the following request: 
```
GET news_headlines/_search
{
  "track_total_hits": true
}
```

Expected response from Elasticsearch: 
![image](https://user-images.githubusercontent.com/60980933/123839774-c0870680-d8ca-11eb-9224-d9a939f902a8.png)

Elasticsearch returns the total number of hits(line 12) and displays top 10 hits. 

### Errors associated with full text search
### Errors associated with aggregations
### Errors associated with mapping
### Errors associated with nodes and shards

**Cause: Not preceding name of the API with an underscore while calling it**

Elasticsearch offers several APIs you can access to get detailed information about your cluster, nodes and etc. 

In part 1: Intro to Elasticsearch and Kibana, we leared how to check for the health of the cluster by sending a request to the cluster API. 

What users often forget is that Elasticsearch APIs begin with an underscore by convention. It is very easy to forget this and send a request like the following: 

Bad example:
```
GET cluster/health
```
Expected response from Elasticsearch:

Elasticsearch returns a 405-error HTTP response along with cause of the error in the response body.

![image](https://user-images.githubusercontent.com/60980933/123321567-5d732980-d4f0-11eb-9e9e-80dc51082a0d.png)

Pay attention to the HTTP error 405. It starts with a 4XX, meaning that there was a client error with the request sent. The message uri[cluster/health?pretty=true] indicates that there is a mistake in the request path. 

Correct example:
```
GET _cluster/heatlh
```
Expected response from Elasticsearch:

Elasticsearch will return a 200-success HTTP response and retrieve information about health of your cluster. 
![image](https://user-images.githubusercontent.com/60980933/123323535-dbd0cb00-d4f2-11eb-9fd1-925cc011c633.png)



