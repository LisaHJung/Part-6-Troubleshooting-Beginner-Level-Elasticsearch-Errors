# Beginner's Crash Course to Elastic Stack Series
## Part-6-Troubleshooting-Beginner-Level-Elasticsearch-Errors

Welcome to the Beginner's Crash Course to Elastic Stack!

This repo contains all resources shared during Part 6: Troubleshooting Beginner-Level Elasticsearch Errors!

Throughout the series, we have learned about CRUD operations, fine tuning the relevance of your search, full text search, aggregation, and mapping. 

As you continue your journey with Elasticsearch, you will inevitably encounter some common errors associated with the topics we have covered in the series. 

Learn how to troubleshoot these pesky errors so you can get unstuck! 

## Resources

[Table of Contents: Beginner's Crash Course to Elastic Stack](https://github.com/LisaHJung/Beginners-Crash-Course-to-the-Elastic-Stack-Series): 

This workshop is a part of the Beginner's Crash Course to Elastic Stack series. Check out this table contents to access all the workshops in the series thus far. This table will continue to get updated as more workshops in the series are released! 

[Free Elastic Cloud Trial](https://ela.st/elastic-beginners)

[Instructions](https://dev.to/lisahjung/beginner-s-guide-to-setting-up-elasticsearch-and-kibana-with-elastic-cloud-1joh) on how to access Elasticsearch and Kibana on Elastic Cloud

[Instructions](https://dev.to/elastic/downloading-elasticsearch-and-kibana-macos-linux-and-windows-1mmo) for downloading Elasticsearch and Kibana

[Presentation slides](https://github.com/LisaHJung/Part-6-Troubleshooting-beginner-level-Elasticsearch-Errors/blob/main/Part%206_%20Troubleshooting%20Beginner%20Level%20Errors.pdf)

[YouTube Playlist of Beginner's Crash Course to Elastic Stack](https://www.youtube.com/watch?v=gS_nHTWZEJ8&list=PL_mJOmq4zsHZYAyK606y7wjQtC0aoE6Es): 

Want to watch all the workshops in the series? Subscribe to the Beginner's Crash Course to Elastic Stack YouTube Playlist! 

[Season 2: Topics Survey](https://ela.st/beginners-season-2-topics)

I wanted to make the content more digestible for all of you! 

Starting with season 2, I will discontinue holding live hour-long workshops and start uploading short clips(10 min or less) on [YouTube Playlist of Beginner's Crash Course to Elastic Stack](https://www.youtube.com/watch?v=gS_nHTWZEJ8&list=PL_mJOmq4zsHZYAyK606y7wjQtC0aoE6Es)

This series is created for YOU! Please let me know what you would like to learn in season 2 by submitting your preference in the survey. 

I will create content on most requested topics along with other helpful topics for beginners! 

## Want To Troubleshoot Your Errors? Follow The Clues! 

Whenever you perform an action with Elasticsearch and Kibana, Elasticsearch responds with an HTTP status and a response body. 

The request below asks Elasticsearch to index a document and assign it an id of 1. 

![image](https://user-images.githubusercontent.com/60980933/125672998-51ecff9f-50cb-488a-ac02-374ed3a8eb6e.png)

The HTTP status of 201-success indicates that the document has been successfully created. The response body indicates that document with an assigned id of 1 has been created in the beginners_crash_course index. 

As we work with Elasticsearch, we will inevitably encounter error messages. 

![image](https://user-images.githubusercontent.com/60980933/126246186-aabff03e-445b-4ff6-848b-5454085c02d3.png)

When this happens, the HTTP status and the response body will provide valuable clues about why the request failed! 

### Common Errors

Here are some common errors that you may encounter as you work with Elasticsearch. 

#### Unable to connect
The cluster may be down or it may be a network issue. Check the network status and cluster health to identify the problem. 
#### Connection unexpectedly closed
The node may have died or it may be a network issue. Retry your request. 
#### 5XX Errors
Errors with an HTTP status starting with 5 stems from internal server error in Elasticsearch. When you see this error, take a look at the Elasticsearch log and identify the problem. 
#### 4XX Errors
Errors with an HTTP status starting with 4 stems from client errors. When you see this error, correct the request before retrying. 

As beginners, we are still familiarizing ourselves with the rules and syntax required to communicate with Elasticsearch. Majority of the error messages we encouter are likely caused by the mistakes we make while writing our requests(4XX errors).   

**To strengthen our understanding of the requests we've learned throughout the series, we will only focus on 4XX errors during this workshop.** 

## Thought Process For Troubleshooting Errors
1. What number does the HTTP status start with(4XX? 5XX?)
2. What does the response say? Always read the full message!
3. Use the [Elasticsearch documentation](https://www.elastic.co/guide/index.html) as your guide. Compare your request with the example from the documentation. Identify the mistake and make appropriate changes.

**At times, you will encounter error messages that are not very helpful. We will go over a couple of these and see how we can troubleshoot these types of errors.**

## Trip Down Memory Lane
Throughout the series, we learned how to send requests related to following topics:

1. CRUD operations
2. Queries
3. Aggregations
4. Mapping

We will revisit each topic and troubleshoot common errors you may encounter as you explore each topic. 

## Errors Associated With CRUD Operations

### Error 1: 404 No such index[x]

In [Part 1: Intro to Elasticsearch and Kibana](https://github.com/LisaHJung/Part-1-Intro-to-Elasticsearch-and-Kibana), we learned how to perform CRUD operations. Let's say we've sent the following request to retrieve a document with an id of 1 from the `common_errors` index.

Request sent: 
```
GET common_errors/_doc/1
```

Expected response from Elasticsearch:

Elasticsearch returns a 404-error along with the cause of the error in the response body. The HTTP status starts with a 4, meaning that there was a client error with the request sent.

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

Elasticsearch returns a 200-success HTTP status acknowledging that the index `common_errors` has been successfully created. 

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

Elasticsearch returns a 405-error along with the cause of the error in the response body. This HTTP status starts with a 4, meaning that there was a client error with the request sent.

If you look at the response, Elasticsearch lists the reason as "Incorrect HTTP method for uri... and method: [PUT], allowed:[POST]." 

![image](https://user-images.githubusercontent.com/60980933/126228111-3f551439-1af7-49d8-a5c9-1f141afb4325.png)

### Cause of Error 2 
This error message suggests that we used the wrong HTTP verb to index this document. 

You can use either PUT or POST HTTP verb to index a document. Each HTTP verb serves a different purpose and requires a different syntax. 

We learned about the difference between the two verbs during [Part 1: Intro to Elasticsearch and Kibana](https://github.com/LisaHJung/Part-1-Intro-to-Elasticsearch-and-Kibana) under the `Index a document` section.  

**When indexing a document, HTTP verb PUT or POST can be used.** 

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

If this is the option you wanted, you could fix the error message by replacing the verb PUT with POST and not including the document id after the document endpoint. 

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

### Error 3: 400 Unexpected Character: was expecting a comma to separate Object entries at [Source: ...] line: x

Suppose you wanted to update document 1 by adding the fields `error` and `solution` as seen below:

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

Elasticsearch returns a 400-error along with the cause of the error in the response body. This HTTP error starts with a 4, meaning that there was a client error with the request sent.

![image](https://user-images.githubusercontent.com/60980933/125700599-a20b223d-59cb-4cc8-85f5-0c18947eb33b.png)

### Cause of Error 3 

If you look at the response, Elasticsearch lists the error type(line 12) as "json_parse_exception" and the reason(line 13) as "...was expecting comma to separate Object entries at ... line: 4]." 

In Elasticsearch, if you have multiple fields("errors" and "solution") in an object("doc"), you must separate each field with a comma. The error message tells us that we need to add a comma at the end of line 4 in our request. 

Add the comma at the end of line 4 as shown below and send the following request:
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

## Errors Associated With Sending Queries

In parts [2](https://github.com/LisaHJung/Part-2-Understanding-the-relevance-of-your-search-with-Elasticsearch-and-Kibana-) and [3](https://github.com/LisaHJung/Part-3-Running-full-text-queries-and-combined-queries-with-Elasticsearch-and-Kibana), we learned how to send queries about news headlines in our index.

As a prerequisite part of these workshops, we added a news headlines dataset to an index we named as `news_headlines`. 

We sent various queries to retrieve documents that match the criteria.  Let's go over common errors you may encounter while working with these queries. 

### Error 4: 400 [x] query does not support [y]

Suppose you want to use the range query to pull up news headlines published within a specific date range. You have sent the following request:
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

Elasticsearch returns a 400-error along with the cause of the error in the response body. This HTTP status starts with a 4, meaning that there was a client error with the request sent.

![image](https://user-images.githubusercontent.com/60980933/125383609-e5f92300-e354-11eb-8fb4-55ac08e5ecf8.png)

If you look at the response, Elasticsearch lists the error type(line 5) as "parsing_exception" and the reason(line 6) as "[range] query does not support [date]." 

This error message is misleading as the range query should be able to retrieve documents that contain terms within a provided range. It should not matter that you have requestsed to run a range query against the date field. 

Let's check the [Elastic documentation on the range query](https://www.elastic.co/guide/en/elasticsearch/reference/7.9/query-dsl-range-query.html) to see what is going on. 

**Screenshot from the documentation:**
![image](https://user-images.githubusercontent.com/60980933/126231182-12f7e6d6-a29d-4076-8b12-4d8bb5f83b86.png)

### Cause of Error 4

The culprit is the range query syntax! 

Our request is missing curly brackets around the bounds of the range query. Let's add the curly brackets as shown below and send the following request:

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

Elasticsearch returns a 200-success status and retrieves news headlines that were published between the specified date range. 

![image](https://user-images.githubusercontent.com/60980933/125383839-4a1be700-e355-11eb-9f54-27fce956f6a3.png)

### Error 5: 400 Unexpected character...: was expecting double-quote to start field name.

In [Part 2](https://github.com/LisaHJung/Part-2-Understanding-the-relevance-of-your-search-with-Elasticsearch-and-Kibana-), we learned about the `multi_match` query. This query allows you to search for the same search terms in multiple fields at one time. 

Suppose you wanted to search for the phrase "party planning" in fields `headline` and `short_description` as shown below:
```
GET news_headlines/_search
{
  "query": {
    "multi_match": {
      "query": "party planning",
      "fields": [
        "headline",
        "short_description"
      ]
    },
    "type": "phrase"
  }
}

```
Expected response from Elasticsearch:

Elasticsearch returns a 400-error along with the cause of the error in the response body. This HTTP status starts with a 4, meaning that something isn't quite right with the request sent.

![image](https://user-images.githubusercontent.com/60980933/126010686-324d860f-f532-4432-81f3-71894a19ee6c.png)

If you look at the response, Elasticsearch lists the error type(line 5) as "parsing_exception" and the reason(line 6) as "[multi_match] malformed query, expected [END_OBJECT] but found [FIELD_NAME]."

### Cause of Error 5

This error is occuring because the parameter type:phrase was placed outside of the `multi_match` query. 

Move the type parameter up a line and move the comma from line 10 to line 9 as shown below and send the request:
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

Elastcsearch returns a 200-success response. All hits contains the phrase party planning in either field `headline` or `hort description` or both! 

![image](https://user-images.githubusercontent.com/60980933/125466228-7125d157-04c9-4315-a242-096598a5e183.png)

### Error 6: 400 parsing_exception

When we search for something, we often ask a multi-faceted question. For example, you may want to retrieve  entertainment news headlines published on "2018-04-12." 

This question actually requires sending multiple queries in one request:

1. A query that retrieves documents from the entertainment category 
2. A query that retrieves documents that were published on "2018-04-12"

Let's say you are most familiar with the `match query` so you write the following request:

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

Elasticsearch returns a 400-error along with the cause of the error in the response body. This HTTP status starts with a 4, meaning that something is off with the query syntax.

![image](https://user-images.githubusercontent.com/60980933/125475180-2f09f7e2-ab54-4a48-a0ac-622848035584.png)

If you look at the response, Elasticsearch lists the error type(line 5) as "parsing_exception" and the reason(line 6) as "[match] query doesn't support multiple fields, found [category] and [date]." 

### Cause of Error 6

Elasticsearch throws an error because a` match query` can query documents from only one field. Our request tried to query multiple fields using only one `match query`. 

**Bool Query**

In [Part 3](https://github.com/LisaHJung/Part-3-Running-full-text-queries-and-combined-queries-with-Elasticsearch-and-Kibana), we learned how to combine multiple queries into one request by using the bool query. 

With the bool query, you can combine multiple queries into one request and you can further specify boolean clauses to narrow down your search results. 

This query offers four clauses that you can choose from:
1. must
2. must_not
3. should
4. filter 

You can mix and match any of these clauses to get the relevant search results you want. 

In our use case, we have two queries:

1. A query that retrieves documents from the entertainment category 
2. A query that retrieves documents that were published on "2018-04-12"

The news headlines could be filtered into yes or no category:

1. Is the news headline from "ENTERTAINMENT" category? **yes** or no  
2. Was the news headline published on 2018-04-12? **yes** or no

Therefore, we can use the filter clause and include two match queries within it:

```
GET news_headlines/_search
{
  "query": {
    "bool": {
      "filter": [
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

Elasticsearch returns a 200-sucess status and shows top 10 hits whose category field contains the value "ENTERTAINMENT" and the date field contains the value of "2018-04-12".

![image](https://user-images.githubusercontent.com/60980933/126013105-1ee3f92b-561b-49f5-a37e-0550b8515279.png)

## Errors Associated With Aggregations and Mapping

Suppose you want to get the summary of categories that exist in our dataset. Since this requires summarizing your data, you decide to send the following aggregations request:

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

### Error 7: 400 Aggregation definition for [x], expected a [y].

Let's say you're only interested in aggregations results.

You remember that you can add a size parameter and set it equal to 0 to avoid fetching the hits. You send the following request to accomplish this task:
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

Elasticsearch returns a 400-error along with the cause of the error in the response body. This HTTP status starts with a 4, meaning that there was a client error with the request sent.

![image](https://user-images.githubusercontent.com/60980933/125509955-a2308af6-bf01-4cd5-b7e0-64f6327a0186.png)

If you look at the response, Elasticsearch lists the error type(line 5) as "parsing_exception" and the reason(line 6) as "Aggregation definition for [size starts with a [VALUE_NUMBER], expected a [START_OBJECT]." 

Something is off with our aggregations request syntax.  Let's go to the [Elastic documentation on aggregations](https://www.elastic.co/guide/en/elasticsearch/reference/7.13/search-aggregations.html) and see what we missed. 

**Screenshot from the documentation:**
![image](https://user-images.githubusercontent.com/60980933/125486600-ea21fb62-3b64-413f-9836-64b4bb4deff3.png)

### Cause of Error 7

This error is occuring because the size parameter was placed in a spot where Elasticsearch is expecting the name of the aggregations. 

If you scroll down to the `Return only aggregation results` section in the documentation, you will see that the size parameter is placed in the outside of the aggregations request as shown below. 

**Screenshot from the documentation:**
![image](https://user-images.githubusercontent.com/60980933/125486926-b83f052c-6291-4412-983a-2b838d5a7aee.png)

So let's place our size parameter outside of the aggregations request and set it equal to 0 as shown below. Send the following request: 
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

Elasticsearch does not retrieve the top 10 hits(line 16) as intended. You can see the aggregations results(an array of categories) without having to scroll through the hits. 

![image](https://user-images.githubusercontent.com/60980933/125835366-af1dcf67-8892-4cfe-a182-1b50eb3850ce.png)

### Error 8: 400 Field [x] of type [y] is not supported for z type of aggregation

The next two errors(error 10 & 11) are related to the requests we have learned in [Part 4: Aggregations](https://github.com/LisaHJung/Part-4-Running-Aggregations-with-Elasticsearch-and-Kibana) and [Part 5: Mapping](https://github.com/LisaHJung/Part-5-Understanding-Mapping-with-Elasticsearch-and-Kibana) workshops. 

During these workshops, we have worked with ecommerce dataset. 

In Part 4, we’ve added the ecommerce dataset to Elasticsearch and named the index (I named mine `ecommerce_original_data`). Then, we had to follow additional steps in `Set up data within Elasticsearch` section in Part 4 repo.

**Screenshot from Part 4 Repo:**

To set up data within Elasticsearch, we implemented the following steps:
![image](https://user-images.githubusercontent.com/60980933/126377626-92ecc3c6-62c9-4bed-8424-61c2f0b12e45.png)
We never covered why we had to go through these steps. It was all because of the the error message we are about to see next!

**From this point on, imagine that you had just added your dataset into ecommerce_original_data index. We haven't completed steps 1 and 2.** 

In Part 4, we learned how to group data into buckets based on time interval. This type of aggregation request is called the `date_histogram aggregation`. 

Suppose we wanted to group our data into 8 hour buckets and have sent the request below:
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

Elasticsearch returns a 400-error along with the cause of the error in the response body. This HTTP status starts with a 4, meaning that there was a client error with the request sent.

![image](https://user-images.githubusercontent.com/60980933/125518829-1ba2cf30-368a-49dd-abfc-47c9e2521620.png)

If you look at the response, Elasticsearch lists the error type(line 5) as "illegal_argument_exception" and the reason(line 6) as "Field [InvoiceDate] of type [keyword] is not supported for aggregation [date_histogram]."

This error is different from syntax error messages we have gone over thus far. It says that the field type keyword is not supported for `date histogram aggregation`, which suggests that this error may have something to do with the mapping. 

Let's check the mapping of the `ecommerce_original_data` index: 

```
GET ecommerce_original_data/_mapping
```

Expected response from Elasticsearch:

You will see that the field InvoiceDate is typed as keyword. 

![image](https://user-images.githubusercontent.com/60980933/125519944-bc1ae5a0-72ad-43a1-9d0b-436c1d5b6504.png)

### Cause of Error 8

Let's take a look at the [Elastic documentation on date_histogram aggregations](https://www.elastic.co/guide/en/elasticsearch/reference/7.13/search-aggregations-bucket-datehistogram-aggregation.html).

**Screenshot from documentation:**

![image](https://user-images.githubusercontent.com/60980933/126014337-d596889c-e3c8-457d-8017-6364b372d836.png)

This error is occuring because the `date_histogram aggregation` cannot be performed on a field typed as keyword. To perform a `date_histogram aggregation` on a field, the field must be mapped as field type date. 

**Remember, you cannot change the mapping of the existing field!** 

**The only way you can accomplish this is to:**

Step 1: Create a new index with the desired mapping 

Step 2: Reindex data from the original index to the new one

Step 3: Send the `date_histogram aggregations` request to the *new index*

In Part 4, this is why we carried out steps 1 and 2! 

**Step 1: Create a new index(ecommerce_data) with the following mapping**
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

**Side note about error associated with the `_meta` field**

If you were following the steps from `Setting up data within Elasticsearch` section from Part 4, you probably have enountered the following error: 

![image](https://user-images.githubusercontent.com/60980933/125847575-06ce48c0-43df-4cc0-8130-5af7b728b18c.png)

This was due to a typo in the request where I forgot to include an underscore before meta in line 4. 

![image](https://user-images.githubusercontent.com/60980933/125846876-f1aa9a5d-0d24-457f-a763-412850ae5e5f.png)

`_meta` field is a space used to include any notes that you want as a reference. It can be tips about common bug fixes or info about your app that you want to include. 

The `_meta` field is completely optional. For our use case, it is not necessary so I have removed the `_meta` field from Part 4 repo since this issue came to my attention.

Sincere apologies to anybody who has encountered that error while following along and thank you to @radhakrishnaakamat for catching the error!! 

**Side note about adding the format of the InvoiceDate field**

Let's look at the date format of the field InvoiceDate:
```
GET ecommerce_original_data/_search 
```
Expected response from Elasticsearch:
![image](https://user-images.githubusercontent.com/60980933/126386813-f8ed5939-a93f-4883-8f11-15f649f70368.png)

The format of the InvoiceDate is "M/d/yyyy H:m".

By default, Elasticsearch is configured to recognize iso8601 date format(ex. 2021-07-16T17:12:56.123Z).

If the date format in your dataset differs from the iso8601 format, Elasticsearch will not recognize it and throw an error. 

In order to prevent this from happening, specify the date format of the InvoiceDate field("format": "M/d/yyyy H:m") within the mapping. The symbols used in date format was formed using this [documentation](https://docs.oracle.com/javase/8/docs/api/java/time/format/DateTimeFormatter.html). 

![image](https://user-images.githubusercontent.com/60980933/126386037-7bce3034-a9c9-4841-bf41-7cf67f259fcc.png)
![image](https://user-images.githubusercontent.com/60980933/126417282-73f5a571-701f-4881-a5c9-f7cb953ccd5e.png)
![image](https://user-images.githubusercontent.com/60980933/126423539-8da82a73-10bb-46cc-86d7-98ab31bea30b.png)

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
**Step 3: Send the date_histogram aggregations request to the new index(ecommerce_data).**
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

### Error 9: 400 Found two aggregation type definitions in [x]: y and z

One of the cool things about Elasticsearch is that you can build any combination of aggregations to answer more complex questions. 

For example, let's say we want to get the daily revenue and the number of unique customers per day.

![image](https://user-images.githubusercontent.com/60980933/126397086-b2910304-c065-4280-aa16-21ae566cbd7a.png)
![image](https://user-images.githubusercontent.com/60980933/126397109-29416d46-c960-42f9-b6dd-c19f825c9b70.png)

Let's say we wrote the following request to accomplish this task:
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

Elasticsearch returns a 400-error along with the cause of the error in the response body. This HTTP error starts with a 4, meaning that there was a client error with the request sent.
![image](https://user-images.githubusercontent.com/60980933/125664685-d60b2c4c-6c2f-44d0-839f-020a53f39419.png)

### Cause of Error 9
This error is occurring because the structure of the aggregations request is incorrect.

We group data into daily buckets. Within each bucket, we calculate the daily revenue and unique nubmer of customers per day.

So this request contains aggregations(pink brackets) within aggregations(blue brackets). 
![image](https://user-images.githubusercontent.com/60980933/126399284-d34b63cf-72bc-4309-a9d8-3d24efa90ab7.png)

The following demonstrates the correct aggregations request structure:
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

Elasticsearch returns a 200-success message. It groups the dataset into daily buckets and calculates number of unique customers per day as well as the daily revenue for each bucket. 

![image](https://user-images.githubusercontent.com/60980933/125665068-f3fabd9a-0b72-41cd-b8d5-3238e75a594d.png)

