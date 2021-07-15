# Beginner's Crash Course to Elastic Stack Series
## Part-6-Troubleshooting-Beginner-Level-Elasticsearch-Errors

Welcome to the Beginner's Crash Course to Elastic Stack!

This repo contains all resources shared during Part 6: Troubleshooting Beginner-Level Elasticsearch Errors!

Throughout the series, we have learned about nodes and shards, fine tuning the relevance of your search, full text search, aggregation, and mapping. 

As you continue your journey with Elasticsearch, you will inevitably encounter some common errors associated with the topics we have covered in the series. 

Learn how to troubleshoot these pesky errors so you can get unstuck! 

## Resources

[Table of Contents: Beginner's Crash Course to Elastic Stack](https://github.com/LisaHJung/Beginners-Crash-Course-to-the-Elastic-Stack-Series): 

This workshop is a part of the Beginner's Crash Course to Elastic Stack series. Check out this table contents to access all the workshops in the series thus far. This table will continue to get updated as more workshops in the series are released! 

[Free Elastic Cloud Trial](https://ela.st/elastic-beginners)

[Instructions](https://dev.to/lisahjung/beginner-s-guide-to-setting-up-elasticsearch-and-kibana-with-elastic-cloud-1joh) on how to access Elasticsearch and Kibana on Elastic Cloud

[Instructions](https://dev.to/elastic/downloading-elasticsearch-and-kibana-macos-linux-and-windows-1mmo) for downloading Elasticsearch and Kibana

[Elastic America Virtual Chapter](https://community.elastic.co/amer-virtual/): 

Want to attend live workshops? Join the Elastic Americal Virtual Chapter to get the deets!

## Want To Troubleshoot Your Errors? Follow The Clues! 
Whenever you perform an action with Elasticsearch and Kibana, Elasticsearch responds with an HTTP status and a response body. 

The request below asks Elasticsearch to index a document and assign it an id of 1. 

![image](https://user-images.githubusercontent.com/60980933/125672998-51ecff9f-50cb-488a-ac02-374ed3a8eb6e.png)

The HTTP status of 201-success indicates that the document has been successfully created. The response body indicates that document with an assigned id of 1 has been created in the beginners_crash_course index. 

As we work with Elasticsearch, we will inevitably encounter error messages. When this happens, the HTTP status and the response body will provide valuable clues about why the request failed so we can fix the error! 

### Common Errors

Here are some common errors that you may encounter as you work with Elasticsearch. 

#### Unable to connect
The cluster may be down or it may be a network issue. Check the network status and cluster health to identify the problem. 
#### Connection unexpectedly closed
The node may have died or it may be a network issue. Retry your request. 
#### 5XX Error
Errors with HTTP status starting with 5 stems from internal server error in Elasticsearch. When you see this, take a look at the Elasticsearch log and identify the problem. 
#### 4XX Error
Errors with HTTP status starting with 4 stems from client errors. When you see this, correct the request before retrying. 

As beginners, we are still familiarizing ourselves with the rules and syntax required to communicate with Elasticsearch. Majority of the error messages we encouter are likely caused by the mistakes we make while writing our requests(4XX errors).   

**To strengthen our grasp on requests we've learned throghout the series, we will only focus on 4XX errors during this workshop.** 

## Thought Process For Troubleshooting Errors
1. What number does the HTTP status start with(4XX? 5XX?)
2. What does the response say?
3. Look up documentation about the specific issue. If request was covered in Beginner's Crash Course, check the repos for correct syntax
4. 

## Trip down memory lane
Throughout the series, welearned how to send requests related to following topics:

1. CRUD operations
2. Queries
3. Aggregations
4. Mapping

We will revisit each topic and troubleshoot common errors you may encounter as you explore each topic. 

## Errors associated with CRUD operations

### Error 1: 404 no such index[x]

In [Part 1: Intro to Elasticsearch and Kibana](https://github.com/LisaHJung/Part-1-Intro-to-Elasticsearch-and-Kibana), we learned how to perform CRUD operations. Let's say we sent the following request to retrieve a document with an id of 1 from the index `common_errors`.

Request sent: 
```
GET common_errors/_doc/1
```

Expected response from Elasticsearch:

Elasticsearch returns a 404-error along with cause of the error in the response body. The HTTP status starts with a 4XX, meaning that there was a client error with the request sent.

![image](https://user-images.githubusercontent.com/60980933/125688779-16b15f58-3bab-4ef4-ab00-aaa8d9490603.png)

When you look at the response body, Elasticsearch lists the reason(line 6) as "no such index [common_errors]." 

Two possible explanations for this error are:
1. The index `common_errors` truly does not exist or was deleted
2. We do not have the correct index name

### Cause of Error 1

In our example, the cause of the error is quite clear! We have not created an index called `common_errors` and we were trying to retrieve a document from an index that does not exist. 

Let's create an index called `common_errors`:

Syntax:
```
PUT Name-of-the-Index
```
Example:
```
PUT common_errors
```
Expected response from Elasticsearch:

Elasticsearch returns a 200-success HTTP status acknowledging that the index common_errors has been successfully created. 

![image](https://user-images.githubusercontent.com/60980933/125689096-3e42b998-1a52-4b76-b3ca-8ed438e0716a.png)

### Error 2: 405 Incorrect HTTP method for uri, allowed: [x]

Now that we have created the index `common_errors`, let's index a document!

Suppose you have remembered that you could use the HTTP verb PUT to index a document and send the following request: 
```
PUT common_errors/_doc
{
  "source_of_error": "Using the wrong syntax for PUT or POST indexing request"
}
```
Expected response from Elasticsearch:

Elasticsearch returns a 405-error along with cause of the error in the response body. This HTTP status starts with a 4XX, meaning that there was a client error with the request sent.

If you look at the response, Elasticsearch lists the reason as "Incorrect HTTP method for uri... allowed:[POST]." 

![image](https://user-images.githubusercontent.com/60980933/125692329-b78088d4-70cd-43e4-80c5-faba2d9af05e.png)

### Cause of Error 2 
This error message suggests that we used the wrong HTTP verb to index this document. 

You can use either PUT or POST HTTP verb to index a document. Each HTTP verb serves a different purpose and requires a different syntax. 

We learned about the difference between the two verbs during [Part 1: Intro to Elasticsearch and Kibana](https://github.com/LisaHJung/Part-1-Intro-to-Elasticsearch-and-Kibana) under `index a document` section.  

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

You will see that our request uses the HTTP verb PUT but it does not include the document id we want to assign to this document. If you add the id of the document to the request as seen below, you will see that the request is carried out without a hitch!

Correct example for PUT indexing request: 
```
PUT common_errors/_doc/1
{
  "source_of_error": "Using the wrong syntax for PUT or POST indexing request"
}
```

Expected response from Elasticsearch:

Elasticsearch returns a 201-success HTTP response acknowledging that document 1 has been successfully created. 

![image](https://user-images.githubusercontent.com/60980933/125692563-c379ea40-b571-4cbd-a77d-ce18bcf1a306.png)

**The HTTP verb POST is used when you want Elasticsearch to autogenerate an id for the document.** 

If this is the option you wanted, then you could fix the error message by replacing the verb PUT with POST and not including the document id after the document endpoint. 

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

Elasticsearch returns a 201-success HTTP response and autogenerates an id(line 4) for the document that was indexed.

![image](https://user-images.githubusercontent.com/60980933/125692819-fd0e12d6-befc-40b3-8686-b4fe0c6c3bb5.png)

### Error 3: 409 version conflict document exists

In part 1, we learned that when you index a document using an id of document that already exists, the original document is overwritten by the new document. To prevent this from happening, we learned how to use the `_create` endpoint!

Suppose we forgot that we indexed a document with an id of 1. Then, we use the `_create` endpoint to index a new document and try to assign it an id of 1. 

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
  "source_of_error": "Using the _create endpoint to index a new document with an id of an existing document"
}
```
Expected response from Elasticsearch:

Elasticsearch returns a 409-error along with cause of the error in the response body. This HTTP error starts with a 4XX, meaning that the request was incorrectly written. 

![image](https://user-images.githubusercontent.com/60980933/125697667-95fdbde1-6527-4076-bafb-66816ea6b494.png)

If you look at the response, Elasticsearch lists the reason(line 6) as "version conflict, document already exists (current version [1])." 

### Cause of Error 3

The indexing request includes the `_create` endpoint. The `_create` endpoint is specifically desgined to prevent a new document from being indexed with an id of an existing document. Since we already have an existing document with an id of 1, Elasticsearch throws a 409-error and does not index this document. 

Assign this document a non-existing id and Elasticsearch will carry out this request without a hitch! 
```
PUT common_errors/_create/2
{
  "source_of_error": "Using the _create endpoint to index a new document with an id of an existing document"
}
```

Expected response from Elasticsearch:

Elasticsearch returns a 201-success response acknowledging that document with an id of 2 has been created. 

![image](https://user-images.githubusercontent.com/60980933/125697823-23574d39-8d08-44a8-9c53-0582cfc56703.png)

### Error 4: 400 Unexpected Character: was expecting a comma to separate Object entries at [Source:...]

Suppose you wanted to update document 1 by adding the fields http_error and solution as seen below:

Syntax:
```
POST Name-of-the-Index/_update/id-of-the-document-you-want-to-update
{
  "doc": {
    "field1": "value",
    "field2": "value",
  }
} 
```
Request sent:
```
POST common_errors/_update/1
{
  "doc": {
    "error": "405 Method Not Allowed"
    "solution": "Look up the syntax of PUT and POST indexing requests and use the correct syntax."
  }
}
```

Expected response from Elasticsearch:

Elasticsearch returns a 400-error along with cause of the error in the response body. This HTTP error starts with a 4XX, meaning that there was a client error with the request sent.

![image](https://user-images.githubusercontent.com/60980933/125700599-a20b223d-59cb-4cc8-85f5-0c18947eb33b.png)

### Cause of Error 4 

If you look at the response, Elasticsearch lists the error type(line 12) as "json_parse_exception" and the reason(line 13) as "...was expecting comma to separate Object entries at ... line: 4]." 

In Elasticsearch, if you have multiple fields in an object("doc"), you must separate each field with a comma. The error message tells us that we need to add a comma at the end of line 4 in our request. 

Once you add the comma, send the following request:
```
POST common_errors/_update/1
{
  "doc": {
    "error": "405 Method Not Allowed",
    "solution": "Look up the syntax of PUT and POST indexing requests and use the correct syntax."
  }
}
```
Expected response from Elasticsearch:

You will see that document with an id of 1 has been successfully updated. 

![image](https://user-images.githubusercontent.com/60980933/125700703-a85dd86f-2e87-4eee-a2c3-48d253d91cd0.png)

Let's send a GET request to see the content of document 1:

```
GET common_errors/_doc/1
```

Expected response from Elasticsearch:

You will get a 200-success HTTP status and see that the fields solution and http_error have been successfully added to document 1(lines 10-12). 

![image](https://user-images.githubusercontent.com/60980933/125700802-0b7da623-8343-4f8b-a448-1837165c3e4a.png)

## Errors Associated with Sending Queries

In parts [2](https://github.com/LisaHJung/Part-2-Understanding-the-relevance-of-your-search-with-Elasticsearch-and-Kibana-) and [3](https://github.com/LisaHJung/Part-3-Running-full-text-queries-and-combined-queries-with-Elasticsearch-and-Kibana), we learned how to send queries about news headlines in our index.

As a prerequisite part of these workshops, we added a news headliens dataset to an index which we named news_headlines. 

We sent various queries to retrieve documents that match the criteria. Let's go over common errors you may encounter while working with these queries. 

### Error 5: 400 unknown key for a VALUE_x in [y].

In [part 2](https://github.com/LisaHJung/Part-2-Understanding-the-relevance-of-your-search-with-Elasticsearch-and-Kibana-), we learned how to retrieve information about documents in our index.

Syntax:
```
GET enter_name_of_the_index_here/_search
```
Example:
```
GET news_headlines/_search
```
Expected response from Elasticsearch:

Elasticsearch displays the number of documents in our index(red box) and a sample of 10 search results(blue box) by default.  

![image](https://user-images.githubusercontent.com/60980933/125364423-acfa8780-e32f-11eb-81e6-9ecd122f1f1a.png)

To improve the response speed on large datasets, Elasticsearch limits the total count to 10,000 by default(red box).  If you want the exact total number of hits, you can use the track_total_hits parameter. 

Let's say we sent the following request: 
```
GET news_headlines/_search
{
  "track total hits": true
}
```

Expected response from Elasticsearch:

Elasticsearch returns a 400-error along with cause of the error in the response body. This HTTP status starts with a 4, meaning that the request was not written correctly.

![image](https://user-images.githubusercontent.com/60980933/125364982-b2a49d00-e330-11eb-8bc1-591c5cad501e.png)

If you look at the response, Elasticsearch lists the error type(line 5) as "parsing_exception" and the reason(line 6) as "Unknown key for a VALUE_BOOLEAN in [track total hits]." 

Whenever you are in doubt about error messages, [Elastic documentation](https://www.elastic.co/guide/index.html) is a great place to start. Let's go to the documentation and search for track total hits. 

### Cause of Error 5

If you look at the documentation on [track_total_hits parameter](https://www.elastic.co/guide/en/elasticsearch/reference/7.9/search-your-data.html#track-total-hits), you will see that the parameter track_total_hits contains an underscore between each word. Our request includes space between the words instead of underscores. 

Therefore, while the correct words are all there, Elasticsearch could not recognize this parameter and threw an error. 

Let's add an underscore between each word as shown below and send the following request: 
```
GET news_headlines/_search
{
  "track_total_hits": true
}
```

Expected response from Elasticsearch: 

Elasticsearch returns 200-success response and shows the total number of hits as 200,853.

![image](https://user-images.githubusercontent.com/60980933/125365254-29da3100-e331-11eb-8528-279110cd976c.png)

### Error 6: 400 [x] query does not support [y]

Suppose you want to use the range query to pull up newsheadlines published within a specific date range and have sent the following request:
```
GET news_headlines/_search
{
  "query": {
    "range": {
      "date": 
        "gte": "2015-06-20",
        "lte": "2015-09-22"
    }
  }
}
```

Expected response from Elasticsearch: 

Elasticsearch returns a 400-error along with cause of the error in the response body. This HTTP status starts with a 4, meaning that there was a client error with the request sent.

![image](https://user-images.githubusercontent.com/60980933/125383609-e5f92300-e354-11eb-8fb4-55ac08e5ecf8.png)

If you look at the response, Elasticsearch lists the error type(line 5) as "parsing_exception" and the reason(line 6) as "[range] query does not support [date]." 

This error message sounds confusing as the range query should be able to retrieve documents that contain terms within a provided range. The date field should not have an effect on that. 

Let's check the documentation on [range query](https://www.elastic.co/guide/en/elasticsearch/reference/7.9/query-dsl-range-query.html) to see what is going on. 

### Cause of Error 6

The culprit is the range query syntax! 

Our request is missing an extra curly braces to wrap the ranges like the request shown below. Let's send the following request and see what happens:

```
GET news_headlines/_search
{
  "query": {
    "range": {
      "date": {
        "gte": "2015-06-20",
        "lte": "2015-09-22"
      }
    }
  }
}
```

Expected response from Elasticsearch:

Elasticsearch returns a 200-success status and retrieves documents whose date field value falls within the range provided in the request. 

![image](https://user-images.githubusercontent.com/60980933/125383839-4a1be700-e355-11eb-9f54-27fce956f6a3.png)

### Error 7: 400 Unexpected character...: was expecting double-quote to start field name.

In [part 2](https://github.com/LisaHJung/Part-2-Understanding-the-relevance-of-your-search-with-Elasticsearch-and-Kibana-), we learned about multi_match query. This query allows you to search for the same search terms in multiple fields at one time. 

Suppose you wanted to search for the phrase party planning in fields headline and short_description as shown below:
```
GET news_headlines/_search
{
  "query": {
    "multi_match": {
      "query": "party planning",
      "fields": [
        "headline",
        "short_description"
      ],
    }
    "type": "phrase"
  }
}
```
Expected response from Elasticsearch:

Elasticsearch returns a 400-error along with cause of the error in the response body. This HTTP status starts with a 4, meaning that there was a client error with the request sent.

![image](https://user-images.githubusercontent.com/60980933/125465154-dd336bf8-d2c5-4061-bd0f-e91bc27b088d.png)

If you look at the response, Elasticsearch lists the error type(line 5) as "json_parse_exception" and the reason(line 6) as "Unexpected character...: was expecting double-quote to start field name.. at line: 9]"

### Cause of Error 7

This error is occuring because the parameter type phrase was placed outside of curly brackets that wrap up the multi_match query. 

Move the type parameter phrase up a line as shown below and send the request:
```
GET news_headlines/_search
{
  "query": {
    "multi_match": {
      "query": "party planning",
      "fields": [
        "headline",
        "short_description"
      ],
      "type": "phrase"
    }
  }
}
```

Expected response from Elasticsearch: 

Elastcsearch returns a 200-success response and returns 6 hits with the phrase party planning in either field headline or short description or both! 

![image](https://user-images.githubusercontent.com/60980933/125466228-7125d157-04c9-4315-a242-096598a5e183.png)

### Error 8: 400 parsing_exception

When we search for something, we often ask a multi-faceted question. For example, you may want to retrieve all entertainment news headlines published on "2018-04-12". 

This question actually requires sending multiple queries in one request. One for retrieving all documents in the entertainment category. The other for retrieving documents that have been publisehd on "2018-04-12".

Let's say you are most familiar with the match query so you write the following request to accomplish this task:

```
GET news_headlines/_search
{
  "query": {
    "match": {
      "category": "ENTERTAINMENT",
      "date":"2018-04-12"
    }
  }
}
```
Expected response from Elasticsearch:

Elasticsearch returns a 400-error along with cause of the error in the response body. This HTTP status starts with a 4, meaning that something is not quite right with the request sent. 

![image](https://user-images.githubusercontent.com/60980933/125475180-2f09f7e2-ab54-4a48-a0ac-622848035584.png)

If you look at the response, Elasticsearch lists the error type(line 5) as "parsing_exception" and the reason(line 6) as "[match] query doesn't support multiple fields, found [category] and [date]." 

### Cause of Error 8
Retrieving entertainment news headlines published on "2018-04-12" requires writing two queries. One that queries for the value "Entertainment" in the category field and the other that queries documents publisehd on"2018-04-12".  

Elasticsearch throws an error because a match query can only query documents from one field. Our request tried to query multiple fields using only one match query. 

In [part 3](https://github.com/LisaHJung/Part-3-Running-full-text-queries-and-combined-queries-with-Elasticsearch-and-Kibana), we learned how to combine multiple queries into one request by using the bool query. 

With the bool query, you can combine multiple queries into one request and you can further specify boolean clauses to narrow down your search results. 

This query offers four clauses that you can choose from. These are must, must_not, should, and filter. You can mix and match any of these clauses to get the relevant search results you want. 

In our use case, we have two queries. One query that pulls up news headlines from the entertainment category. One query that pulls up news headlines written on 2018 of April 12th. 

In order for us to get relevant search results, BOTH of these queries must be true. 
So in this case we will use the must clause and include two match queries within it:
```
GET news_headlines/_search
{
  "query": {
    "bool": {
      "must": [
        {
          "match": {
            "category": "ENTERTAINMENT"
          }
        },
        {
          "match": {
            "date": "2018-04-12"
          }
        }
      ]
    }
  }
}
```
Expected response from Elastcsearch:

Elasticsearch returns a 200-sucess status and shows top 10 hits whose category field contains the value "ENTERTAINMENT" and the date field contains the value "2018-04-12".

![image](https://user-images.githubusercontent.com/60980933/125475511-cdb63c4b-8417-434f-9bb7-64361d5dc8ab.png)

## Errors Associated with Aggregations and Mapping

### Error 9: 400-error Aggregation definition for [x], expected a [y].

Suppose you want to get the summary of all categories that exist in our dataset. Since this requires summarizing your data, you decide to send the following aggregations request:

```
GET news_headlines/_search
{
  "aggs": {
    "by_category": {
      "terms": {
        "field": "category"
      }
    }
  }
}
```
Expected response from Elasticsearch:

By default, Elasticsearch returns both top 10 search hits and aggregations results. Notice that the top 10 search hits take up lines 16-168. 

![image](https://user-images.githubusercontent.com/60980933/125504763-e19596a1-cbdd-4ab0-867b-8a4fb0220fdf.png)

Let's say you are only interested in aggregations results and you remember that you can add a size parameter and set it equal to 0 to avoid fetching the hits. You send the following request to accomplish this task:
```
GET news_headlines/_search
{
  "aggs": {
    "size": 0,
    "by_category": {
      "terms": {
        "field": "category"
      }
    }
  }
}
```

Expected response from Elasticsearch: 

Elasticsearch returns a 400-error along with cause of the error in the response body. This HTTP status starts with a 4, meaning that there was a client error with the request sent.

![image](https://user-images.githubusercontent.com/60980933/125509955-a2308af6-bf01-4cd5-b7e0-64f6327a0186.png)

If you look at the response, Elasticsearch lists the error type(line 5) as "parsing_exception" and the reason(line 6) as "Aggregation definition for [size starts with a [VALUE_NUMBER], expected a [START_OBJECT]." 

Something is off with our aggregations request syntax. Let's go to the Elastic documentation for [aggregations](https://www.elastic.co/guide/en/elasticsearch/reference/7.13/search-aggregations.html) and see what we missed. 

**Screenshot from the documentation:**
![image](https://user-images.githubusercontent.com/60980933/125486600-ea21fb62-3b64-413f-9836-64b4bb4deff3.png)

### Cause of Error 9

This error is occuring because the size parameter was placed in a spot where Elasticsearch is expecting the name of the aggregations. 

If you scroll down to the `Return only aggregation results` section in the documentation, you will see that the size parameter is placed in the outside of the aggregations request as shown below. 

Screenshot from the documentation:
![image](https://user-images.githubusercontent.com/60980933/125486926-b83f052c-6291-4412-983a-2b838d5a7aee.png)

So let's place our size parameter outside of the aggregations request and set it equal to 0.
Send the following request: 
```
GET news_headlines/_search
{
  "size": 0,
  "aggs": {
    "by_category": {
      "terms": {
        "field": "category"
      }
    }
  }
}
```

Expected response from Elasticsearch:

Elasticsearch does not retrieve the top 10 hits(line 16) as intended. You can see the aggregations results, an array of categories, without having to scroll through the hits. 

![image](https://user-images.githubusercontent.com/60980933/125835366-af1dcf67-8892-4cfe-a182-1b50eb3850ce.png)

**Other uses of the size parameter**

The size parameter is not only used to omit top 10 search hits from the response. Depending on where the size  parameter is placed, you can specify the maximum number of results to return. 

For example, let's say you want to send the same aggregation request but you want to limit the number of categories returned to top 15. In that case, you would add a size parameter to the field terms and set it equal to 15 as shown below. This will return top 15 categories that contains most number of documents. 

```
GET news_headlines/_search
{
  "size": 0,
  "aggs": {
    "by_category": {
      "terms": {
        "size": 15,
        "field": "category"
      }
    }
  }
}
```

Note that there are two size parameters placed in this request. The one outside of the aggregations request is set to 0 and the size parameter set to 15 within the terms aggregation. 

Expected response from Elasticsearch:

You will see that top 10 search hits have been omitted from the response(as a result of the first size parameter) and 15 categories with most number of documents are returned in the aggregations request(as a result of the second size parameter).

![image](https://user-images.githubusercontent.com/60980933/125512772-5c795218-af7d-484f-94f8-149b96e282d8.png)

### Error 10: 400- Field [x] of type [y] is not supported for z type of aggregation

The next two errors(error 10 & 11) is related to the requests we have learned in [Part 4: Aggregations](https://github.com/LisaHJung/Part-4-Running-Aggregations-with-Elasticsearch-and-Kibana) and [Part 5: Mapping workshops](https://github.com/LisaHJung/Part-5-Understanding-Mapping-with-Elasticsearch-and-Kibana). 

During these workshops we have worked with ecommerce data. If part 4, we’ve added the ecommerce data to Elasticsearch and named the index (I named mine ecommerce_original_data). Then, we had to follow additional steps in `Set up data within Elasticsearch` section in Part 4.

To set up data within Elasticsearch, we created a new index with a new mapping(step 1). Then, we had to reindex the data from the original index to the new index we just created(step 2). 

We never covered why we had to go through these steps. It was all because of the the error message we are about to see next!

**From this point on, imagine that you had just added your dataset into ecommerce_original_data index.** 

**At this point, we have not created a new index with the customized mapping or have reindexed the original dataset into the new index.** 

Before we get started, let's get an idea of what our document looks like by sending the following request:

```
GET ecommerce_original_data/_search
```

Expected response from Elasticsearch:

Elasticsearch returns a 200-success status along with top 10 hits. Each document has fields called Description, Quantity, InvoiceNo, CustomerID, UnitPrice, Country, InvoiceDate, and StockCode. 

![image](https://user-images.githubusercontent.com/60980933/125518031-d16061ff-a15f-4a36-8ad6-86c705ed8805.png)

In part 4, we learned how to group data into buckets based on time interval. This type of aggregation request is called the `date_histogram aggregation`. 

Suppose we wanted to group our data in to 8 hour buckets and we have sent the request below. 
```
GET ecommerce_original_data/_search
{
  "size": 0,
  "aggs": {
    "transactions_by_8_hrs": {
      "date_histogram": {
        "field": "InvoiceDate",
        "fixed_interval": "8h"
      }
    }
  }
}
```
Expected response from Elasticsearch:

Elasticsearch returns a 400-error along with cause of the error in the response body. This HTTP status starts with a 4, meaning that there was a client error with the request sent.

![image](https://user-images.githubusercontent.com/60980933/125518829-1ba2cf30-368a-49dd-abfc-47c9e2521620.png)

If you look at the response, Elasticsearch lists the error type(line 5) as "illegal_argument_exception" and the reason(line 6) as "Field [InvoiceDate] of type [keyword] is not supported for aggregation [date_histogram]"

This error is different from syntax error messages we have gone over thus far. It says that the field type keyword is not supported for `date histogram aggregation`, which suggests that this error may have something to do with the mapping. 

Let's check the mapping of the ecommerce_original_data index: 

```
GET ecommerce_original_data/_mapping
```

Expected response from Elasticsearch:

You will see that the field InvoiceDate is typed as keyword. 

![image](https://user-images.githubusercontent.com/60980933/125519944-bc1ae5a0-72ad-43a1-9d0b-436c1d5b6504.png)

### Cause of Error 10

This error is occuring because the date histogram aggregation cannot be performed on a field typed as keyword. To perform a date histogram aggregation on a field, the field must be mapped so that it specifies field type as date and the format of the date.

In part 4, this is why we created a new index with the desired mapping and reindexed the data from original index to ecommerce_data index. 

**Step 1: Create a new index(ecommerce_data) with the following mapping.**
```
PUT ecommerce_data
{
  "mappings": {
    "properties": {
      "Country": {
        "type": "keyword"
      },
      "CustomerID": {
        "type": "long"
      },
      "Description": {
        "type": "text"
      },
      "InvoiceDate": {
        "type": "date",
        "format": "M/d/yyyy H:m"
      },
      "InvoiceNo": {
        "type": "keyword"
      },
      "Quantity": {
        "type": "long"
      },
      "StockCode": {
        "type": "keyword"
      },
      "UnitPrice": {
        "type": "double"
      }
    }
  }
}
```
**Side note Error with _meta**

If you were following the steps from `Setting up data within Elasticsearch` section from Part 4: Aggregations, you probably have enountered this error. 

![image](https://user-images.githubusercontent.com/60980933/125847575-06ce48c0-43df-4cc0-8130-5af7b728b18c.png)

This was due to a typo in the request where I forgot to include an underscore before meta in line 4. 
![image](https://user-images.githubusercontent.com/60980933/125846876-f1aa9a5d-0d24-457f-a763-412850ae5e5f.png)

`_meta` field is a space used to include any notes that you want to include as a reference. It can be tips about common bug fixes or any info about your app that you want to include. 

The `_meta` field is completely optional. For our use case, it is not necessary so I have removed the meta field in the repo since this issue came to my attention.

Sincere apologies to anybody who has encountered that error while following along and thank you to @radhakrishnaakamat for catching the error!! 

**Step 2: Reindex the data from original index(source) to the one you just created(destination).**
```
POST _reindex
{
  "source": {
    "index": "ecommerce_original_data"
  },
  "dest": {
    "index": "ecommerce_data"
  }
}
```
**Step 3: Send aggregations request to the new index ecommerce_data.**
```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "transactions_by_8_hrs": {
      "date_histogram": {
        "field": "InvoiceDate",
        "fixed_interval": "8h"
      }
    }
  }
}
```
Expected response from Elasticsearch:

Elasticsearch returns a 200-success response. It divides the dataset into 8 hour buckets and returns them in the response. 

![image](https://user-images.githubusercontent.com/60980933/125531405-875e198d-b229-441b-ae2e-d2428e6fbde1.png)

### Error 11: 400 Found two aggregation type definitions in [x]: y and z

One of the cool thing about Elasticsearch is that you can build any combination of aggregations to answer more complex questions. 

For example, let's say we want to get the daily revenue AND the number of unique customers per day.

This requires grouping data into daily buckets then calculating the daily revenue and the number of unique customers within each bucket. 

Let's say we wrote this request to accomplish this task:
```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "transactions_per_day": {
      "date_histogram": {
        "field": "InvoiceDate",
        "calendar_interval": "day"
      },
      "daily_revenue": {
        "sum": {
          "script": {
            "source": "doc['UnitPrice'].value * doc['Quantity'].value"
          }
        }
      },
      "number_of_unique_customers_per_day": {
        "cardinality": {
          "field": "CustomerID"
        }
      }
    }
  }
}
```

Expected response from Elasticsearch:

Elasticsearch returns a 400-error along with cause of the error in the response body. This HTTP error starts with a 4XX, meaning that there was a client error with the request sent.
![image](https://user-images.githubusercontent.com/60980933/125664685-d60b2c4c-6c2f-44d0-839f-020a53f39419.png)

### Cause of Error 11
There are two things that are wrong with this aggregation.
However, the type must be the same. Here you have a bucket aggregation(date_histogram) and metric aggregations(sum aggregation and cardinality aggregation) mixed in one aggregations request. 

We need to ... :
```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "transactions_per_day": {
      "date_histogram": {
        "field": "InvoiceDate",
        "calendar_interval": "day"
      },
      "aggs": {
        "daily_revenue": {
          "sum": {
            "script": {
              "source": "doc['UnitPrice'].value * doc['Quantity'].value"
            }
          }
        },
        "number_of_unique_customers_per_day": {
          "cardinality": {
            "field": "CustomerID"
          }
        }
      }
    }
  }
}
```
Expected response from Elasticsearch: 

Elasticsearch returns a 200-success message. It divides dataset into daily buckets and calculates number of unique customers per day as well as daily revenue for each bucket. 

![image](https://user-images.githubusercontent.com/60980933/125665068-f3fabd9a-0b72-41cd-b8d5-3238e75a594d.png)

