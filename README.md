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

## Follow the clues! 
Whenever you perform an action with Elasticsearch and Kibana, Elasticsearch responds with an HTTP status and a response body. 

![image](https://user-images.githubusercontent.com/60980933/123306018-fe0c1e00-d4dd-11eb-931d-6c8f7b90eb26.png)

The HTTP status of 201-success indicates that the document has been successfully created. The response body indicates that document with an assigned id of 1 has been created in the beginners_crash_course index. 

When the request fails, the HTTP status and response body provide valuable clues as to why the request failed so you can fix the error! 

### Common Errors
#### Unable to connect
The cluster may be down or it may be a network issue. Check the network status and cluster health to identify the problem. 
#### Connection unexpectedly closed
The node may have died or it may be a network issue. Retry your request. 
#### 5XX Error
HTTP errors that start with 5 stems from internal server error in Elasticsearch. When you see this, take a look at the Elasticsearch log and identify the problem. 
#### 4XX Error
HTTP errors that start with 4 stems from client errors. When you see this, correct the request before retrying. 

At beginner level, many error messages occur because of the way the request is written(4XX error). We will focus on 4XX errors during this workshop. 

## Approach
1. What number does the HTTP response start with(4XX? 5XX?)
2. What does the response say?

## Trip down memory lane
Throughout the series, we have learned how to send requests that are related to following topics:

1. CRUD operations
2. Queries
3. Aggregations
4. Mapping

We will revisit each topic and go over common errors you may encounter as you explore these topics further. 

### Errors associated with CRUD operations

**Error 1: 404 index_not_found_exception**

In [Part 1: Intro to Elasticsearch and Kibana](https://github.com/LisaHJung/Part-1-Intro-to-Elasticsearch-and-Kibana), we learned how to perform CRUD operations. Let's say we sent the following request to retrieve a document with an id of 1 from the index `common_errors`.

Request sent: 
```
GET common_errors/_doc/1
```

Expected response from Elasticsearch:
![image](https://user-images.githubusercontent.com/60980933/125141088-41b37a00-e0d1-11eb-957a-77bcea7207d7.png)

Elasticsearch returns a 404-error along with cause of the error in the response body. The HTTP error starts with a 4XX, meaning that there was a client error with the request sent.

When you look at the response body, Elasticsearch lists the reason(line 6) as "no such index [common_errors]." 

Two possible explanations for this error are:
1. The index common_errors truly does not exist or was deleted
2. We do not have the correct index name

**The Cause of Error 1** 

In our example, the cause of the error is quite clear! We have not created an index called common_errors and we were trying to retrieve a document from an index that does not exist. 

Let's create an index called common_errors:

Syntax:
```
PUT Name-of-the-Index
```
Example:
```
PUT common_errors
```
Expected response from Elasticsearch:

Elasticsearch returns a 200-success HTTP response acknowledging that the index common_errors has been successfully created. 

![image](https://user-images.githubusercontent.com/60980933/125141169-80e1cb00-e0d1-11eb-83e7-bdbf8f7244ea.png)

**Error 2: 405 Method Not Allowed**

Now that we have created the index common_errors, let's index a document!

Suppose you have remembered that you could use the HTTP verb PUT to index a document and send the following request: 
```
PUT common_errors/_doc
{
  "source_of_error": "Using the wrong syntax for PUT or POST indexing request"
}
```
Expected response from Elasticsearch:

Elasticsearch returns a 405-error along with cause of the error in the response body. This HTTP error starts with a 4XX, meaning that there was a client error with the request sent.

If you look at the response, Elasticsearch lists the reason as "Incorrect HTTP method for uri... allowed:[POST]." 

![image](https://user-images.githubusercontent.com/60980933/125142331-b50abb00-e0d4-11eb-92fd-c05eface3fb7.png)

**The Cause of Error 2**  
This error message suggests that we used the wrong HTTP verb to index this document. 

You can use either PUT or POST HTTP verb to index a document. Each HTTP verb serves a different purpose and requires a different syntax. 

We learned about the difference between the two during [Part 1: Intro to Elasticsearch and Kibana](https://github.com/LisaHJung/Part-1-Intro-to-Elasticsearch-and-Kibana) under index a document section.  

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

![image](https://user-images.githubusercontent.com/60980933/125142771-c4d6cf00-e0d5-11eb-9634-b0cb19e5117f.png)

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

Elasticsearch returns a 201-success HTTP response and autogenerates an id(line 4) for the document that was indexed.

![image](https://user-images.githubusercontent.com/60980933/125143039-94dbfb80-e0d6-11eb-88f3-11ac19d02b95.png)

**Error 3: 409 version_conflict_engine_exception**

When you index a document using an id of document that already exists, the existing document is overwritten by the new document. If you do not want a existing document to be overwritten, you can use the `_create` endpoint!

Suppose we forgot that we indexed a document with an id of 1. Then, we use the `_create` endpointto index the following document and assign it an id of 1. 

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

Elasticsearch returns a 409-error along with cause of the error in the response body. This HTTP error starts with a 4XX, meaning that the request was incorrectly written. 

![image](https://user-images.githubusercontent.com/60980933/125318359-009caf00-e2f7-11eb-901d-e8867db668d9.png)

If you look at the response, Elasticsearch lists the reason(line 6) as "version conflict, document already exists (current version [1])." 

**The Cause of Error 3**

The indexing request includes the `_create` endpoint. The `_create` endpoint is specifically desgined to prevent the original document from being overwritten. Since we already have an existing document with an id of 1, Elasticsearch throws a 409-error and does not index this document. 

Assign this document a non-existing id and Elasticsearch will carry out this request without a hitch! 
```
PUT common_errors/_create/2
{
  "source_of_error": "Using the _create endpoint to index a document with an existing id"
}
```

Expected response from Elasticsearch:

Elasticsearch returns a 201-success response acknowledging that document with an id of 2 has been created. 

![image](https://user-images.githubusercontent.com/60980933/125318715-583b1a80-e2f7-11eb-9694-48a9b959f792.png)

**Error 4: 400 mapper_parsing_exception**

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
    "http_error": "405 Method Not Allowed"
    "solution": "Look up the syntax of PUT and POST indexing requests and use the correct syntax."
  }
}
```

Expected response from Elasticsearch:

![image](https://user-images.githubusercontent.com/60980933/125338618-abb86300-e30d-11eb-9495-82b27037f03e.png)

Elasticsearch returns a 400-error along with cause of the error in the response body. This HTTP error starts with a 4XX, meaning that there was a client error with the request sent.

**The Cause of Error 4**

If you look at the response, Elasticsearch lists the error type(line 12) as "json_parse_exception" and the reason(line 13) as "...was expecting comma to separate Object entries at ... line: 4]." 

In Elasticsearch, if you have multiple fields in an object("doc"), you must separate each field with a comma. The error message tells us that we need to add a comma at the end of line 4 in our request. 

Once you add the comma, send the following request:
```
POST common_errors/_update/1
{
  "doc": {
    "http_error": "405 Method Not Allowed",
    "solution": "Look up the syntax of PUT and POST indexing requests and use the correct syntax."
  }
}
```
Expected response from Elasticsearch:

You will see that document with an id of 1 has been successfully updated. 

![image](https://user-images.githubusercontent.com/60980933/125338938-08b41900-e30e-11eb-863d-3e28c460836c.png)

Let's send a GET request to see the content of document 1:

```
GET common_errors/_doc/1
```

Expected response from Elasticsearch:

You will get a 200-success HTTP response and see that the fields solution and http_error have been successfully added to document 1(lines 10-12). 

![image](https://user-images.githubusercontent.com/60980933/125339166-487b0080-e30e-11eb-8c33-bfb8f0c3ad02.png)

### Errors Associated with Sending Queries

In parts [2](https://github.com/LisaHJung/Part-2-Understanding-the-relevance-of-your-search-with-Elasticsearch-and-Kibana-) and [3](https://github.com/LisaHJung/Part-3-Running-full-text-queries-and-combined-queries-with-Elasticsearch-and-Kibana), we added a dataset to an index we named news_headlines. 

We sent various queries to retrieve documents that match the criteria. Let's go over common errors you may encounter while working with these queries. 

**Error 1: 400 parsing_exception**

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

Elasticsearch will display the number of documents in our index(red box) and a sample of 10 search results(blue box) by default.  

![image](https://user-images.githubusercontent.com/60980933/125364423-acfa8780-e32f-11eb-81e6-9ecd122f1f1a.png)

To improve the response speed on large datasets, Elasticsearch limits the total count to 10,000 by default(red box).  If you want the exact total number of hits, you can use the track total hits parameter. 

Let's say we sent the following request: 
```
GET news_headlines/_search
{
  "track total hits": true
}
```

Expected response from Elasticsearch:

Elasticsearch returns a 400-error along with cause of the error in the response body. This HTTP error starts with a 4XX, meaning that the request was not written correctly.

![image](https://user-images.githubusercontent.com/60980933/125364982-b2a49d00-e330-11eb-8bc1-591c5cad501e.png)

If you look at the response, Elasticsearch lists the error type(line 5) as "parsing_exception" and the reason(line 6) as "Unknown key for a VALUE_BOOLEAN in [track total hits]." 

Whenever you are in doubt about error messages, [Elastic documentation](https://www.elastic.co/guide/index.html) is a great place to start. Let's go to the documentation and search for track total hits. 

If you look at the documentation on [track total hits parameter](https://www.elastic.co/guide/en/elasticsearch/reference/7.9/search-your-data.html#track-total-hits), you will see that the parameter track_total_hits contains an underscore between each word. Our request includes space between the words instead of underscores.Therefore, while the correct terms are all there, Elasticsearch could not recognize this parameter and threw an error. 

Let's add an underscore between each term as shown below and send the following request: 
```
GET news_headlines/_search
{
  "track_total_hits": true
}
```

Expected response from Elasticsearch: 

Elasticsearch returns 200-success response and shows the total number of hits as 200,853.

![image](https://user-images.githubusercontent.com/60980933/125365254-29da3100-e331-11eb-8528-279110cd976c.png)

**Error 2: 400 parsing_exception**

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

Elasticsearch returns a 400-error along with cause of the error in the response body. This HTTP error starts with a 4XX, meaning that there was a client error with the request sent.

![image](https://user-images.githubusercontent.com/60980933/125383609-e5f92300-e354-11eb-8fb4-55ac08e5ecf8.png)

If you look at the response, Elasticsearch lists the error type(line 5) as "parsing_exception" and the reason(line 6) as "[range] query does not support [date]." 

This error message sounds confusing as the range query should be able to retrieve documents that contain terms within a provided range. The date field should not have an effect on that. 

Let's check the documentation on the [Range query](https://www.elastic.co/guide/en/elasticsearch/reference/7.9/query-dsl-range-query.html) to see what is going on. 

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

Elasticsearch returns a 200 response and retrieves documents whose d fall in the range provided in the request. 

![image](https://user-images.githubusercontent.com/60980933/125383839-4a1be700-e355-11eb-9f54-27fce956f6a3.png)

**Error 3: 400 json_parse_exception**

Suppose you wanted to search for the phrase party planning in multiple fields as shown below:
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
Elasticsearch returns a 400-error along with cause of the error in the response body. This HTTP error starts with a 4XX, meaning that there was a client error with the request sent.

![image](https://user-images.githubusercontent.com/60980933/125465154-dd336bf8-d2c5-4061-bd0f-e91bc27b088d.png)

If you look at the response, Elasticsearch lists the error type(line 5) as "json_parse_exception" and the reason(line 6) as ""Unexpected character...: was expecting double-quote to start field name.. at line: 9]"

This error is occuring because the parameter type phrase should be included within the multi_match bracket.  

Move the type parameter up a line as shown below and send the request:
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

Elastcsearch returns a 200-success response and returns 6 hits with the phrase party planning in either fields headline or short description or both! 

![image](https://user-images.githubusercontent.com/60980933/125466228-7125d157-04c9-4315-a242-096598a5e183.png)

***Error 2*** 
```
GET news_headlines/_search
{
  "query": {
    "match": {
      "headline": "Khloe Kardashian Kendall Jenner",
      "operator": "and"
    }
  }
}
```

Elasticsearch returns a 400-error along with cause of the error in the response body. This HTTP error starts with a 4XX, meaning that there was a client error with the request sent.

If you look at the response, Elasticsearch lists the error type(line 11) as "parsing_exception" and the reason(line 12) as "[match] query doesn't support multiple fields, found [headline] and [operator]." 

This error is occuring because the match query only supports one field. In order to get around this issue, add a query clause under the match cluase and include the operator as shown below. 

```
GET news_headlines/_search
{
  "query": {
    "match": {
      "headline": {
        "query": "Khloe Kardashian Kendall Jenner",
        "operator": "and"
      }
    }
  }
}
```

Expected response from Elasticsearch:
![image](https://user-images.githubusercontent.com/60980933/124653511-9d280280-de5a-11eb-8856-1df03a832bac.png)
It pulls up one hit that contains all four search terms in the query. 

minimum should match is included here. 

**Error 4: 400 parsing_exception**

Let's say you want to ask a multi-faceted question that requires sending multiple queries in one request. You are most familiar with the match query so you write the following request to retrieve entertainment news headlines publisehd on "2018-04-12". 

Request sent:
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

Elasticsearch returns a 400-error along with cause of the error in the response body. This HTTP error starts with a 4XX, meaning that there was a client error with the request sent.

![image](https://user-images.githubusercontent.com/60980933/125475180-2f09f7e2-ab54-4a48-a0ac-622848035584.png)

If you look at the response, Elasticsearch lists the error type(line 5) as "parsing_exception" and the reason(line 6) as "[match] query doesn't support multiple fields, found [category] and [date]." 

Elasticsearch throws an error because a match query can only query documents from one field. Retrieving entertainment news headlines published on "2018-04-12" requires writing two queries. One that queries for the value "Entertainment" in the category field and the other that queries documents publisehd on"2018-04-12".  

In [part 3](https://github.com/LisaHJung/Part-3-Running-full-text-queries-and-combined-queries-with-Elasticsearch-and-Kibana), we learned how to combine multiple queries into one request by using the bool query. Since both queries must be true for the document to be considered as a hit, we use the must clause and include two match queries within it:
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

Elasticsearch returns a 200-sucess response and shows top 10 hits with documents whose category field contains the value called "Entertainment" and the date field contains the value "2018-04-12".

![image](https://user-images.githubusercontent.com/60980933/125475511-cdb63c4b-8417-434f-9bb7-64361d5dc8ab.png)

### Errors Associated with Aggregations

**Error 1: 400-error parsing_exception**

Suppose you want to get the summary of all categories that exist in our dataset. Since this requires summarizing your data, you decide to send an aggregations request.

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
By default, Elasticsearch returns both top 10 hits and aggregations results. Notice that Top 10 search hits take up lines 16-168. If you are only interested in aggreation results you can add a size parameter and set it equal to 0 to avoid fetching the hits.

![image](https://user-images.githubusercontent.com/60980933/125504763-e19596a1-cbdd-4ab0-867b-8a4fb0220fdf.png)

Let's say you sent the following request to accomplish this task:
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

Elasticsearch returns a 400-error along with cause of the error in the response body. This HTTP error starts with a 4XX, meaning that there was a client error with the request sent.

![image](https://user-images.githubusercontent.com/60980933/125486214-f176f1f5-867b-44cf-b5a0-30e34cc2a5b4.png)

If you look at the response, Elasticsearch lists the error type(line 5) as "parsing_exception" and the reason(line 6) as "Aggregation definition for [size starts with a [VALUE_NUMBER], expected a [START_OBJECT]." 

Something is off with our aggregations request syntax. Let's go to the Elastic documentation for [aggregations](https://www.elastic.co/guide/en/elasticsearch/reference/7.13/search-aggregations.html) and see what we missed. 

Screenshot from the documentation:
![image](https://user-images.githubusercontent.com/60980933/125486600-ea21fb62-3b64-413f-9836-64b4bb4deff3.png)

This error is occuring because the size parameter was placed in a spot where Elasticsearch is expecting the name of the aggregations. 

If you scroll down to the `Return only aggregation results` section in the documentation, you will see that the size parameter is placed in the outermost bracket as shown below. 

![image](https://user-images.githubusercontent.com/60980933/125486926-b83f052c-6291-4412-983a-2b838d5a7aee.png)

So let's place our size parameter in the outermost bracket and set it equal to 0 and send the request: 
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

Elasticsearch does not retrieve the top 10 hits(line 16) and you can see the aggregations results, an array of categories, without having to scroll through the hits. 

![image](https://user-images.githubusercontent.com/60980933/124628166-74454480-de3d-11eb-9a8d-adc9cf80673e.png)

**Other uses of the size parameter**

The size parameter is not only used to omit top 10 search hits from the response. It is also used to specify the maximum number of hits to return.Depending on where the size parameter is placed, you can specify the maximum number of hits to return. 

For example, let's say you want to send the same aggregation request but you want to limit the number of categories returned to top 15. In that case, you would add a size parameter to the field terms and set it equal to 15 as shown below. This will return top 15 categories that contains most number of documents. 

Note that there are two size parameters placed in this request. One in the outermost part of the request set to 0 and the size parameter set to 15 within the terms aggregation. 

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

Expected response from Elasticsearch:

You will see that top 10 search hits have been omitted from the response and 15 categories with most number of documents are returned in the aggregations request.

![image](https://user-images.githubusercontent.com/60980933/124629000-3b599f80-de3e-11eb-8886-0d4ef541f846.png)

**Error 400- illegal argument exception**
```
GET eo/_search
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

Elasticsearch returns a 400-error along with cause of the error in the response body. This HTTP error starts with a 4XX, meaning that there was a client error with the request sent.

If you look at the response, Elasticsearch lists the error type(line 5) as "illegal_argument_exception" and the reason(line 6) as "Field [InvoiceDate] of type [keyword] is not supported for aggregation [date_histogram]"

![image](https://user-images.githubusercontent.com/60980933/124955596-6544ca80-dfd4-11eb-8c8f-532f3ac2a09f.png)

It refers to the type of the field(mapping) so let's take a look at the mapping. 
```
GET eo/_mapping
```

Expected response from Elasticsearch:
![image](https://user-images.githubusercontent.com/60980933/124956943-c8832c80-dfd5-11eb-8958-be6d18705199.png)

![image](https://user-images.githubusercontent.com/60980933/124955596-6544ca80-dfd4-11eb-8c8f-532f3ac2a09f.png)

This error is occuring because the date histogram aggregation cannot be performed on a field typed as keyword. It must have type as date and the format of the date must be specified. 

Create a new index(ecommerce_data) with the following mapping.
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
```
POST _reindex
{
  "source": {
    "index": "eo"
  },
  "dest": {
    "index": "ecommerce_data"
  }
}
```

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
**Errors 400 json_parse_exception**
```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "transactions_per_custom_price_ranges": {
      "range": {
        "field": "UnitPrice",
        "ranges": 
          {
            "to": 50
          },
          {
            "from": 50,
            "to": 200
          },
          {
            "from": 200
          }
      }
    }
  }
}
```
Expected response from Elasticsearch: 
![image](https://user-images.githubusercontent.com/60980933/124959022-05502300-dfd8-11eb-96e9-210119cf2144.png)

Elasticsearch returns a 400-error along with cause of the error in the response body. This HTTP error starts with a 4XX, meaning that there was a client error with the request sent.

If you look at the response, Elasticsearch lists the error type(line 5) as "json_parse_exception" and the reason(line 5) as "Unexpected character... was expecting double-quote to start field name at ... line: 11, column: 12]"

Hm... have no idea how to fix this. Go to the docs and search for range aggregation. 
Ranges must be contained within square brackets. 
```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "transactions_per_custom_price_ranges": {
      "range": {
        "field": "UnitPrice",
        "ranges": [
          {
            "to": 50
          },
          {
            "from": 50,
            "to": 200
          },
          {
            "from": 200
          }
        ]
      }
    }
  }
}
```
Expected response from Elasticsearch:
![image](https://user-images.githubusercontent.com/60980933/124960941-1a2db600-dfda-11eb-954c-23db5ebcd41c.png)

```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "5_customers_with_lowest_number_of_transactions": {
      "terms": {
        "field": "CustomerID",
        "size": 5,
        "order": 
          "_count": "asc"
      }
    }
  }
}
```
Expected response from Elasticsearch:
![image](https://user-images.githubusercontent.com/60980933/124961200-67aa2300-dfda-11eb-839f-488415572f03.png)

Elasticsearch returns a 400-error along with cause of the error in the response body. This HTTP error starts with a 4XX, meaning that there was a client error with the request sent.

If you look at the response, Elasticsearch lists the error type(line 5) as "x_content_parse_exception" and the reason(line 6) as "[9:11] [terms] order doesn't support values of type: VALUE_STRING"

This error is occuring because you must put the curly brackets around order field. 
```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "5_customers_with_lowest_number_of_transactions": {
      "terms": {
        "field": "CustomerID",
        "size": 5,
        "order": {
          "_count": "asc"
        }
      }
    }
  }
}
```
Expected response from Elasticsearch:
![image](https://user-images.githubusercontent.com/60980933/124961559-dedfb700-dfda-11eb-86fe-53b0f8ba13ad.png)
It returns top 5 customers with the lowest transactions. 

Combined aggregation:
```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "transactions_per_day": {
      "date_histogram": {
        "field": "InvoiceDate",
        "calendar_interval": "day",
        "order": {
          "daily_revenue": "desc"
        }
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
![image](https://user-images.githubusercontent.com/60980933/124964478-506d3480-dfde-11eb-85b2-d0a236bb0832.png)

You can have multiple aggregations request within one aggregation. however, the type must be the same. Here you have a bucket aggreagation(date_historam) and metric aggregations(sum aggregation and cardinality aggregation) mixed in one aggregations request. 

```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "transactions_per_day": {
      "date_histogram": {
        "field": "InvoiceDate",
        "calendar_interval": "day",
        "order": {
          "daily_revenue": "desc"
        }
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
![image](https://user-images.githubusercontent.com/60980933/124964180-f10f2480-dfdd-11eb-8154-e2b8a418f047.png)

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



