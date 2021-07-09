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

**Error 1: 400 Unknown Key**
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

**Error 1: 400 [X] query does not support multiple fields**

Suppose you want to use the match query to pull up all documents where the category field contains the value called "Entertainment" and the date field contains the value "2018-04-12". 

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

If you look at the response, Elasticsearch lists the error type(line 11) as "parsing_exception" and the reason(line 12) as "[match] query doesn't support multiple fields, found [category] and [date]." 

Elasticsearch throws an error because the match query does not support multiple fields. To search in multiple fields at the same time, you use the bool query. 

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
![image](https://user-images.githubusercontent.com/60980933/124649175-61d70500-de55-11eb-85d3-050ef35f3819.png)
Elasticsearch returns top 10 hits with documents whose the category field contains the value called "Entertainment" and the date field contains the value "2018-04-12".

**Error 2: 400 [X] query does not support [y]**

Suppose you want to use the range query to pull up all documents where the date field contains a term between the two date ranges provided:
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
![image](https://user-images.githubusercontent.com/60980933/123853448-b4a34080-d8da-11eb-9f29-ad56a205342c.png)

Elasticsearch returns a 400-error along with cause of the error in the response body. This HTTP error starts with a 4XX, meaning that there was a client error with the request sent.

If you look at the response, Elasticsearch lists the error type(line 11) as "parsing_exception" and the reason(line 12) as "[range] query does not support [date]." 

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
![image](https://user-images.githubusercontent.com/60980933/123855939-bc181900-d8dd-11eb-9aed-0cbe6d80ced5.png)
Elasticsearch retrieves documents whose ranges fall in the range provided in the request. 

**Error 2: 400 error Aggregation definition for [x], expected a [y] **

Suppose you want to send an aggregation request to get the summary of all categories that exist in our dataset. The size parameter allows you to specify how many hits should be returned in the response. Since you only want aggregations results, you add a size parameter and set it equal to 0 to avoid fetching the hits. 

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
![image](https://user-images.githubusercontent.com/60980933/124624234-f29fe780-de39-11eb-83b9-0614e0cdc925.png)

Elasticsearch returns a 400-error along with cause of the error in the response body. This HTTP error starts with a 4XX, meaning that there was a client error with the request sent.

If you look at the response, Elasticsearch lists the error type(line 11) as "parsing_exception" and the reason(line 12) as "Aggregation definition for [size starts with a [VALUE_NUMBER], expected a [START_OBJECT]." 

This error is occuring because aggregation requests always starts with the name of aggregation. The size parameter allows you to specify how many hits should be returned in the response. Depending on where the size parameter is placed, it can return different things. 

By default, Elasticsearch returns top 10 hits whenever the request is sent. If you do not want any hits to be returned, you would place the size parameter in the outermost bracket as shown below. You will see that this will prevent Elasticsearch from fetching the top 10 hits. 

You will also notice that there is an addi

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
![image](https://user-images.githubusercontent.com/60980933/124628166-74454480-de3d-11eb-9a8d-adc9cf80673e.png)
Elasticsearch does not retrieve the top 10 hits(line 16), you can see the aggregations results immediately. You will see that it returns an array of categories. 

Supppose you want to send the same aggregation request but you want to limit the number of categories returned up to the top 15 with the most number of documents. In that case, you would add a size parameter to the field terms and set it equal to 15 as shown below. Depending on where you place the size parameter, the scope of the size parameter changes. 
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
![image](https://user-images.githubusercontent.com/60980933/124629000-3b599f80-de3e-11eb-8886-0d4ef541f846.png)

You will see that top 10 hits have been omitted from the response and 15 top categories are returned.

***Error 4*** 
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
### Errors associated with full text search

**Error 400 unexpected character**
Suppose you are searching for the search terms Michelle and Obama in multiple fields. So you use the multi_match query as whown below:

```
GET news_headlines/_search
{
  "query": {
    "multi_match": {
      "query": "Michelle Obama",
      "fields": [
        "headline",
        "short_description"
        "authors"
      ]
    }
  }
}
```

Expected response from Elasticsearch:
![image](https://user-images.githubusercontent.com/60980933/124657891-4c1b0d00-de60-11eb-866a-67b296fb1e93.png)

Elasticsearch returns a 400-error along with cause of the error in the response body. This HTTP error starts with a 4XX, meaning that there was a client error with the request sent.

If you look at the response, Elasticsearch lists the error type(line 11) as "json_parsing_exception" and the reason(line 12) as "Unexpected character ...: was expecting comma to separate Array entries\n at ... line: 8...]" 

This error is occuring because we are missing a comma in line 8. In the fields array, items must be separated by a comma. 

Add the comma as shown below:
```
GET news_headlines/_search
{
  "query": {
    "multi_match": {
      "query": "Michelle Obama",
      "fields": [
        "headline",
        "short_description",
        "authors"
      ]
    }
  }
}
```
Expected response from Elasticsearch:
![image](https://user-images.githubusercontent.com/60980933/124658052-897f9a80-de60-11eb-9f4b-4b4c21f1ebb8.png)

Elasticsearch returns hits that contain the term "Michelle" or Obama" in the fields headline, short_description, and authors. 

**Error 400 json_parse_exception**
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
Elasticsearch returns a 400-error along with cause of the error in the response body. This HTTP error starts with a 4XX, meaning that there was a client error with the request sent.

If you look at the response, Elasticsearch lists the error type(line 11) as "json_parse_exception" and the reason(line 12) as ""Unexpected character...: was expecting double-quote to start field name.. at line: 9]"

This error is occuring because the parameter type phrase should be included within the multi_match bracket.  

If you move the type parameter up a line as shown below:
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
![image](https://user-images.githubusercontent.com/60980933/124660803-ed579280-de63-11eb-902c-103df14dca9f.png)

It returns 6 hits with the phrase party planning in either fields headline or short description. 


### Errors associated with aggregations

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



