Cheat sheet where i attempted to get the syntax only versions of elasticsearch commands from lessons in
https://github.com/LisaHJung/Beginners-Crash-Course-to-Elastic-Stack-Series-Table-of-Contents

Get information about cluster and nodes:

```
GET _API/parameter
```

Get info about cluster health

```
GET _cluster/health
```

Get info about nodes in a cluster:

```
GET _nodes/stats
```

---

# CRUD Operations

## Create an index:

```
PUT Name-of-the-Index
```

## Index a document

Use POST when you want Elasticsearch to autogenerate an id for your document.

```
POST Name-of-the-Index/_doc
{
  "field": "value"
}
```

Use PUT when you want to assign a specific id to your document

```
PUT Name-of-the-Index/_doc/id-you-want-to-assign-to-this-document
{
  "field": "value"
}
```

When you index a document using an id that already exists, the existing document is overwritten by the new document

If you do not want a existing document to be overwritten, you can use the \_create endpoint

```
PUT Name-of-the-Index/_create/id-you-want-to-assign-to-this-document
{
  "field": "value"
}
```

## Read

read a document

```
GET Name-of-the-Index/_doc/id-of-the-document-you-want-to-retrieve
```

## Update

update the fields in a document

```
POST Name-of-the-Index/_update/id-of-the-document-you-want-to-update
{
  "doc": {
    "field1": "value",
    "field2": "value",
  }
}
```

## Delete

```
DELETE Name-of-the-Index/_doc/id-of-the-document-you-want-to-delete
```

---

# Queries

retrieve information about documents in an index

```
GET enter_name_of_the_index_here/_search
```

Get exact number fo hits if over 10,000

```
GET enter_name_of_the_index_here/_search
{
  "track_total_hits": true
}
```

Search for data within a specific time range

```
GET enter_name_of_the_index_here/_search
{
  "query": {
    "Specify the type of query here": {
      "Enter name of the field here": {
        "gte": "Enter lowest value of the range here",
        "lte": "Enter highest value of the range here"
      }
    }
  }
}
```

## increasing the precision

### using the `and` operator:

```
GET enter_name_of_index_here/_search
{
  "query": {
    "match": {
      "Specify the field you want to search": {
        "query": "Enter search terms",
        "operator": "and"
      }
    }
  }
}
```

### using `minimum_should_match`

```
GET enter_name_of_index_here/_search
{
  "query": {
    "match": {
      "headline": {
        "query": "Enter search term here",
        "minimum_should_match": Enter a number here
      }
    }
  }
}
```

## Query Types

### Full text query (OR logic, order and proximity dont matter)

```
GET Enter_name_of_index_here/_search
{
  "query": {
    "match": {
      "Specify the field you want to search": {
        "query": "Enter search terms"
      }
    }
  }
}
```

### match_prase query (Search for phrases)

```
GET Enter_name_of_index_here/_search
{
  "query": {
    "match_phrase": {
      "Specify the field you want to search": {
        "query": "Enter search terms"
      }
    }
  }
}
```

all hits must:

1. have all terms in specified field
2. terms appear in specified order
3. terms appear next to each other

### multi_match query (Querying multiple fields)

```
GET Enter_the_name_of_the_index_here/_search
{
  "query": {
    "multi_match": {
      "query": "Enter search terms here",
      "fields": [
        "List the field you want to search over",
        "List the field you want to search over",
        "List the field you want to search over"
      ]
    }
  }
}
```

#### per field boosting

you can add weight to a certain field

```
GET Enter_the_name_of_the_index_here/_search
{
  "query": {
    "multi_match": {
      "query": "Enter search terms",
      "fields": [
        "List field you want to boost^2",
        "List field you want to search over",
        "List field you want to search over"
      ]
    }
  }
}
```

### match_phrase query (search multiple fields for a phrase)

```
GET Enter_the_name_of_the_index_here/_search
{
  "query": {
    "multi_match": {
      "query": "Enter search phrase",
      "fields": [
        "List field you want to boost^2",
        "List field you want to search over",
        "List field you want to search over"
      ],
      "type": "phrase"
    }
  }
}
```

---

# Aggregations

basic aggregation syntax

```
GET enter_name_of_the_index_here/_search
{
  "aggs": {
    "name your aggregation here": {
      "specify aggregation type here": {
        "field": "name the field you want to aggregate here",
        "size": state how many buckets you want returned here
      }
    }
  }
}
```

Metric Aggregations

follow the same syntax as basic query with these options for aggregation type

- sum
- min
- max
- avg
- cardinality (unique count)

limit the scope of an aggregation

```
GET Enter_name_of_the_index_here/_search
{
  "size": 0,
  "query": {
    "Enter match or match_phrase here": {
      "Enter the name of the field": "Enter the value you are looking for"
    }
  },
  "aggregations": {
    "Name your aggregations here": {
      "Specify aggregations type here": {
        "field": "Name the field you want to aggregate here"
      }
    }
  }
}
```

Bucket Aggregations

1. Date Histogram Aggregation
2. Histogram Aggregation
3. Range Aggregation
4. Terms aggregation

histogram aggregations syntax:

date histograms have 2 interval options:

- calendar_interval
- fixed_interval

regular histograms just use a numerical value for the interval

```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "Name your aggregations here": {
      "histogram": {
        "field":"Name the field you want to aggregate on here",
        "interval": Specify the interval here
      }
    }
  }
}
```

ordering a histogram aggregation (fixed range):

```
GET ecommerce_data/_search
{
  "size": 0,
  "aggs": {
    "transactions_per_price_interval": {
      "histogram": {
        "field": "UnitPrice",
        "interval": 10,
        "order": {
          "_key": "desc"
        }
      }
    }
  }
}
```

Range aggregations allow you to specify explicit ranges

```
GET Enter_name_of_the_index_here/_search
{
  "size": 0,
  "aggs": {
   "Name your aggregations here": {
      "range": {
        "field": "Name the field you want to aggregate on here",
        "ranges": [
          {
            "to": x
          },
          {
            "from": x,
            "to": y
          },
          {
            "from": z
          }
        ]
      }
    }
  }
}
```

terms aggregation

```
GET Enter_name_of_the_index_here/_search
{
  "aggs": {
    "Name your aggregations here": {
      "terms": {
        "field": "Name the field you want to aggregate on here",
        "size": State how many top results you want returned here
      }
    }
  }
}
```

example of a combined aggregation

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
        }
      }
    }
  }
}
```

calculating multiple metrics per bucket

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

---

# Combined queries

general syntax:

```
GET enter_name_of_the_index_here/_search
{
  "query": {
    "match": {
      "Enter the name of the field": "Enter the value you are looking for"
    }
  },
  "aggregations": {
    "Name your aggregation here": {
      "significant_text": {
        "field": "Enter the name of the field you are searching for"
      }
    }
  }
}
```

Bool query:

```
GET name_of_index/_search
{
  "query": {
    "bool": {
      "must": [
        {One or more queries can be specified here.
        A document MUST match all of these queries to be considered as a hit.}
      ],
      "must_not": [
        {A document must NOT match any of the queries specified here.
        It it does, it is excluded from the search results.}
      ],
      "should": [
        {A document does not have to match any queries specified here.
        However, it if it does match, this document is given a higher score.}
      ],
      "filter": [
        {These filters(queries) place documents in either yes or no category.
        Ones that fall into the yes category are included in the hits. }
      ]
    }
  }
}
```

---

Delete by query (delete items returned from this query)

```
POST ecommerce_data/_delete_by_query
{
  "query": {
    "range": {
      "UnitPrice": {
        "lte": 0
      }
    }
  }
}
```

remove hits field from an aggregation

```
GET Enter_name_of_the_index_here/_search
{
  "size": 0,
  "aggs": {
    "Name your aggregations here": {
      "min": {
        "field": "Name the field you want to aggregate on here"
      }
    }
  }
}
```
