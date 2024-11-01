# ElasticSearch

Elasticsearch is a search engine based on Apache Lucene. It provides a distributed, multitenant-capable full-text search engine with an HTTP web interface and schema-free JSON documents.

---

## ElasticSearch Basics and Fundamentals

### 1. Core Concepts:

---

Diving into Elasticsearch's **core concepts** is essential for understanding its architecture and functionality. 

### 1.1 **Documents and Indices**
   - **Document**: A JSON object with fields representing data.
     - Example:
       ```json
       {
         "title": "Learning Elasticsearch",
         "author": "John Doe",
         "published_date": "2023-01-15"
       }
       ```
   - **Index**: A collection of documents, e.g., "books" index.
     - Creating an index example:
       ```json
       PUT /books
       ```

### 1.2 **Mapping and Types**
   - **Mapping**: Defines field types and structure.
     - Example:
       ```json
       PUT /books/_mapping
       {
         "properties": {
           "title": { "type": "text" },
           "author": { "type": "keyword" },
           "published_date": { "type": "date" }
         }
       }
       ```
   - **Type**: No longer applicable as each index now supports only one type.

### 1.3 **Shards and Replicas**
   - **Shards**: Elasticsearch divides indices into shards to scale horizontally.
     - Example: Creating an index with custom shards and replicas.
       ```json
       PUT /books
       {
         "settings": {
           "number_of_shards": 3,
           "number_of_replicas": 1
         }
       }
       ```

### 1.4 **Cluster, Nodes, and Roles**
   - **Cluster**: A named grouping of nodes.
   - **Nodes**: Instances within a cluster, each assigned specific roles.
      - **Master Node**: Manages configurations and updates.
      - **Data Node**: Manages data storage and processing.
      - **Client/Coordinator Node**: Routes queries, doesn't store data.

### 1.5 **Elasticsearch API Actions (CRUD)**
   - **Indexing** (Create/Update):
     ```json
     POST /books/_doc/1
     {
       "title": "Learning Elasticsearch",
       "author": "John Doe",
       "published_date": "2023-01-15"
     }
     ```
   - **Retrieving**:
     ```json
     GET /books/_doc/1
     ```
   - **Updating**:
     ```json
     POST /books/_doc/1/_update
     {
       "doc": { "author": "Jane Doe" }
     }
     ```
   - **Deleting**:
     ```json
     DELETE /books/_doc/1
     ```

### Summary of the Core Concepts
1. Data is divided into documents, stored in indices, and distributed across shards.
2. Nodes work together in a cluster, balancing the load for efficient querying and data redundancy.

---

### 2. Inverted Index

---

An **inverted index** is a fundamental data structure in Elasticsearch and other search engines. It optimizes search efficiency by storing a mapping from terms (words) to their locations within documents. Let’s break it down into key components and processes:

### 2.1. **Core Structure of Inverted Index**
   - At its heart, an inverted index consists of two parts:
     - **Terms**: Each unique word or token in the text.
     - **Postings List**: A list of documents that contain each term, often including positional data (like the term's location within each document).

   For instance, if a dataset contains the documents:
   - Doc 1: “Elasticsearch powers search”
   - Doc 2: “Search powers insights”
   
   The inverted index would look like this:
   ```
   "Elasticsearch" -> [Doc 1]
   "powers" -> [Doc 1, Doc 2]
   "search" -> [Doc 1, Doc 2]
   "insights" -> [Doc 2]
   ```

### 2.2. **Building the Inverted Index**
   The process involves several stages:
   - **Tokenization**: Splitting text into words or tokens.
   - **Normalization**: Making tokens consistent, like converting to lowercase.
   - **Stemming/Lemmatization** (optional): Reducing words to their base or root forms.
   - **Indexing**: Populating the index with terms and the corresponding document references.

### 2.3. **How Searches Work**
   When a user searches for a term, Elasticsearch retrieves the postings list from the inverted index, quickly locating documents containing that term. For multi-term queries, Elasticsearch can intersect postings lists, using logical operations (e.g., AND, OR) to combine or filter results.

### 2.4. **Optimizations**
   - **Frequency and Proximity**: Stores additional data, like term frequency (number of times a term appears) and positions, which aids relevance scoring.
   - **BKD Trees**: Elasticsearch uses BKD trees for optimized spatial and numeric queries, boosting search performance for structured and semi-structured data.
   - **Compression**: Compresses data in the index to save memory and improve I/O efficiency, especially for large datasets.

### 2.5. **Benefits**
   - **Speed**: Inverted indices provide near-instant retrieval by mapping terms to documents directly.
   - **Efficiency**: Reduced data retrieval time, especially for full-text searches and large datasets.
   - **Scalability**: Easily scalable across distributed systems with sharding, enhancing parallel processing.

### Summary of the Inverted Index

Inverted indices are the foundation of Elasticsearch’s speed and relevance in text search. This structure is tailored for high performance in full-text search scenarios, especially when complex queries and filtering are involved.

---

### 3. Analyzers

---

### 3.1. **What is an Analyzer?**
   - An **analyzer** in Elasticsearch processes text data both at the time of indexing and during querying. It consists of a **tokenizer** and a series of **token filters**.
   - The analyzer breaks down text into searchable units, enabling effective full-text searches by making text uniform and manageable.

### 3.2. **Components of an Analyzer**
   - **Tokenizer**: The first step in analysis. A tokenizer splits text into individual tokens or words. For instance, the standard tokenizer splits based on whitespace, punctuation, etc.
   - **Filters**: After tokenization, filters modify the tokens by:
     - **Removing stop words** (e.g., "the," "is") to reduce noise.
     - **Lowercasing** to maintain consistency.
     - **Stemming or lemmatization** to reduce words to their root form, so "running" becomes "run."

### 3.3. **Built-in Analyzers**
   - **Standard Analyzer**: Default analyzer, performs basic tokenization, lowercasing, and stop word filtering.
   - **Simple Analyzer**: Splits text by non-letter characters and lowercases tokens.
   - **Whitespace Analyzer**: Only splits text by whitespace without additional filtering.
   - **Custom Analyzer**: You can create custom analyzers to specify a tokenizer and filters tailored to your data.

### 3.4. **Custom Analyzer Configuration**
   Creating a custom analyzer involves defining:
   - A **tokenizer** (e.g., edge-ngram tokenizer for partial word matches).
   - A list of **token filters** to process the tokens (e.g., synonym filters, ASCII folding for diacritical marks).
   
   Example configuration:
   ```json
   {
     "analysis": {
       "analyzer": {
         "custom_analyzer": {
           "type": "custom",
           "tokenizer": "whitespace",
           "filter": ["lowercase", "stop", "synonym"]
         }
       }
     }
   }
   ```

### 3.5. **Usage During Indexing and Querying**
   - **Indexing**: When data is indexed, the analyzer processes the input text, stores the terms generated, and builds an inverted index.
   - **Querying**: Analyzers also process the query text so that the terms match the indexed format, ensuring accurate search results.

### 3.6. **Practical Applications of Custom Analyzers**
   - **Synonym Handling**: Custom analyzers can include synonym filters to handle synonyms effectively during searches.
   - **Partial Matching**: Edge-ngram tokenizers help with autocomplete and partial matching.
   - **Language-Specific Analyzers**: Built-in language analyzers (e.g., `english`, `french`) handle language-specific tokenization and filtering.

### 3.7. **Benefits of Analyzers**
   - **Precision**: They allow fine control over how text is processed.
   - **Performance**: Proper use of analyzers can improve query performance by optimizing the text for search requirements.
   - **Relevance**: They ensure better relevance scoring by normalizing input and query text, removing noise, and emphasizing meaningful terms.

### Summary of Analyzers.

Analyzers transform raw text into optimized, searchable data, playing a critical role in making Elasticsearch searches accurate and efficient.

---

### 4. ElasticSearch Queries

---

In Elasticsearch, queries are central to retrieving data. They’re categorized as **leaf queries** (operating on specific fields) and **compound queries** (combining multiple queries). Here's a deep dive into each type, with examples to illustrate their functionality:

### 4.1 **Leaf Queries**

These are standalone, field-specific queries (like `term` and `match` above) that don’t depend on other queries to function.

### 4.1.1 **Match Queries (for Full-Text Search)**
   - **Match Query**: Used for analyzing full-text fields, capable of handling fuzziness, synonyms, and relevance scoring.
     - Example: To search for documents with “elastic search” in the "description" field:
       ```json
       {
         "query": {
           "match": {
             "description": "elastic search"
           }
         }
       }
       ```

   - **Match Phrase Query**: Requires terms to appear in the specified order, useful for exact phrase searches.
     - Example: To search for the exact phrase “elastic search”:
       ```json
       {
         "query": {
           "match_phrase": {
             "description": "elastic search"
           }
         }
       }
       ```

### 4.1.2. **Term Queries (for Structured Data)**
   - **Term Query**: Best for exact matches on keyword fields (IDs, tags, enums) without tokenization.
     - Example: To find documents where the “status” field is exactly “active”:
       ```json
       {
         "query": {
           "term": {
             "status": "active"
           }
         }
       }
       ```
   - **Terms Query**: Allows searching for multiple exact values within a field.
     - Example: To find documents with statuses of either "active" or "pending":
       ```json
       {
         "query": {
           "terms": {
             "status": ["active", "pending"]
           }
         }
       }
       ```

### 4.2. **Compound Queries**
   Compound queries allow for complex logic by combining multiple queries, enabling fine-grained control over query conditions and relevance.

### 4.2.1. **Bool Query**
The most flexible compound query, allowing logic-based combinations:

- **Must Clause**: The `must` clause ensures that all enclosed queries must match. Think of it as an "AND" operation.
    - Example: This returns documents where both `"title"` contains "Elasticsearch" and `"content"` contains "query".
        ```json
        {
            "query": {
                "bool": {
                "must": [
                    { "match": { "title": "Elasticsearch" } },
                    { "match": { "content": "query" } }
                ]
                }
            }
        }
        ```

- **Should Clause**: The `should` clause is a flexible OR operation. If any query in the `should` clause matches, the document is considered a match. Adding multiple `should` clauses increases relevance based on how many match.
    - Example: In this example, documents with either "search" or "indexing" in the `tags` field are included.
        ```json
        {
            "query": {
                "bool": {
                "should": [
                    { "match": { "tags": "search" } },
                    { "match": { "tags": "indexing" } }
                ],
                "minimum_should_match": 1
                }
            }
        }
        ```

- **Must Not Clause**: The `must_not` clause excludes documents that match any query within it, similar to a "NOT" operation.
    - Example: This query includes documents with "Elasticsearch" in the title but excludes those with "archived" status.
        ```json
        {
            "query": {
                "bool": {
                "must": { "match": { "title": "Elasticsearch" } },
                "must_not": { "match": { "status": "archived" } }
                }
            }
        }
        ```

- **Filter Clause**: The `filter` clause narrows down the result set without affecting relevance scores. It’s optimized for structured queries and often used to improve query performance.

    - Example: This query matches documents with "Elasticsearch" in the title and filters to only those with "published" status.
        ```json
        {
            "query": {
                "bool": {
                "must": { "match": { "title": "Elasticsearch" } },
                "filter": { "term": { "status": "published" } }
                }
            }
        }
        ```

#### **Complex Boolean Query Example**
Combining multiple clauses:

```json
{
  "query": {
    "bool": {
      "must": [
        { "match": { "title": "Elasticsearch" } }
      ],
      "should": [
        { "match": { "category": "tutorial" } },
        { "match": { "category": "guide" } }
      ],
      "must_not": [
        { "term": { "status": "archived" } }
      ],
      "filter": [
        { "range": { "publish_date": { "gte": "2023-01-01" } } }
      ]
    }
  }
}
```
This query retrieves documents with "Elasticsearch" in the title, optionally boosts relevance if the document is in "tutorial" or "guide" categories, excludes documents marked as "archived," and only includes documents published after January 1, 2023.


### 4.2.2.  **Dis Max Query** (disjunction max): 
Optimizes for the highest relevance score among multiple queries, often used when querying across similar fields with varied wording.
- Example: Searching for the most relevant match between “title” and “description” fields:
    ```json
    {
        "query": {
            "dis_max": {
                "queries": [
                    { "match": { "title": "elastic search" } },
                    { "match": { "description": "elastic search" } }
                ]
            }
        }
    }
    ```

### 4.3 **Geo Queries**

Elasticsearch provides several geo-specific queries for filtering and scoring documents based on geographic location:

### 4.3.1 **Geo Bounding Box Query**
Defines a rectangular area by specifying two corner points (top-left and bottom-right). Documents with locations inside this box are matched.

- Example: Find documents within a bounding box.
    ```json
    {
      "query": {
        "geo_bounding_box": {
          "location": {
            "top_left": {
              "lat": 40.73,
              "lon": -74.1
            },
            "bottom_right": {
              "lat": 40.01,
              "lon": -71.12
            }
          }
        }
      }
    }
    ```

### 4.3.2 **Geo Distance Query**
Finds documents within a certain distance from a point. Useful for proximity searches, like "find stores within 10 miles."

- Example: Search within a 50 km radius.
    ```json
    {
      "query": {
        "geo_distance": {
          "distance": "50km",
          "location": {
            "lat": 40.7128,
            "lon": -74.0060
          }
        }
      }
    }
    ```

### 4.3.3 **Geo Polygon Query**
Searches within a polygon defined by a series of latitude and longitude points, allowing for irregular area shapes.

- Example: Match locations within a polygon.
    ```json
    {
      "query": {
        "geo_polygon": {
          "location": {
            "points": [
              { "lat": 40.73, "lon": -74.1 },
              { "lat": 40.01, "lon": -71.12 },
              { "lat": 39.73, "lon": -73.1 }
            ]
          }
        }
      }
    }
    ```

### 4.3.4 **Geo Shape Query**
The `geo_shape` query allows for more complex spatial filtering, using pre-defined shapes like circles, polygons, or lines. This is often used with indexed geometries.

- Example: Search within a complex shape (circle or custom shape).
    ```json
    {
      "query": {
        "geo_shape": {
          "location": {
            "shape": {
              "type": "circle",
              "coordinates": [-74.1, 40.73],
              "radius": "1000m"
            },
            "relation": "within"
          }
        }
      }
    }
    ```

### **4.3.5 Using Geo Filters with Bool Queries**

Geo filters are often used in combination with other query types within `bool` queries, allowing flexible, location-based filtering along with other criteria.

#### Example of a Combined Geo and Bool Query
Finds published documents within a specific area and filters out archived content.

```json
{
  "query": {
    "bool": {
      "must": [
        { "term": { "status": "published" } }
      ],
      "filter": {
        "geo_distance": {
          "distance": "50km",
          "location": {
            "lat": 40.7128,
            "lon": -74.0060
          }
        }
      },
      "must_not": [
        { "term": { "status": "archived" } }
      ]
    }
  }
}
```


### Summary of Elastic Queries
- **Match queries** are ideal for full-text fields requiring analysis (e.g., descriptions).
- **Term queries** work well with keyword fields needing exact matches (e.g., IDs).
- **Bool queries** allow complex logic with filtering and relevance.
- **Dis Max queries** maximize relevance across similar fields.
- **Geo Bounding Box** for rectangular areas.
- **Geo Distance** for proximity.
- **Geo Polygon** for custom shapes.
- **Geo Shape** for complex spatial data.

These queries, combined thoughtfully, make Elasticsearch highly adaptable to various search needs.

---

### 5. Aggregations

---

Elasticsearch’s aggregation framework is divided into **metrics** and **bucket aggregations**. Here’s a deep dive into each, with subtypes and examples.

### **5.1. Metrics Aggregations**
These calculate values from field data, like sums or averages.

- **Avg Aggregation**: Computes the average for numeric fields.
   - Example: Calculate average price across documents.
     ```json
     {
       "aggs": {
         "avg_price": { "avg": { "field": "price" } }
       }
     }
     ```

- **Sum Aggregation**: Totals numeric field values.
   - Example: Sum of all sales amounts.
     ```json
     {
       "aggs": {
         "total_sales": { "sum": { "field": "amount" } }
       }
     }
     ```

- **Min/Max Aggregations**: Find minimum or maximum field values.
   - Example: Find the highest score.
     ```json
     {
       "aggs": {
         "max_score": { "max": { "field": "score" } }
       }
     }
     ```

- **Stats Aggregation**: Generates summary stats (min, max, avg, sum, count).
   - Example: Summarize views across articles.
     ```json
     {
       "aggs": {
         "article_views": { "stats": { "field": "views" } }
       }
     }
     ```

- **Cardinality Aggregation**: Counts unique values.
   - Example: Count unique users by ID.
     ```json
     {
       "aggs": {
         "unique_users": { "cardinality": { "field": "user_id" } }
       }
     }
     ```

- **Percentiles/Percentile Ranks Aggregations**: Show value distribution.
   - Example: Get 50th, 90th, and 99th percentiles for response times.
     ```json
     {
       "aggs": {
         "response_percentiles": {
           "percentiles": { "field": "response_time", "percents": [50, 90, 99] }
         }
       }
     }
     ```

### **5.2. Bucket Aggregations**
These create groups (buckets) of documents based on field values or criteria. Each bucket can contain documents matching conditions and may contain further sub-aggregations.

- **Terms Aggregation**: Groups documents by unique terms.
   - Example: Group documents by category.
     ```json
     {
       "aggs": {
         "by_category": { "terms": { "field": "category" } }
       }
     }
     ```

- **Range Aggregation**: Group documents within defined ranges.
   - Example: Bucket users by age ranges.
     ```json
     {
       "aggs": {
         "age_ranges": {
           "range": { "field": "age", "ranges": [{ "to": 20 }, { "from": 20, "to": 30 }, { "from": 30 }] }
         }
       }
     }
     ```

- **Date Histogram**: Buckets documents based on date intervals (e.g., daily, monthly).
   - Example: Group events by month.
     ```json
     {
       "aggs": {
         "monthly_events": { "date_histogram": { "field": "date", "calendar_interval": "month" } }
       }
     }
     ```

- **Histogram Aggregation**: Creates buckets based on custom numeric ranges.
   - Example: Histogram of item prices.
     ```json
     {
       "aggs": {
         "price_histogram": { "histogram": { "field": "price", "interval": 10 } }
       }
     }
     ```

- **Filter Aggregation**: Buckets documents matching a specific filter.
   - Example: Filter for high-value orders.
     ```json
     {
       "aggs": {
         "high_value_orders": { "filter": { "range": { "price": { "gt": 100 } } } }
       }
     }
     ```

- **Filters Aggregation**: Allows multiple filter buckets in a single query.
   - Example: Separate buckets for high and low prices.
     ```json
     {
       "aggs": {
         "price_buckets": {
           "filters": {
             "filters": {
               "expensive": { "range": { "price": { "gt": 100 } } },
               "cheap": { "range": { "price": { "lt": 50 } } }
             }
           }
         }
       }
     }
     ```

- **Geohash Grid Aggregation**: For geolocation-based bucketing, especially with maps.
   - Example: Group location data by geohash grid.
     ```json
     {
       "aggs": {
         "geo_buckets": { "geohash_grid": { "field": "location", "precision": 5 } }
       }
     }
     ```

### **5.3. Combining Aggregations**
Each aggregation can nest other aggregations, allowing complex analysis structures.

#### Example of a Combined Aggregation
Calculate the average order amount by city and age range:

```json
{
  "aggs": {
    "by_city": {
      "terms": { "field": "city" },
      "aggs": {
        "age_ranges": {
          "range": { "field": "age", "ranges": [{ "to": 20 }, { "from": 20, "to": 30 }, { "from": 30 }] },
          "aggs": {
            "avg_order_amount": { "avg": { "field": "order_amount" } }
          }
        }
      }
    }
  }
}
```

### Summary of Aggregations

With these aggregations, Elasticsearch becomes a powerful analytics engine, enabling sophisticated data analysis directly within the index.

---

### 6. Sorting

---

### 6.1. **Basic Sorting**
   - By default, Elasticsearch sorts results by `_score` (relevance-based sorting).
   - To sort by a specific field, use `sort` in the query.

   ```json
   {
     "sort": [
       { "price": { "order": "asc" } }
     ],
     "query": { "match_all": {} }
   }
   ```

### 6.2. **Field-based Sorting**
   - Sortable fields should be of numeric, date, or keyword type (for text, use `keyword` fields).
   - Example sorting by a date field:

   ```json
   {
     "sort": [
       { "release_date": { "order": "desc" } }
     ],
     "query": { "match_all": {} }
   }
   ```

### 6.3. **Multiple Sort Fields**
   - Specify multiple sort criteria, with the first sort field taking precedence.

   ```json
   {
     "sort": [
       { "price": { "order": "asc" } },
       { "rating": { "order": "desc" } }
     ],
     "query": { "match_all": {} }
   }
   ```

### 6.4. **Nested Sorting**
   - For nested documents (e.g., arrays of objects), use `nested` with a path to sort by nested fields.

   ```json
   {
     "sort": [
       {
         "products.price": {
           "order": "asc",
           "nested": {
             "path": "products",
             "filter": { "range": { "products.price": { "gt": 10 } } }
           }
         }
       }
     ],
     "query": { "match_all": {} }
   }
   ```

### 6.5. **Geolocation Sorting**
   - Use `geo_distance` to sort by proximity for `geo_point` fields.

   ```json
   {
     "sort": [
       {
         "_geo_distance": {
           "location": "40.715, -73.988",
           "order": "asc",
           "unit": "km"
         }
       }
     ],
     "query": { "match_all": {} }
   }
   ```

### 6.6. **Script-based Sorting**
   - Script-based sorting allows custom logic using the `painless` scripting language.

   ```json
   {
     "sort": {
       "_script": {
         "type": "number",
         "script": {
           "source": "doc['price'].value * params.factor",
           "params": { "factor": 1.2 }
         },
         "order": "desc"
       }
     },
     "query": { "match_all": {} }
   }
   ```

### 6.7. **Missing Values**
   - Specify handling for missing values with `missing`, setting them as the highest or lowest values.

   ```json
   {
     "sort": [
       { "price": { "order": "asc", "missing": "_last" } }
     ],
     "query": { "match_all": {} }
   }
   ```

### 6.8. **Sorting in Aggregations**
   - In aggregations, sort bucket aggregations by criteria, like by `count` or custom metrics.

   ```json
   {
     "aggs": {
       "top_brands": {
         "terms": {
           "field": "brand.keyword",
           "order": { "_count": "desc" }
         }
       }
     }
   }
   ```

---

### 7. Relevance Scoring

---

Elasticsearch's relevance scoring is crucial for ranking documents based on their similarity to a query. Here’s an in-depth look:

### **7.1. Scoring Mechanism and BM25 Algorithm**

The **BM25 (Best Matching 25)** algorithm is Elasticsearch’s default relevance scoring algorithm. BM25 improves upon traditional TF-IDF (Term Frequency-Inverse Document Frequency) by adjusting term frequency saturation and document length normalization, providing more nuanced relevance.

#### Core Components of BM25:
   - **Term Frequency (TF)**: Counts how often a term appears in a document. Higher term frequency can increase relevance, but BM25 applies diminishing returns to avoid favoring very high counts excessively.
   - **Inverse Document Frequency (IDF)**: Rare terms across the entire dataset are given higher weight since they provide more unique information about a document.
   - **Document Length Normalization**: BM25 penalizes longer documents, assuming shorter documents with the same term density are more relevant to the term.

### **7.2. Calculating the BM25 Score**
The BM25 formula combines these components, with two main parameters:
   - **k1**: Controls term frequency saturation (default around 1.2). Higher values give more influence to term frequency.
   - **b**: Controls length normalization (default around 0.75). Higher values penalize longer documents more strongly.
   - [BM25 Alogirthm](https://www.elastic.co/blog/practical-bm25-part-2-the-bm25-algorithm-and-its-variables)

### **7.3. Understanding Scoring in Elasticsearch Queries**

In Elasticsearch, relevance scores are generated by the **"match"** or **"multi_match"** queries. Each document receives a relevance score, and results are ranked based on these scores. You can inspect scores using the `"explain": true` parameter, which details each document’s score and shows how BM25 factors contribute.

#### Example Query with Relevance Scoring:
```json
{
  "query": {
    "match": {
      "content": {
        "query": "Elasticsearch relevance scoring",
        "boost": 1.5
      }
    }
  },
  "explain": true
}
```
This query searches for "Elasticsearch relevance scoring" in the `content` field. The `"boost"` parameter can emphasize this field for relevance, while `"explain": true` helps analyze the scoring breakdown.

### **7.4. Improving Relevance with Advanced Techniques**
   - **Field Boosting**: Increase relevance by weighting certain fields more heavily.
   - **Function Scoring**: Custom scoring functions let you adjust scores based on field values, geographic distance, or date recency.
   - **Multi-Match Queries**: Use multiple fields in a single query, like title and content, to balance relevance from multiple sources.

### **7.5. Practical Use Cases for BM25 in Elasticsearch**
   - **E-commerce**: Boost product names over descriptions to prioritize document structure.
   - **Content Recommendation**: Add boosts to recent articles or content based on reader engagement.

### Summary of Relevance Score

Relevance scoring with BM25 is foundational to Elasticsearch’s search quality, offering powerful controls for tuning results to your specific needs. 

---

### 8. Pagination and Cursors

---

Pagination in Elasticsearch is essential for handling large result sets efficiently, as it prevents overwhelming the client and server. Elasticsearch offers different methods for pagination, each with specific use cases. Let's break down each method:

---

### 8.1. **Basic Pagination with `from` and `size`**
   - The `from` parameter skips a set number of results, while `size` controls the number of results returned.
   - **Use Case**: Simple pagination for small datasets.
   - **Limitations**: Inefficient for large datasets because performance drops as `from` increases (since Elasticsearch has to load and sort through all documents before the specified offset).

   **Example**:
   ```json
   {
     "from": 20,
     "size": 10,
     "query": { "match_all": {} }
   }
   ```
   This retrieves results from the 21st to the 30th position.

### 8.2. **Search After for Deep Pagination**
   - **Purpose**: Overcomes the limitations of `from` and `size` by using a **cursor**.
   - Uses the `search_after` parameter to specify a unique field value (like a timestamp or an ID) from the last document of the previous page.
   - **Use Case**: Suitable for deep pagination (e.g., pages far into the dataset).
   - **Limitations**: Requires sorted queries; can’t jump directly to a page.

   **Example**:
   ```json
   {
     "sort": [ { "timestamp": "asc" }, { "id": "asc" } ],
     "size": 10,
     "query": { "match_all": {} },
     "search_after": [1627489200, "XYZ123"] 
   }
   ```
   Here, `search_after` takes the values from the `timestamp` and `id` fields of the last document on the previous page, ensuring seamless navigation.

### 8.3. **Scroll API for Bulk Data Retrieval**
   - **Purpose**: Efficiently handles large datasets by creating a consistent snapshot of the index to avoid changes affecting results.
   - **Use Case**: Primarily used for exporting or processing the entire dataset rather than interactive pagination.
   - **Limitations**: Not ideal for real-time paginated views; holds large segments in memory, which can strain resources.
   
   **Example Workflow**:
   - First, initiate a scroll session:
     ```json
     {
       "size": 100,
       "query": { "match_all": {} },
       "scroll": "1m" 
     }
     ```
   - Use the `_scroll_id` returned by the initial request to retrieve subsequent pages:
     ```json
     {
       "scroll": "1m",
       "scroll_id": "DXF1ZXJ5QW5kRmV0Y2gBAAAAAAAA..."
     }
     ```

   After each scroll request, repeat until the returned results are empty, which indicates that all documents have been retrieved.

### 8.4. **Point-in-Time (PIT) for Real-time Pagination with Consistency**
   - **Purpose**: Offers a consistent snapshot of the index at a specific point, ensuring paginated results don’t change between requests.
   - **Use Case**: Good for real-time applications where data changes frequently but consistent pagination is necessary.
   - **Limitations**: PIT sessions should be short-lived to avoid excessive resource use.

   **Example Workflow**:
   - First, initiate a Point-in-Time session:
     ```json
     POST /index_name/_pit?keep_alive=1m
     ```
   - Use the `pit_id` with `search_after` for paginated queries:
     ```json
     {
       "size": 10,
       "query": { "match_all": {} },
       "pit": { "id": "PIT_ID", "keep_alive": "1m" },
       "sort": [ { "timestamp": "asc" }, { "id": "asc" } ],
       "search_after": [1627489200, "XYZ123"]
     }
     ```
   - Close the PIT session when done:
     ```json
     DELETE /_pit
     ```

### 8.5. **Limitations and Considerations**
   - **Deep Pagination**: As the dataset grows, deep pagination can become costly due to increased memory usage. Use `search_after` or PIT for deeper, efficient pagination.
   - **Real-time Constraints**: The Scroll API locks data for consistency, so it’s unsuitable for real-time applications. In contrast, `search_after` and PIT handle real-time updates better.
   - **Sort Requirements**: Both `search_after` and PIT require sorted fields to maintain proper order across paginated results.

### 8.6. Summary Table of Pagination Techniques

| Method          | Use Case                                 | Limitations                        | Example Scenarios                                |
|-----------------|-----------------------------------------|------------------------------------|--------------------------------------------------|
| `from` & `size` | Simple pagination for small datasets    | Performance drop for large `from` values | Basic search pages, small datasets              |
| `search_after`  | Deep pagination without `from` overhead | Requires sorted fields, can’t skip pages | Infinite scrolling, data tables with lots of records |
| Scroll API      | Bulk data export/processing             | High memory usage, no real-time consistency | Data migration, report generation               |
| Point-in-Time   | Consistent real-time pagination         | Needs frequent re-creation to avoid memory issues | Dashboards, applications requiring consistent views |

Each method serves specific needs, balancing consistency, performance, and real-time capabilities. This setup allows Elasticsearch to handle vast and dynamic datasets while supporting efficient data retrieval.

---

### 9. Facets and Filters

---

Faceting creates summaries of data, useful for search result filtering, like categorizing search results by price or brand. Filters, on the other hand, optimize performance by narrowing down documents without affecting scoring.

### 9.1. **Facets** (Aggregations)
   Faceting is a process in Elasticsearch that aggregates search results, providing structured summaries for complex queries. For example, if searching for "laptops," facets can aggregate results by price range, brand, or processor type, allowing users to filter search results dynamically.

   - **Bucket Aggregations**: Group documents by certain criteria, creating “buckets” for each unique value. Common bucket types include:
     - **Terms Aggregation**: Summarizes by unique field values (e.g., brand names).
     - **Range Aggregation**: Creates buckets for numerical ranges (e.g., price ranges).
     - **Date Histogram**: Buckets documents by date intervals, great for tracking trends over time.
   - **Metric Aggregations**: Calculate statistical metrics like count, sum, average, min, and max on fields within buckets, useful for summaries.
   - **Multi-Level Aggregations**: Nested aggregations allow more complex analyses, like a "price by brand by location" breakdown, giving a deeper multi-dimensional view of data.

### 9.2. **Filters**: Filtering without Scoring Impact
   Filters enable the narrowing of search results by criteria (e.g., price < $500), improving query efficiency and bypassing relevance scoring. They’re often used to pre-process data before a full-text search and work well with caches, resulting in faster query performance.

   - **Filtered Query**: Using the `bool` query, filters can be applied to queries under `must` or `filter` clauses. Filters in `filter` clauses are cached and reusable, significantly boosting speed for repetitive filters (e.g., "available items only").
   - **Filter Types**:
     - **Term Filter**: Exact match filtering for structured data, efficient for keywords or IDs.
     - **Range Filter**: Sets criteria for numeric or date fields.
     - **Geo Filters**: Filter based on geolocation, such as finding items within a radius.
   
### 9.3. **Combining Facets and Filters in Search Applications**
   In complex search interfaces, facets allow users to drill down through categories, while filters further refine their selections without recalculating scores, ensuring responsive user experiences.


### 9.4. **Sample Implementations**

#### 9.4.1. **Creating Facets (Aggregations)**
To implement facets, you’ll define bucket aggregations to group and categorize data. For instance, creating a facet for "price range" and "brand" in a search for laptops:

```json
GET /products/_search
{
  "query": {
    "match": { "description": "laptop" }
  },
  "aggs": {
    "price_ranges": {
      "range": {
        "field": "price",
        "ranges": [
          { "to": 500 },
          { "from": 500, "to": 1000 },
          { "from": 1000 }
        ]
      }
    },
    "brands": {
      "terms": { "field": "brand.keyword" }
    }
  }
}
```

This example provides a breakdown of price ranges and a count of each brand, creating flexible filters users can click on to refine results.

#### 9.4.2. **Using Filters for Optimized Performance**
Filters improve performance by narrowing results without scoring. Here’s an example of using a `bool` query with a `filter` clause:

```json
GET /products/_search
{
  "query": {
    "bool": {
      "must": {
        "match": { "description": "laptop" }
      },
      "filter": [
        { "term": { "in_stock": true } },
        { "range": { "price": { "lt": 1000 } } }
      ]
    }
  }
}
```

In this query, `in_stock` and `price` filters optimize search results without affecting scoring.

### 9.5. **Performance Optimization Techniques**

#### 9.5.1. **Caching Filters**
   - Filters are often cached by Elasticsearch, particularly if used frequently. Filtering with terms (exact matches) or numeric ranges benefits from caching, as it bypasses repeated computation and improves query speed.

#### 9.5.2. **Minimize Full-Text Search in Filters**
   - Full-text queries like `match` should only appear in scoring-related queries (e.g., `must`). Using `term` or `range` filters for precise values (like IDs, prices) reduces computational load.

#### 9.5.3. **Selective Use of Aggregations**
   - Aggregations, especially nested ones, can be heavy on resources. Avoid overusing multi-level facets and restrict them to essential fields. For high-cardinality fields (e.g., many unique values), consider filtering or limiting the number of results.

#### 9.5.4. **Balancing Shard Count**
   - Elasticsearch distributes filters and aggregations across shards. While more shards can speed up complex aggregations, too many shards may lead to overhead. Test performance based on index size and query type to determine optimal shard counts.

---

## Cluster Architecture

### 1. Cluster Overview and Node Responsibilities

---

Each node type in an Elasticsearch cluster has specialized roles that allow it to handle different aspects of indexing, searching, and managing data. Let's explore the node types in detail.

#### **1.1. Master Node**
The master node is the cluster’s brain, responsible for the overall management and health of the cluster.

- **Responsibilities**:
  - **Cluster State Management**: Tracks all nodes, indices, and shard locations, ensuring the cluster state is updated consistently.
  - **Index and Shard Management**: Responsible for creating and deleting indices and for allocating and rebalancing shards. When new nodes are added or removed, the master node redistributes shards to maintain balance and high availability.
  - **Settings**: Manages cluster-wide settings like shard allocation and replica counts.
  - **Health Checks**: Constantly checks the health of nodes, removing unresponsive nodes to maintain a stable cluster state.

- **Consensus and Election Process**:
  - Elasticsearch uses the **Zen Discovery** module to elect a master node in case the active master node goes down.
  - **Master-eligible nodes** participate in elections, where the first node to form a majority connection with other nodes becomes the new master.
  - **Fault Tolerance**: Having multiple master-eligible nodes increases fault tolerance but only one node actively acts as the master.

#### **1.2. Data Node**
Data nodes are the primary nodes for **storing data, processing indexing operations, and executing search and aggregation requests**.

- **Responsibilities**:
  - **Storage**: Holds the actual data and handles shards (both primary and replicas).
  - **Indexing and Searching**: Executes indexing requests to store new data and processes queries on local data.
  - **Shard Management**: Each data node hosts a subset of the cluster’s primary and replica shards, handling local searches and aggregations to ensure efficient processing.
  - **Scalability and Fault Tolerance**: Data nodes scale horizontally, meaning adding more data nodes increases storage and processing capacity, and replication ensures fault tolerance.

#### **1.3. Coordinating Node**
Also known as the **client node**, the coordinating node acts as a **router** for client requests, managing query distribution and response aggregation.

- **Responsibilities**:
  - **Routing**: Breaks down client requests (both indexing and search requests) and forwards them to the relevant data nodes.
  - **Response Aggregation**: Gathers responses from data nodes and consolidates results to produce a final response to the client.
  - **Load Distribution**: Since coordinating nodes don’t store data or shards, they focus on reducing load on data nodes, especially in large clusters.

#### **1.4. Ingest Node**
Ingest nodes preprocess data before it is indexed, often using **ingest pipelines** to transform and enrich data.

- **Responsibilities**:
  - **Data Transformation**: Uses ingest pipelines to manipulate data (e.g., parsing logs, enriching data fields, removing sensitive information).
  - **Efficient Preprocessing**: By processing data before indexing, ingest nodes reduce the need for expensive data transformations during searches.

---

### 2. **Cluster Hierarchy Overview**

---

Each level in the cluster architecture plays a role in organizing and distributing data efficiently across nodes.

#### **2.1. Cluster**
   - The cluster is the top-level structure that **contains multiple nodes**, managing data storage and search operations as a unified system.
   - **Shard Allocation**: The master node manages shard distribution across data nodes, balancing load and ensuring fault tolerance by replicating shards across nodes.
   - **Redundancy and Fault Tolerance**: Replica shards provide redundancy, so if a data node goes down, the cluster remains available by rerouting requests to replica shards.

#### **2.2. Index**
   - An index in Elasticsearch is similar to a **database table** and is the logical grouping of documents that share the same structure.
   - **Sharding**: Each index is divided into **shards** (subsets of data) to distribute storage and query load across the cluster.
   - **Mappings**: Defines the structure of documents in the index, specifying field types and enabling indexing configurations like analyzers.

#### **2.3. Shards**
   - Shards are the fundamental units of data distribution and parallelism in Elasticsearch.
   - **Lucene Index**: Each shard is a full **Lucene index**, allowing it to process and store data independently.
   - **Primary and Replica Shards**: Each index has a configurable number of **primary shards** (original data) and **replica shards** (copies for fault tolerance).
   - **Query Parallelism**: When a search request is made, each shard executes the query in parallel, and results are merged by the coordinating node.

#### **2.4. Segments**
   - Shards are broken down into smaller, immutable units called **segments**. Lucene writes data in segments for efficient searching.
   - **Immutable Nature**: Once written, segments are not modified. Instead, new data or updates are added to new segments, and Lucene periodically merges segments to optimize performance.
   - **Segment Merging**: Over time, Elasticsearch merges smaller segments into larger ones to reduce the number of segments, which improves search speed and reduces disk usage.

#### **2.5. Documents**
   - **Basic Data Units**: Each document is a JSON object stored in segments, representing an individual record in the index.
   - **Field Mappings**: The document structure is defined by field mappings, which specify data types and control how each field is indexed and searched.
   - **Dynamic and Static Mappings**: Elasticsearch supports dynamic mapping for fields that don’t have predefined mappings, although static mappings offer better control over data structure and indexing behavior.

---

### 3. **Elasticsearch Request Flow and Processing**

---

To deeply understand how **request flow and cluster operations** work in Elasticsearch, let’s walk through each stage of the process in detail:

### 3.1. **Receiving a Request**

When a **client** sends a request to Elasticsearch, it can be either a **query** (search request) or an **indexing** (write) request. Here’s how this begins:

   - The client connects to a **coordinating node** (any node can act as a coordinating node, even though it's sometimes a dedicated role).
   - The coordinating node is responsible for breaking down the request and routing it to the appropriate **data nodes**.

### 3.2. **Breaking Down the Request**

   - **Identifying Relevant Shards and Nodes**:
     - For search queries, the coordinating node first identifies the **index or indices** involved in the query.
     - Each index in Elasticsearch is divided into **shards** (logical units of data). The coordinating node identifies which shards are relevant to the query.
     - It then routes the query to the nodes hosting those shards. Each shard exists as a **primary shard** and one or more **replica shards** for redundancy.

   - **Selecting Shards (Primary or Replica)**:
     - For each shard, Elasticsearch will select either the primary shard or a replica shard, distributing the query load across replicas when possible to balance the workload.

### 3.3. **Execution at Shard Level**

At this stage, each shard executes the request locally. This process differs slightly between a **search** request and an **indexing** request.

#### **3.3.1. Query Execution (Search Requests)**

   - **Query Phase**:
     - In the **query phase**, each shard independently executes the query within its dataset.
     - It retrieves **document IDs, scores, and metadata** of relevant documents and returns these results to the coordinating node. During this phase, only the necessary information (not the full document contents) is returned, optimizing efficiency.

   - **Fetch Phase**:
     - The coordinating node then initiates a **fetch phase**, retrieving the actual document data only for the top results.
     - In this phase, the coordinating node consolidates document IDs and metadata, then requests the complete documents from the relevant shards. This two-phase process reduces network traffic, as full documents are only fetched after initial ranking.

#### **3.3.2. Indexing Execution (Write Requests)**

   - **Primary Shard Indexing**:
     - For indexing requests, the coordinating node forwards the request to the relevant **primary shard**.
     - The primary shard performs the **indexing operation** first, ensuring data is written correctly.

   - **Replication to Replica Shards**:
     - Once the primary shard successfully indexes the document, it forwards the change to all **replica shards**.
     - Each replica shard indexes the document in parallel, ensuring consistency across the cluster.


### 3.4. **Response Consolidation**

After the query or indexing operation completes, the coordinating node consolidates the response:

   - **Sorting and Ranking**:
     - For search queries, the coordinating node **merges and ranks** the results from all shards.
     - Elasticsearch calculates a **relevance score** for each document based on factors like term frequency and inverse document frequency (TF-IDF) and applies **BM25** scoring to determine document relevance.
     - The coordinating node then orders the documents according to their scores and prepares the final list of top results.

   - **Returning the Response**:
     - Once consolidation and sorting are complete, the coordinating node sends the final response back to the client.


### 3.5. **Indexing Flow Details**

The indexing flow includes several key mechanisms that ensure data consistency and durability:

   - **Primary-Replica Synchronization**:
     - Every document write (index, delete, or update) is processed on the **primary shard** first.
     - Once the primary shard successfully indexes or updates the document, it forwards the operation to all **replica shards**.

   - **Distributed Write-Ahead Log (WAL)**:
     - To ensure durability, Elasticsearch writes every indexing operation to a **write-ahead log (WAL)** on both the primary and replica nodes.
     - The WAL is essentially a **transaction log** that records all changes before committing them to Lucene’s index structures.
     - This ensures that even if a node crashes before committing changes, the transaction log allows for **recovery** to a consistent state.

   - **Commit Process**:
     - After a certain threshold of changes, Elasticsearch commits these changes to Lucene. This involves:
       - **Creating new segments** in the Lucene index.
       - **Flushing** the WAL and persisting the new segment data, making the data available for query.
     - Committed changes are persistent and survive node restarts, while uncommitted changes are only present in the WAL.

### 3.6. **Request Flow and Cluster Operations Summary**

To visualize this, here’s a simplified flow of the entire process:

1. **Client Sends Request** (Search or Index) → **Coordinating Node Receives Request**
2. **Coordinating Node Identifies Relevant Shards** (and chooses primary or replica shards)
3. **Execution on Shards**:
   - **Query Phase (Search)**:
     - Query executed on selected shards.
     - Each shard returns IDs and scores of matching documents.
   - **Indexing Phase (Write)**:
     - Document written to the primary shard.
     - Changes forwarded to replica shards.
4. **Fetch Phase (Search)**:
   - Fetches full documents for the top results.
5. **Consolidation and Response**:
   - Coordinating node merges, ranks, and sorts results for search.
   - Coordinating node confirms write operation on all replicas for indexing.
6. **Final Response Sent to Client**


### 3.7. **Additional Considerations**

- **Fault Tolerance and High Availability**:
   - **Shard Replicas**: Multiple replicas ensure availability even if a primary shard goes down.
   - **Node Failure Handling**: If a node fails, Elasticsearch promotes a replica shard to primary, maintaining data consistency and availability.
  
- **Cluster State Management**:
   - The **master node** maintains the **cluster state** (e.g., index settings, shard allocation).
   - This state is communicated to all nodes, so each node is aware of the cluster’s configuration and the locations of shards.

---

### 4. Lucene, Segments, and Index Structures

---

To thoroughly understand Elasticsearch's storage and retrieval mechanisms, let’s go deep into **Lucene's segments, inverted index, and advanced data structures** like **BKD trees**. Lucene, at its core, powers Elasticsearch, giving it the ability to handle and query massive datasets with impressive speed and efficiency.

### 4.1. **Lucene and Segments**

#### **4.1.1. What is a Segment?**

A **segment** in Lucene is a self-contained, immutable collection of documents that forms a subset of a shard. Each segment is essentially a mini-index with its own data structures, including **inverted indexes**, **stored fields**, and **other data structures** to facilitate efficient searching and retrieval.

- **Immutable Nature**: Once created, a segment cannot be modified. Instead, when new documents are added, they go into new segments.
- **Segment Lifecycle**:
  - New documents are first written to a memory buffer.
  - Periodically, Lucene flushes this buffer, creating a new segment on disk.
  - Older segments may be merged with newer ones through the **segment merging** process.

#### **4.1.2 Segment Merging**
- **Purpose**: To control the number of segments and improve query performance by reducing the overhead of searching across numerous small segments.
- **Process**:
  - When segments reach a certain size, Lucene automatically merges smaller segments into larger ones. This process involves re-indexing and rewriting the segments into one larger segment, discarding deleted documents along the way.
  - Merging is a balancing act: it reduces the number of segments, improving search speed, but it also incurs I/O cost.
  
  **Example**:
  - Imagine a shard with 100 small segments. Lucene might merge them into fewer, larger segments (say, 10 segments), consolidating their data and removing any "marked as deleted" documents.

#### **4.1.3. Advantages of Segments**
- **Concurrency**: Multiple segments can be queried simultaneously, allowing Elasticsearch to parallelize search operations across segments.
- **Efficiency**: Immutability of segments ensures that data doesn't have to be locked for reads, enhancing speed and consistency.
- **Data Deletion**: Since segments are immutable, deletions do not remove documents immediately; instead, they are marked for deletion. The segment merge process will then eliminate these "deleted" documents.


### 4.2. **Lucene’s Inverted Index**

The **inverted index** is Lucene’s most fundamental data structure and is the backbone of Elasticsearch’s fast full-text search capabilities.

#### **4.2.1. Structure of an Inverted Index**
The inverted index allows quick lookups by mapping **terms** to **postings lists** (lists of documents containing each term).

- **Terms**: Unique words or tokens extracted from the text fields of documents.
- **Postings Lists**: Lists that store document identifiers for each term. Each entry in a postings list links to:
  - Document ID
  - Term frequency within the document (for relevance scoring)
  - Position information (for phrase or proximity queries)
  
  **Example**:
  - Suppose you index the text "Elasticsearch is scalable search". The inverted index might look like this:
    ```
    Term          Documents
    ------------------------
    "Elasticsearch" [1]
    "is"            [1, 2]
    "scalable"      [1, 3]
    "search"        [1]
    ```

#### **4.2.2. Advantages of Inverted Index**
- **Fast Term Lookup**: Quick access to documents containing specific terms, making it ideal for keyword-based and full-text searches.
- **Optimized Relevance Scoring**: Lucene uses term frequency-inverse document frequency (TF-IDF) and the BM25 algorithm to compute relevance scores, which improve search result accuracy.

#### **4.2.3. Additional Optimizations in Inverted Index**
- **Skip Lists**: Lucene uses skip lists within postings lists to speed up queries, especially for large documents. Skip lists allow the query engine to "skip over" sections of the postings list, reducing unnecessary comparisons.
- **Positions and Offsets**: Positions allow for phrase queries (where terms must appear in sequence) and proximity queries (where terms must be within a certain distance from each other).


### 4.3. **BKD Trees and Doc Values**

Apart from the inverted index, Lucene also uses specialized data structures to handle numeric and spatial data.

#### **4.3.1. BKD Trees**

**BKD Trees** are used in Elasticsearch for indexing and querying numeric, date, and geospatial data, especially for high-cardinality fields (fields with a large number of unique values).

- #### **Structure of a BKD Tree**
  BKD trees are essentially a form of a **k-d tree** (k-dimensional tree), optimized for indexing and searching over multiple dimensions. Each dimension can represent a distinct numeric field (e.g., latitude, longitude, or timestamp).

  - **Splitting Mechanism**: The tree splits data points along axes at each level, grouping data into smaller ranges or "blocks." Each block holds a set of points within a defined range.
  - **Hierarchical Storage**: At each level of the tree, the data is divided along a specific dimension, allowing efficient narrowing of the search space during queries.

- #### **Using BKD Trees for Queries**
  BKD trees efficiently handle:
  - **Range Queries**: For fields like timestamps, BKD trees allow fast filtering of documents within a date range.
  - **Geo-Distance Queries**: For `geo_point` fields, Elasticsearch calculates distances within BKD trees, efficiently handling "find near" queries.

  **Example**:
  - Suppose you have a field `geo_point` representing user locations. A BKD tree indexes these coordinates, allowing Elasticsearch to quickly retrieve points within a bounding box or radius without scanning all documents.

- #### **Advantages of BKD Trees**
  - **Multi-dimensional Support**: BKD trees can store high-dimensional points, making them versatile for multi-dimensional data like 3D spatial points or multiple numeric fields.
  - **Efficient Storage**: Stores blocks instead of individual points, minimizing memory usage.
  - **Fast Range Queries**: Since data is stored in a hierarchical fashion, range and bounding box queries can quickly navigate through the tree, skipping irrelevant nodes.


#### **4.3.2. Doc Values**

**Doc values** enable efficient retrieval of field values for sorting, aggregation, and faceting. Instead of retrieving data from inverted indexes (which are optimized for search), doc values provide a **columnar storage** format that is ideal for analytical tasks.

- #### **Structure of Doc Values**
  Doc values store fields in **column-oriented** storage rather than row-oriented storage:
  - **Columnar Format**: Each field’s values are stored together, allowing fast access to all values of a single field across multiple documents.
  - **On-Disk Storage**: Doc values are stored on disk rather than in memory, allowing efficient memory usage and enabling large datasets to be queried without excessive RAM usage.

- #### **Types of Doc Values**
  Doc values are defined by the field type:
  - **Numeric Doc Values**: For fields with numeric data types, enabling efficient sorting and numeric aggregation (e.g., `sum`, `avg`).
  - **Binary Doc Values**: Used for keyword fields, allowing sorting and aggregation on text data.
  - **Geo-point Doc Values**: Specifically for geographic points, enabling geo-distance calculations.

- #### **Using Doc Values in Queries**
  Doc values are essential for operations such as:
  - **Sorting**: Sorting a results set by a field (e.g., price, timestamp) retrieves the field values directly from doc values.
  - **Aggregations**: Aggregating data (e.g., calculating the average rating) can access all values of a field across documents, significantly speeding up aggregation operations.
  - **Faceting and Filtering**: Fast access to field values in columnar format enables Elasticsearch to quickly generate facets and apply filters.

  **Example**:
  - Sorting results by `price` in a large index of products:
    - Doc values store `price` in a single column, which Elasticsearch reads to quickly sort documents without scanning each document individually.

- #### **Advantages of Doc Values**

  - **Efficient Sorting and Aggregation**: Since doc values are structured columnar data, they allow fast and memory-efficient operations.
  - **Reduced Memory Usage**: Storing doc values on disk and only loading them as needed minimizes memory consumption.
  - **Columnar Efficiency**: Doc values allow Elasticsearch to quickly access specific field values across documents, ideal for analytical queries.

#### 4.3.3. Summary of Lucene Data Structures in Elasticsearch

| Data Structure      | Purpose                                         | Use Cases                          | Benefits                             |
|---------------------|-------------------------------------------------|------------------------------------|--------------------------------------|
| **Segments**        | Immutable sub-indices within a shard            | All document storage               | Concurrent searches, immutability    |
| **Inverted Index**  | Maps terms to documents                         | Full-text search                   | Fast term lookups                    |
| **BKD Trees**       | Indexes numeric and multidimensional data       | Geospatial, timestamp queries      | Efficient range queries              |
| **Doc Values**      | Columnar storage for fields                     | Sorting, aggregations              | Optimized memory usage               |
| **Point Data Types**| Indexes geographic points (latitude-longitude)  | Proximity, bounding box queries    | Fast geospatial indexing             |  

### 4.4. **Advanced Data Structures in Lucene**

Let's dive into the **point data types and spatial indexes** used in Elasticsearch, especially focusing on how it handles **geospatial data** with **`geo_point` fields**. We’ll look at how **Quadtrees** and **R-trees** work, their role in spatial indexing, and how they support geospatial queries such as **bounding box** and **proximity searches**.

#### 4.4.1. **Overview of Spatial Data in Elasticsearch**

Elasticsearch supports geospatial data using the **`geo_point`** and **`geo_shape`** data types:
- **`geo_point`**: Stores latitude-longitude pairs for points on a map and is primarily used for proximity searches (e.g., “find locations within 10km”).
- **`geo_shape`**: Used for more complex shapes, such as polygons or multipoints, and is suitable for defining geographical areas like cities or lakes.

Geospatial queries include:
- **Bounding Box Queries**: Searches for documents within a specific rectangle defined by coordinates.
- **Distance Queries**: Searches for documents within a specified radius from a point.
- **Polygon Queries**: Searches for documents within or intersecting with a complex polygonal area.

#### 4.4.2. **Quadtrees and R-trees in Spatial Indexing**

**Quadtrees** and **R-trees** are tree-based data structures that organize spatial data by dividing the space into hierarchical grids or regions, allowing efficient geospatial query processing.

#### **Quadtrees**

Quadtrees are **hierarchical, 2-dimensional spatial indexes** that recursively partition space into four quadrants or nodes, making them highly suitable for spatial data like latitude and longitude pairs.

- **Structure**:
  - Each node (region of space) is divided into four sub-nodes or quadrants.
  - The depth of the tree (how many times it splits) depends on data density, as each quadrant only further divides if it contains more than a specified number of points.

- **How it Works**:
  - **Indexing**: Points are inserted into a quadrant based on their coordinates. If a quadrant exceeds the point threshold, it splits into four sub-quadrants, each storing a subset of the points.
  - **Querying**: For a bounding box or radius query, the algorithm quickly eliminates entire quadrants that fall outside the search area, only scanning relevant sub-quadrants, drastically reducing search space.

- **Use Cases**:
  - **Bounding Box Queries**: Quadtrees can quickly locate all points within a bounding box by navigating through relevant quadrants.
  - **Proximity Queries**: Proximity searches are efficient since the tree can “zoom in” to specific areas, ignoring distant quadrants.

**Example**:
Imagine we have a city map with thousands of restaurants, each represented as a point (latitude, longitude).
- A quadtree organizes the map into quadrants based on restaurant density. Denser regions are divided further to create sub-quadrants.
- To find restaurants within a specific neighborhood, the quadtree quickly filters out distant quadrants, only scanning nearby ones, significantly speeding up search.

#### **Advantages of Quadtrees**
- **Efficiency**: The hierarchical division allows for fast data retrieval.
- **Scalability**: Quadtrees adapt to dense data by increasing tree depth, meaning they can efficiently handle varied densities in spatial data.
- **Low Overhead**: Quadtrees are relatively simple to implement and optimize for 2D spaces.


#### **R-trees**

R-trees are another popular spatial data structure used to index multi-dimensional data (e.g., geographic shapes) by grouping nearby objects in bounding rectangles.

- **Structure**:
  - An R-tree organizes data into **bounding rectangles** called nodes. Each node represents a group of data points or shapes.
  - These bounding rectangles are arranged hierarchically: each parent node contains child nodes that represent sub-regions.
  - Each leaf node in an R-tree contains actual data points or bounding rectangles for objects (e.g., specific areas on a map).

- **How it Works**:
  - **Indexing**: When a shape or point is added, it’s placed within a bounding rectangle in a leaf node. If the rectangle becomes too crowded, the node splits, creating new bounding rectangles for its child nodes.
  - **Querying**: For a search (e.g., finding locations within a radius), the R-tree filters bounding rectangles that don’t intersect the search area, allowing the query to narrow down results without exhaustive scans.

- **Use Cases**:
  - **Polygon Queries**: R-trees are ideal for complex shapes like polygons, as each node can hold bounding rectangles for large or complex areas.
  - **Intersection or Overlap Queries**: R-trees can efficiently check for intersections between shapes, such as finding areas that overlap with a given region.

**Example**:
Consider a map with various regions, like parks, lakes, and neighborhoods, each represented as a polygon.
- An R-tree groups these polygons in bounding rectangles based on location. Polygons that are close to each other fall under the same rectangle.
- When searching for parks within a 5km radius, the R-tree discards rectangles outside this range, only exploring relevant areas to find matching polygons.

#### **Advantages of R-trees**
- **Supports Complex Shapes**: Suitable for irregular or large polygons, making them valuable for indexing diverse geographic data.
- **Efficient Spatial Queries**: Ideal for intersection, containment, and nearest-neighbor queries.
- **Adaptive Structure**: R-trees adapt their structure based on the spatial layout of data, minimizing wasted space in the tree.


#### 4.4.3. **Lucene's Internal Use of Quadtrees and R-trees**

While Elasticsearch doesn’t directly expose Quadtrees and R-trees as configurations, Lucene, its underlying search library, utilizes versions of these structures to handle spatial indexing efficiently.

- **Quadtrees in Lucene**:
  - Used for simple geospatial data like points, where dividing space into quadrants improves search performance.
  - Points (like those in `geo_point` fields) benefit from this structure, as it speeds up bounding box queries and basic distance queries.

- **R-trees in Lucene**:
  - Suited for `geo_shape` fields and other complex shapes where simple 2D point indexing isn’t enough.
  - Helps manage and query large polygons, multi-points, and other shapes with irregular boundaries.

Lucene optimizes these data structures to fit within its **segment-based** storage, allowing them to scale across multiple indices and segments, handling both large-scale geospatial queries and basic point-based distance queries.

#### 4.4.4. **Spatial Query Processing in Elasticsearch**

Using these data structures, Elasticsearch processes spatial queries as follows:

1. **Bounding Box Query**:
   - For a rectangular region, Elasticsearch leverages Quadtrees to restrict the search space to quadrants that intersect with the bounding box. Points or shapes within these quadrants are retrieved.

2. **Distance Query**:
   - For a proximity search (e.g., finding locations within 5km of a point), the **Geo Distance Filter** calculates distances from a central point and retrieves points from quadrants or nodes that fall within this radius.

3. **Polygon Query**:
   - For complex polygons (e.g., “find all parks within a specific neighborhood”), Elasticsearch uses an R-tree structure to store polygonal shapes in bounding rectangles, allowing fast intersection tests with other regions.


#### 4.4.5. **Summary Table of Spatial Data Structures in Elasticsearch**

| Data Structure | Purpose                            | Use Cases                          | Key Characteristics                           |
|----------------|------------------------------------|------------------------------------|----------------------------------------------|
| **Quadtrees**  | Efficient point indexing           | Bounding box, proximity searches   | Hierarchical grid of quadrants               |
| **R-trees**    | Complex shape and polygon indexing | Intersection, overlap queries      | Bounding rectangles with hierarchical nodes  |
| **BKD Trees**  | Multi-dimensional numeric data     | Numeric and geo-distance filters   | Balanced k-d tree with blocks of data points |


#### 4.4.6. **Practical Applications and Benefits**

These data structures optimize spatial queries in Elasticsearch, allowing it to handle diverse geospatial data efficiently. For example:
- **Bounding Box Queries** are accelerated by Quadtrees, making them ideal for finding all points in a geographic area.
- **Distance Queries** are optimized by both Quadtrees and BKD trees, allowing real-time retrieval of nearby points.
- **Polygon Queries** are handled by R-trees, which efficiently manage irregular shapes and large polygons for accurate intersection checks.

By integrating these structures into Lucene, Elasticsearch supports powerful geospatial capabilities across various applications, including mapping services, logistics, and location-based searches. 

### 4.5. How Lucene's Structures Fit into Elasticsearch’s Query Flow

1. **Document Indexing**:
   - As documents are indexed, Lucene tokenizes text fields, stores terms in the inverted index, and creates doc values for fields that require sorting or aggregation.

2. **Segment Creation**:
   - Documents are grouped into segments, with each segment containing its own inverted index, BKD trees, and doc values.

3. **Query Execution**:
   - **Term-based Queries**: The inverted index quickly retrieves documents containing specific terms.
   - **Numeric or Geospatial Queries**: BKD trees are used to retrieve documents within a certain numeric range or geographic area.
   - **Sorting and Aggregation**: Doc values facilitate sorting by loading field values column-by-column rather than document-by-document.

Lucene’s well-designed structures—segments, inverted indexes, and multidimensional BKD trees—create the foundation for Elasticsearch’s speed and scalability, enabling it to support complex queries and large datasets efficiently. 

---

### 5. **Visual Representation of Cluster Architecture (Hierarchy Flow)**

---

```plaintext
Elasticsearch Cluster
└── Nodes
    ├── Master Node
    │   ├── Manages cluster state
    │   └── Handles shard allocation and rebalancing
    ├── Data Node
    │   ├── Stores data and handles indexing/searching
    │   ├── Manages primary and replica shards
    │   └── Processes local queries and aggregations
    ├── Coordinating Node
    │   ├── Routes client requests to data nodes
    │   ├── Aggregates responses from data nodes
    │   └── Sends final response to the client
    └── Ingest Node
        ├── Processes and transforms data before indexing
        └── Enriches data with pipelines

Index
└── Shards (Primary and Replica)
    └── Lucene Index (Each shard is a Lucene index)
        ├── Segments (Immutable data units in a shard)
        │    ├── Inverted Index
        │    ├── Doc Values
        │    └── BKD Trees (for numeric & geo fields)
        └── Documents (JSON objects representing data records)(within segments)

```

---

### 6. **Summary of Cluster Roles and Data Flow**

---

| Component      | Description                                 | Responsibilities                                                  |
|----------------|---------------------------------------------|-------------------------------------------------------------------|
| Cluster        | Top-level structure with multiple nodes     | Manages overall data distribution, availability, and search       |
| Master Node    | Brain of the cluster                        | Handles cluster state, shard allocation, and fault tolerance      |
| Data Node      | Primary storage and processing node         | Stores data, handles indexing, querying, and replica management   |
| Coordinating Node | Routes and aggregates client requests    | Routes requests to data nodes, aggregates responses, and sends back to clients |
| Ingest Node    | Data transformation node                    | Preprocesses data with pipelines, ideal for parsing and enrichment |
| Index          | Logical grouping of documents               | Organizes data for efficient storage and querying                 |
| Shard          | Distributed subset of index data            | Represents a Lucene index with primary and replica copies         |
| Segment        | Immutable unit in a shard                   | Stores indexed data for fast read access                          |
| Document       | Basic data unit in segments                 | JSON object representing individual data records                  |

---

## Thread Pools

Diving into Elasticsearch’s search and other thread pools is crucial to understanding its performance and scalability. These pools are essential for managing various tasks like indexing, searching, and handling incoming requests. Let’s go through these pools from end to end, covering configurations, management, and performance metrics to monitor.

---
### 1. **Overview**
---
   - Elasticsearch uses multiple thread pools to handle different types of tasks.
   - Each pool is configured with a specific number of threads, queue sizes, and rejection policies.
   - Key pools include:
     - **Search Pool**: Handles search requests and related operations.
     - **Index Pool**: Manages indexing requests, processing, and writing data.
     - **Get Pool**: Manages document retrieval requests (like `GET` requests for individual documents).
     - **Bulk Pool**: Handles bulk indexing operations.
     - **Management Pool**: Manages internal maintenance tasks, like merging indices and refreshes.
     - **Snapshot Pool**: Handles operations related to creating and restoring snapshots.

---
### 2. **Detailed Breakdown of Key Pools**
---
   Here’s a closer look at each pool, along with common configurations and considerations:

   - **Search Pool**
     - **Purpose**: Processes search queries, sorting, aggregations, and other search-related tasks.
     - **Configuration**: 
       - `threads`: Generally calculated based on available CPU cores. It can be configured as `# of processors * 3`.
       - `queue_size`: Defines the maximum number of requests that can be queued before rejecting new ones. Default is `1000`.
       - **Tuning Tips**: Increase `queue_size` if you experience high query load, but monitor memory use carefully to avoid `OutOfMemory` errors.
     - **Metrics to Monitor**:
       - `search_pool.active`: Shows active threads handling search requests.
       - `search_pool.queue`: Tracks pending search requests.
       - `search_pool.rejected`: Indicates rejected requests due to an overloaded pool.

   - **Index Pool**
     - **Purpose**: Handles indexing requests to add or update documents.
     - **Configuration**:
       - `threads`: Typically set to the number of processors.
       - `queue_size`: Default is `200`. If your indexing rate is high, you may need to increase this.
       - **Tuning Tips**: Optimize for high throughput if you’re indexing frequently, but monitor for increased latency.
     - **Metrics to Monitor**:
       - `index_pool.active`: Active threads indexing data.
       - `index_pool.queue`: Number of indexing requests waiting in the queue.
       - `index_pool.rejected`: Tracks rejected indexing requests.

   - **Get Pool**
     - **Purpose**: Manages retrieval requests like fetching individual documents.
     - **Configuration**:
       - `threads`: Tied to the number of processors, generally around `# of processors * 2`.
       - `queue_size`: Default is `1000`.
     - **Metrics to Monitor**:
       - `get_pool.active`: Active threads processing `GET` requests.
       - `get_pool.queue`: Queued retrieval requests.
       - `get_pool.rejected`: Indicates if any requests are being rejected.

   - **Bulk Pool**
     - **Purpose**: Optimizes bulk indexing operations, commonly used for high-throughput data ingestion.
     - **Configuration**:
       - `threads`: Usually set as `# of processors * 2`.
       - `queue_size`: Often set at `50` to limit memory usage.
     - **Metrics to Monitor**:
       - `bulk_pool.active`: Active threads handling bulk operations.
       - `bulk_pool.queue`: Queued bulk requests.
       - `bulk_pool.rejected`: Shows if the bulk pool is rejecting requests due to overload.

   - **Management Pool**
     - **Purpose**: Manages background tasks like index merging and cleanup operations.
     - **Configuration**:
       - Configured with `1` to `5` threads as these tasks are not frequent.
     - **Metrics to Monitor**:
       - `management_pool.active`: Active management threads.
       - `management_pool.queue`: Queued management tasks.
       - **Tip**: Monitor this to ensure background tasks aren’t blocking other resources.

   - **Snapshot Pool**
     - **Purpose**: Handles backup and snapshot-related tasks, essential for data recovery and backups.
     - **Configuration**:
       - Configured with a low thread count (1 or 2).
     - **Metrics to Monitor**:
       - `snapshot_pool.active`: Active threads handling snapshots.
       - `snapshot_pool.queue`: Queued snapshot operations.

---
### 3. **Configuring and Tuning Thread Pools**
---
   - **Heap Size**: Ensure your heap size is appropriate for the load. Elasticsearch requires sufficient memory to handle pool tasks without bottlenecks.
   - **CPU Allocation**: Configure your server's CPU allocation based on thread pool demand. Pools that handle intensive operations, like search and indexing, benefit from additional CPU resources.
   - **Queue Size and Rejection Policies**:
     - Increase queue size if you see a consistent rise in queued tasks, but watch for higher memory usage.
     - Rejection policies control what happens when a queue is full; the default policy discards excess requests, but this can be customized if needed.

---
### 4. **Monitoring Metrics for Thread Pools**
---
   - **Cluster Health and Node Stats**: Regularly monitor cluster health metrics for thread pool performance, looking for bottlenecks or rejected tasks.
   - **Node-level Monitoring**: Elasticsearch provides detailed metrics per node. Monitoring `node_stats.thread_pool.*` via monitoring tools (like Kibana or Prometheus) helps assess each pool's workload.
   - **Typical Metrics**:
     - `active`: Indicates active threads. High values suggest a busy node.
     - `queue`: Shows queued tasks. A consistent rise may indicate that the pool size needs tuning.
     - `rejected`: Rejected tasks are a clear sign of bottlenecks.
---
### 5. **Example Scenarios and Best Practices**
---
   - **High Search Load**: If the search pool is frequently rejecting requests, consider increasing CPU allocation, thread count, or queue size.
   - **High Indexing Load**: For frequent data ingestion, optimize the bulk and index pools. Consider using the bulk API with a balanced queue size and monitor memory.
   - **Snapshot Operations**: Schedule snapshots during off-peak hours to reduce the impact on performance, as snapshot operations consume significant I/O.

---
### 6. **Summary Table of Pools**
---


| **Thread Pool**     | **Purpose**                              | **Default Threads**               | **Default Queue Size** | **Key Metrics**                   | **Tuning Tips**                                                                                              |
|---------------------|------------------------------------------|-----------------------------------|-------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------|
| **Search Pool**     | Processes search queries, aggregations   | `# of processors * 3`            | 1000                    | `search_pool.active`, `search_pool.queue`, `search_pool.rejected` | Increase `queue_size` if many queries are queued; monitor memory usage to prevent `OutOfMemory` issues.     |
| **Index Pool**      | Handles indexing requests for documents  | `# of processors`                | 200                     | `index_pool.active`, `index_pool.queue`, `index_pool.rejected` | For high indexing rates, increase queue size and thread count as necessary.                                 |
| **Get Pool**        | Retrieves individual documents           | `# of processors * 2`            | 1000                    | `get_pool.active`, `get_pool.queue`, `get_pool.rejected`      | Increase `queue_size` if retrieval requests are high; monitor latency and resource usage.                   |
| **Bulk Pool**       | Processes bulk indexing operations       | `# of processors * 2`            | 50                      | `bulk_pool.active`, `bulk_pool.queue`, `bulk_pool.rejected`   | Keep `queue_size` modest to limit memory use; monitor latency during high bulk loads.                       |
| **Management Pool** | Manages maintenance tasks like merges    | 1–5                              | 5                       | `management_pool.active`, `management_pool.queue`             | Generally low usage; monitor only if `queue` is frequently non-empty, indicating background task delays.    |
| **Snapshot Pool**   | Handles snapshot creation and restoration| 1–2                              | 5                       | `snapshot_pool.active`, `snapshot_pool.queue`                 | Schedule snapshots during low-activity periods; adjust resources if snapshots interfere with other tasks.    |


These pools are essential to optimizing Elasticsearch’s handling of diverse workloads. Monitoring and adjusting each pool based on your workload ensures better performance and resource management across the Elasticsearch cluster.

---
## Caching

In Elasticsearch, caching plays a vital role in speeding up queries by storing frequently accessed data at various levels, minimizing I/O operations and improving response times. Here’s a detailed, end-to-end look at caching in Elasticsearch, from the lowest level to the highest, covering each caching mechanism and its role.

### 1. **Types of Caches in Elasticsearch**

   There are several caching mechanisms in Elasticsearch, each working at a different level:

   - **Filesystem Cache (OS-level)**
   - **Shard-level Cache**
     - **Field Data Cache**
     - **Query Cache**
     - **Request Cache**
   - **Node-level Cache**
     - **Segment Cache**
     - **Indices Cache**

Each of these caches serves a specific purpose and optimizes a different aspect of query processing.

### 2. **Filesystem Cache (OS-level)**
   - **Location**: Managed by the operating system (OS) outside Elasticsearch.
   - **Purpose**: The OS-level filesystem cache holds frequently accessed files and segments in memory, reducing the need for repeated disk access.
   - **How it Works**: Elasticsearch relies on the OS filesystem cache to store files like index segments, which can be memory-mapped to avoid reading from disk. The OS determines which files to cache based on access patterns.
   - **Optimization Tips**: Ensure that sufficient system memory is available for filesystem caching, as Elasticsearch performs best when a large portion of indices can be cached at this level.

### 3. **Shard-level Cache**

#### **3.1. Field Data Cache**
   - **Purpose**: Speeds up operations that require field data, like sorting and aggregations, especially on text fields with `keyword` or `numeric` types.
   - **How it Works**: When a field is accessed for sorting or aggregations, Elasticsearch loads the field data into memory as a field data cache. Field data caching is costly in terms of memory.
   - **Eviction Policy**: Field data cache is held in memory, and it’s evicted based on the `indices.fielddata.cache.size` setting. Default eviction strategy is based on the Least Recently Used (LRU) policy.
   - **Metrics to Monitor**:
     - `fielddata.memory_size`: Shows the amount of memory used by field data.
     - `fielddata.evictions`: Tracks the number of evictions, which can indicate if your cache size is too small.

#### **3.2. Query Cache**
   - **Purpose**: Caches the results of frequently used queries to reduce re-execution. Especially useful for filters and term-level queries.
   - **How it Works**: When the same query is run multiple times, Elasticsearch caches the results at the shard level, so it doesn’t have to re-run the same logic.
   - **Query Types Cached**: Primarily caches filters like `term` queries and `range` queries, but does not cache full-text queries as they are more complex and vary widely.
   - **Eviction Policy**: LRU-based. Controlled by the `indices.queries.cache.size` setting (default is 10% of the heap).
   - **Metrics to Monitor**:
     - `query_cache.memory_size`: Memory occupied by the query cache.
     - `query_cache.evictions`: Counts the number of evictions due to cache full.
     - `query_cache.hit_count` and `query_cache.miss_count`: Useful for determining cache effectiveness.

#### **3.3. Request Cache**
   - **Purpose**: Optimizes entire search requests that involve aggregations and aren’t dependent on recent data changes.
   - **How it Works**: Unlike the query cache, which stores results of individual query segments, the request cache stores results of entire search requests. It's effective for costly aggregation queries on static data.
   - **Conditions**: Only applies to requests marked with `request_cache=true` and doesn’t cache when `track_total_hits` is enabled.
   - **Eviction Policy**: Configurable per index with `index.requests.cache.enable` (enabled by default). Also LRU-based.
   - **Metrics to Monitor**:
     - `request_cache.memory_size`: Shows the memory consumed by the request cache.
     - `request_cache.evictions`: Tracks evictions in the request cache.
     - `request_cache.hit_count` and `request_cache.miss_count`: Indicates request cache hit ratio.

### 4. **Node-level Cache**

#### **4.1. Segment Cache**
   - **Purpose**: Manages Lucene’s in-memory structures, like field terms, postings, and dictionaries, which are essential for indexing and search operations.
   - **How it Works**: Lucene, the underlying library in Elasticsearch, caches segments of the index in memory to allow for faster retrieval of terms, term frequencies, and position information.
   - **Eviction Policy**: Handled by Lucene based on access frequency and memory limits. It’s self-managed but benefits from larger heap sizes.
   - **Metrics to Monitor**:
     - `segment.memory_in_bytes`: Memory used for cached segments.
     - `segments.count`: Number of segments in the index.
   - **Optimization Tips**: Reduce segment merging frequency if memory is limited, but note that a higher number of segments can reduce performance.

#### **4.2 Indices Cache**
   - **Purpose**: Stores certain metadata for all indices within the cluster to speed up operations that retrieve or refresh metadata.
   - **How it Works**: Caches index mappings and settings to avoid repeatedly loading them from the disk, enabling faster index and shard management.
   - **Eviction Policy**: LRU-based, with cache size managed by the heap space available.
   - **Metrics to Monitor**: `indices.memory` and `indices.evictions` to track the memory usage and evictions for index metadata.

### 5. **Cache Tuning and Best Practices**

   - **Heap Size and Garbage Collection (GC)**: Elasticsearch caches are memory-intensive, so allocate sufficient heap space. Optimal heap size is typically 50% of physical memory, up to 32GB.
   - **Cache Sizes**: Configure cache sizes based on your workload. If queries are complex, increase `query_cache` and `request_cache`. For frequent aggregations, prioritize `field data cache`.
   - **Monitoring and Adjustment**: Regularly monitor cache-related metrics. High eviction rates or memory pressure indicate that cache sizes should be increased.
   - **Use `fscache`**: The filesystem cache is essential, so configure sufficient memory for the OS to hold index files and segments, avoiding excessive I/O operations.
   - **Selective Caching**: For frequently accessed static data, explicitly enable request and query caching by setting `request_cache=true` in your queries or adjusting cache policies at the index level.

### 6. **Summary Table of Caching Mechanisms**

| **Cache Type**           | **Level**       | **Purpose**                                         | **Eviction Policy**       | **Key Metrics**                             | **Tuning Tips**                                    |
|--------------------------|-----------------|-----------------------------------------------------|----------------------------|---------------------------------------------|----------------------------------------------------|
| **Filesystem Cache**     | OS              | Stores files and segments in OS memory              | Managed by OS              | N/A                                         | Ensure ample OS memory for larger index caching    |
| **Field Data Cache**     | Shard-level     | Caches field data for aggregations and sorting      | LRU-based, configurable    | `fielddata.memory_size`, `fielddata.evictions` | Increase size for high aggregation requirements   |
| **Query Cache**          | Shard-level     | Caches individual filter query results              | LRU-based, configurable    | `query_cache.memory_size`, `query_cache.evictions`, `query_cache.hit_count`, `query_cache.miss_count` | Monitor hit/miss ratios to determine effectiveness |
| **Request Cache**        | Shard-level     | Caches entire search request results                | LRU-based, per-index       | `request_cache.memory_size`, `request_cache.evictions`, `request_cache.hit_count`, `request_cache.miss_count` | Best for aggregations on static data              |
| **Segment Cache**        | Node-level      | Caches Lucene index segments and postings           | Managed by Lucene          | `segment.memory_in_bytes`, `segments.count`  | Larger heap size improves cache efficiency         |
| **Indices Cache**        | Node-level      | Caches index metadata (mappings, settings)          | LRU-based                  | `indices.memory`, `indices.evictions`       | Adjust based on frequency of metadata updates      |

Each caching layer works in tandem to optimize query speed and efficiency, and monitoring these caches is essential for fine-tuning Elasticsearch performance to meet your specific use cases.

---
## Index Life Management - ILM

Elasticsearch’s **Hot-Warm-Cold (Hot-Warm-Cold-Frozen)** architecture is part of its **Index Lifecycle Management (ILM)** system, designed to optimize storage and cost-efficiency for managing data that has different usage patterns over time. This architecture allows you to store data on different types of nodes (hot, warm, cold, and frozen) based on data retention needs and access frequency. Here’s an in-depth look at each phase and how to effectively manage data with Elasticsearch’s ILM:

---

### 1. **Purpose of Hot-Warm-Cold Architecture**
   - Optimizes storage costs and resource allocation based on the age and usage pattern of the data.
   - Maximizes query performance for recent data while preserving older data with reduced resource consumption.
   - Enables scalable management of large data volumes across different hardware types or cloud storage.

### 2. **Phases in ILM (Index Lifecycle Management)**

The ILM policy defines actions to transition data through different stages based on time or data access patterns:

   - **Hot Phase**: For recent, frequently accessed data.
   - **Warm Phase**: For less frequently queried but still relevant data.
   - **Cold Phase**: For older data accessed occasionally.
   - **Frozen Phase**: For archived data with very low access frequency.
   - **Delete Phase**: Optionally, data is deleted at the end of its lifecycle.

### 3. **Hot Phase (High-Performance Node Configuration)**

   - **Purpose**: Stores actively ingested and queried data.
   - **Hardware Requirements**: High CPU, fast SSD storage, and large memory (RAM) for fast indexing and search.
   - **Shard Configuration**:
     - Use a smaller number of larger shards to optimize indexing speed.
     - Allocate more replicas if redundancy and high read performance are necessary.
   - **ILM Actions**:
     - **Rollover**: Create a new index after a certain data volume or time threshold is met (e.g., daily index rollover).
     - **Force Merge**: Reduce the number of segments per shard to improve read efficiency.
   - **Common Use Cases**: Logging, analytics on recent data, real-time application monitoring.

### 4. **Warm Phase (Mid-Performance Node Configuration)**

   - **Purpose**: Stores data that is queried less frequently but is still relevant for analysis.
   - **Hardware Requirements**: Moderate CPU, lower-cost storage (often SSD or HDD), and less RAM compared to hot nodes.
   - **Shard Configuration**:
     - May have fewer replicas compared to the hot phase, given reduced query demands.
     - Shards are consolidated to optimize storage.
   - **ILM Actions**:
     - **Shrink**: Shrink the index to fewer primary shards to save resources.
     - **Force Merge**: Further reduce segments for efficiency.
     - **Move to Warm Nodes**: Transfers data from hot to warm nodes for cost-effective storage.
   - **Common Use Cases**: Historical data analysis, infrequent but accessible data, dashboards showing past data trends.

### 5. **Cold Phase (Cost-Effective Node Configuration)**

   - **Purpose**: Stores older data that is rarely accessed but still retained for compliance or occasional analysis.
   - **Hardware Requirements**: Lower-cost HDD storage, lower CPU, and RAM. Cold nodes can tolerate higher latency.
   - **Shard Configuration**:
     - Minimal number of replicas, as high availability is less critical.
     - Shards can be larger as search latency is not a priority.
   - **ILM Actions**:
     - **Move to Cold Nodes**: Moves indices to cold nodes to reduce costs.
     - **Freeze Index**: Index is searchable but placed in a lower-memory footprint mode.
     - **Force Merge to a Single Segment**: Further reduces the number of segments.
   - **Common Use Cases**: Compliance storage, infrequently accessed logs, backup data for low-priority analysis.

### 6. **Frozen Phase (Archival Node Configuration)**

   - **Purpose**: Stores archived data that is very rarely accessed.
   - **Hardware Requirements**: Can use very low-cost storage options, including external storage like S3 or other blob storage.
   - **Shard Configuration**:
     - Data in the frozen phase is accessed directly from disk without caching in memory.
     - Lowest possible number of replicas, if any.
   - **ILM Actions**:
     - **Move to Frozen Nodes**: Moves data to frozen nodes or even object storage to further reduce costs.
     - **Unfreeze on Query**: Data is unarchived only when accessed.
   - **Common Use Cases**: Long-term archives, historical records for compliance.

### 7. **Delete Phase (Optional Phase)**

   - **Purpose**: Automatically deletes data that has reached the end of its lifecycle.
   - **ILM Actions**:
     - **Delete Index**: Permanently removes data from the cluster.
   - **Common Use Cases**: Compliance-driven data retention policies, automated data cleanup.

### 8. **Setting Up ILM Policies**

   - Define an ILM policy specifying transitions and actions for each phase.
   - Assign the policy to indices at creation, typically through index templates.
   - The ILM policy automatically manages the transition of data through the hot, warm, cold, and frozen phases.

### 9. **Cluster Resource Optimization with Hot-Warm-Cold Architecture**

   - **Hot Nodes**: Provide high-speed access to recent data, require higher infrastructure investment.
   - **Warm Nodes**: Optimize costs while maintaining access to recent historical data with moderate query needs.
   - **Cold Nodes**: Cost-efficient storage for compliance or archival data.
   - **Frozen Nodes**: Minimal cost storage, including cloud or external storage options for rarely accessed data.

### 10. **Example ILM Policy**

   Here’s an example ILM policy to illustrate phase-based transitions for a log analytics use case:

   ```json
   {
     "policy": {
       "phases": {
         "hot": {
           "actions": {
             "rollover": {
               "max_age": "7d",
               "max_size": "50gb"
             },
             "set_priority": {
               "priority": 100
             }
           }
         },
         "warm": {
           "min_age": "30d",
           "actions": {
             "allocate": {
               "number_of_replicas": 1
             },
             "forcemerge": {
               "max_num_segments": 1
             },
             "set_priority": {
               "priority": 50
             }
           }
         },
         "cold": {
           "min_age": "90d",
           "actions": {
             "allocate": {
               "require": {
                 "data": "cold"
               }
             },
             "freeze": {},
             "set_priority": {
               "priority": 0
             }
           }
         },
         "delete": {
           "min_age": "365d",
           "actions": {
             "delete": {}
           }
         }
       }
     }
   }
   ```

   - **Hot Phase**: Rollover index every 7 days or 50GB.
   - **Warm Phase**: Transition after 30 days, reduce segment count, keep one replica.
   - **Cold Phase**: Transition after 90 days, freeze index to reduce memory usage.
   - **Delete Phase**: Automatically delete indices older than 365 days.

### 11. **Advantages of Hot-Warm-Cold Architecture**

   - **Cost Savings**: Reduces storage and resource costs by using lower-cost hardware for less-accessed data.
   - **Optimized Performance**: Ensures high performance for recent data by keeping it on high-speed hot nodes.
   - **Automated Management**: ILM policies automate data transitions, reducing operational overhead.
   - **Flexible Retention**: Facilitates retention policies to ensure compliance with data governance and regulatory requirements.

### 12. **Best Practices**

   - **Separate Nodes by Type**: Use dedicated nodes for each phase to optimize hardware based on the phase requirements.
   - **Frequent ILM Review**: Regularly review and adjust ILM policies based on access patterns and data growth.
   - **Monitor ILM Transitions**: Track transition performance to ensure data is moving efficiently between phases.

### 13. Summary Table

| **Phase**    | **Data Usage**        | **Hardware**                  | **ILM Actions**                         | **Typical Use Cases**                             |
|--------------|-----------------------|-------------------------------|-----------------------------------------|---------------------------------------------------|
| **Hot**      | Active, high access   | High CPU, SSD, large RAM      | Rollover, force merge                   | Real-time search, recent logs, app monitoring     |
| **Warm**     | Mid-range access      | Moderate CPU, SSD or HDD, RAM | Shrink, force merge, reallocate         | Data analytics on recent history, dashboard views |
| **Cold**     | Infrequent access     | Low CPU, HDD, minimal RAM     | Freeze, move to cold nodes              | Compliance storage, infrequent analysis           |
| **Frozen**   | Rarely accessed       | Minimal CPU/RAM, cloud storage| Unfreeze on access, move to frozen node | Long-term archival, compliance data               |
| **Delete**   | Expired data          | N/A                           | Delete                                  | Data lifecycle cleanup                            |

This Hot-Warm-Cold architecture in Elasticsearch enables you to balance cost, performance, and data accessibility across various hardware configurations, ensuring that data is always stored cost-effectively without compromising on necessary access patterns.


---
## List of REST APIs


| **API Endpoint**                | **Purpose**                                      | **Description** | **Typical Latency** | **Use Cases**                                       |
|---------------------------------|--------------------------------------------------|-----------------|----------------------|-----------------------------------------------------|
| **Index APIs**                  |                                                  |                 |                      |                                                     |
| `POST /{index}/_doc`            | Index a Document                                 | Adds or updates a document in the specified index. | Low (~5-10ms for small docs) | Real-time data ingestion, document updates.         |
| `POST /{index}/_bulk`           | Bulk Indexing                                    | Allows indexing, updating, or deleting multiple documents in a single request. | Low to Medium (~10-50ms) | High-volume data ingestion, ETL processes.          |
| `POST /{index}/_update/{id}`    | Update a Document                                | Partially updates a document by ID in the specified index. | Low to Medium (~10-30ms) | Updating specific fields in documents.              |
| `DELETE /{index}/_doc/{id}`     | Delete a Document                                | Deletes a document by ID from the specified index. | Low (~5-10ms) | Document removal based on unique IDs.               |
|                                 |                                                  |                 |                      |                                                     |
| **Search APIs**                 |                                                  |                 |                      |                                                     |
| `GET /{index}/_search`          | Search Documents                                 | Executes a search query with optional filters, aggregations, and pagination. | Medium to High (~10-100ms, based on complexity) | Full-text search, structured queries, analytics.    |
| `POST /{index}/_search/scroll`  | Scroll Search                                    | Enables retrieving large datasets by scrolling through search results. | Medium to High (~50-200ms) | Pagination for large datasets, data exports.        |
| `DELETE /_search/scroll`        | Clear Scroll Context                             | Clears scroll contexts to free up resources. | Low (~5ms) | Resource management after scroll search.            |
| `POST /_msearch`                | Multi-Search                                     | Allows execution of multiple search queries in a single request. | Medium to High (~20-150ms) | Batch querying, dashboard visualizations.           |
| `POST /{index}/_count`          | Count Documents                                  | Counts the documents matching a query without returning full results. | Low (~5-20ms) | Quick counts of filtered datasets.                  |
|                                 |                                                  |                 |                      |                                                     |
| **Aggregation APIs**            |                                                  |                 |                      |                                                     |
| `POST /{index}/_search`         | Aggregation Queries                              | Used with the search API to retrieve aggregated data (e.g., histograms, averages). | Medium to High (~20-150ms) | Analytics, reporting, data summarization.           |
|                                 |                                                  |                 |                      |                                                     |
| **Cluster and Node APIs**       |                                                  |                 |                      |                                                     |
| `GET /_cluster/health`          | Cluster Health                                   | Returns health information on the cluster, nodes, and indices. | Low (~5ms) | Monitoring cluster health and node status.          |
| `GET /_cluster/stats`           | Cluster Statistics                               | Provides statistics on cluster status, node usage, and storage. | Low to Medium (~5-20ms) | Cluster-wide monitoring and performance analysis.   |
| `POST /_cluster/reroute`        | Cluster Reroute                                  | Manually reroutes shards in the cluster. | Medium (~20-50ms) | Shard management and rebalancing.                   |
| `GET /_nodes/stats`             | Node Statistics                                  | Returns stats for nodes, including CPU, memory, and thread pools. | Low to Medium (~5-20ms) | Node health monitoring, resource usage analysis.    |
| `GET /_cat/nodes`               | List Nodes                                       | Provides a list of all nodes in the cluster in a human-readable format. | Low (~5ms) | Node overview, node status.                         |
| `GET /_cat/indices`             | List Indices                                     | Lists all indices with metadata on size, health, and document count. | Low (~5ms) | Index management and monitoring.                    |
|                                 |                                                  |                 |                      |                                                     |
| **Index Management APIs**       |                                                  |                 |                      |                                                     |
| `PUT /{index}`                  | Create Index                                     | Creates a new index with specific settings and mappings. | Low (~10-20ms) | Index setup and schema definition.                  |
| `DELETE /{index}`               | Delete Index                                     | Deletes the specified index. | Low (~5-10ms) | Index removal, data management.                     |
| `PUT /{index}/_settings`        | Update Index Settings                            | Updates settings (e.g., refresh interval) of an index. | Low (~10ms) | Dynamic adjustments of index settings.              |
| `POST /{index}/_refresh`        | Refresh Index                                    | Refreshes an index to make recent changes searchable. | Medium (~10-30ms) | Ensures data is available for search in near-real-time. |
| `POST /{index}/_forcemerge`     | Force Merge Index                                | Reduces the number of segments in an index to optimize storage. | High (~100ms - 1s+) | Optimize index storage, improve search speed.       |
|                                 |                                                  |                 |                      |                                                     |
| **Cache and Memory Management** |                                                  |                 |                      |                                                     |
| `POST /{index}/_cache/clear`    | Clear Index Cache                                | Clears the cache for the specified index. | Low (~5ms) | Cache management for performance tuning.            |
| `POST /_flush`                  | Flush Index                                      | Writes all buffered changes to disk. | Medium (~10-30ms) | Data durability, cache clearing.                    |
| `POST /{index}/_refresh`        | Refresh                                          | Makes recent changes to an index visible to search. | Medium (~10-30ms) | Near real-time updates in the search index.         |
|                                 |                                                  |                 |                      |                                                     |
| **Snapshot and Backup APIs**    |                                                  |                 |                      |                                                     |
| `PUT /_snapshot/{repo}/{snapshot}` | Create Snapshot                              | Creates a snapshot of indices for backup. | High (dependent on index size) | Data backup, disaster recovery.                     |
| `GET /_snapshot/{repo}/{snapshot}` | Get Snapshot Status                          | Checks the status of an existing snapshot. | Low (~5ms) | Monitor snapshot progress, status checks.           |
| `DELETE /_snapshot/{repo}/{snapshot}` | Delete Snapshot                          | Deletes an existing snapshot. | Medium (~10-20ms) | Snapshot lifecycle management, freeing storage.     |
|                                 |                                                  |                 |                      |                                                     |
| **Security and Role Management**|                                                  |                 |                      |                                                     |
| `POST /_security/role/{role}`   | Create/Update Role                               | Creates or updates a security role with specific permissions. | Low (~5-10ms) | Access control, role-based permissions.             |
| `POST /_security/user/{user}`   | Create/Update User                               | Creates or updates a user in Elasticsearch. | Low (~5-10ms) | User management, access permissions.                |
| `GET /_security/_authenticate`  | Authenticate User                                | Authenticates the current user. | Low (~5ms) | Session management, authentication checks.          |

### Additional Notes
- **Latency** varies by operation complexity, cluster size, and data volume. Basic CRUD operations and small management tasks typically have lower latencies.
- **Bulk operations** optimize high-volume data processing by batching requests, but they may slightly increase latency for individual actions due to combined processing.
- **Search and aggregation latencies** increase with query complexity, the number of documents involved, and the number of shards searched.

---

## Capacity Planning & Cluster Design


| **Parameter**               | **Formula / Best Practice**                                | **Description**                                                                                     | **Example Calculation**                                              |
|-----------------------------|------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|----------------------------------------------------------------------|
| **Total Data Nodes**        | \( \text{Total Shards} \times \text{Shard Size} / \text{Node Disk Capacity} \) | Calculate required data nodes based on total shard size and node storage.                           | 20 shards of 30GB each on 600GB nodes → 1 data node                 |
| **Heap Memory Allocation**  | \( \text{Total Node RAM} \times 0.5 \) (up to 32GB max)    | Allocate 50% of node memory to JVM heap, up to 32GB, to optimize memory usage.                      | Node with 64GB RAM → 32GB heap                                       |
| **Storage Requirement**     | \( \text{Total Data Size} \times (1 + \text{Replication Factor}) \times \text{Retention Period (months)} \) | Plan storage capacity based on expected data size and replication.                                  | 1TB data, 1 replica, 1-month retention → 2TB                         |
| **Ideal Shard Size**        | 20-50 GB per shard                                         | Recommended shard size for optimal performance and manageability.                                   | Aim for shards ~30GB each for balanced load                          |
| **Shards per Index**        | \( \text{Total Data Size} / \text{Target Shard Size} \)    | Calculate shard count to avoid excessive shard management overhead.                                 | 500GB index, 25GB target shard size → 20 shards                      |
| **Max Shard Count**         | Avoid more than 20 shards per GB of heap                   | Ensure shard count doesn’t exceed a level that can cause memory strain.                             | 32GB heap → max ~640 shards across all indices                       |
| **Master Nodes**            | Minimum of 3                                               | Dedicated master nodes ensure quorum-based fault tolerance and cluster stability.                   | At least 3 master nodes for high availability                        |
| **Coordinating Nodes**      | 1 per 10 data nodes in large clusters                      | Handle query load without adding data nodes, especially with complex aggregation queries.           | 50 data nodes → 5 coordinating nodes                                 |
| **CPU Requirement**         | 4-8 CPUs per data node                                     | Allocate enough CPUs to handle search and indexing operations without bottlenecks.                  | 4-8 CPUs per node for typical workloads                              |
| **Disk I/O Throughput**     | Minimum of 300 MB/s (write-heavy)                          | For write-intensive clusters, ensure sufficient I/O throughput for data nodes.                      | SSDs or fast disks are recommended                                   |
| **Disk Usage Threshold**    | Keep below 75% disk usage per node                         | Avoid exceeding 75% disk usage on data nodes to prevent performance degradation.                    | Monitor for 75% threshold, Elasticsearch throttles at ~85%           |
| **Index Throttle State**    | Throttles at ~85% disk usage                               | Elasticsearch throttles indexing if nodes exceed 85% disk usage.                                    | Configure alerts to prevent reaching throttle state                  |
| **Memory Usage - Field Data Cache** | `indices.fielddata.cache.size` based on available heap | Field data cache size impacts sorting and aggregation speed, stored in memory.                      | Set to 20-30% of available heap for high-aggregation workloads       |
| **Query Cache Size**        | 10% of heap by default (adjustable)                        | Caches filter queries to speed up repeated search operations.                                       | Increase for clusters with frequent repetitive queries               |
| **Request Cache Size**      | Enabled per index, LRU-based                               | Cache complete search requests, especially useful for aggregation-heavy queries on static data.     | Enable on indices with frequent aggregation queries                  |
| **Cluster Health**          | `GET /_cluster/health` (monitor green, yellow, red status) | Regularly monitor cluster health to identify potential issues in shard allocation and node status.  | Alerts for yellow or red statuses to detect unallocated shards       |
| **Pending Tasks**           | `GET /_cluster/pending_tasks`                              | Track the number of pending tasks; delays may signal node or cluster overload.                      | Monitor to ensure tasks are processed promptly                       |
| **CPU Usage per Node**      | Track via `node.cpu.percent`                               | Monitor CPU load on nodes, especially data and coordinating nodes handling heavy queries.           | Keep below 80% for balanced performance                              |
| **Search Latency**          | Monitor `search.fetch_time` and `search.query_time`        | Search latency metrics indicate performance bottlenecks; high values can suggest tuning is needed.  | Target <100ms for interactive queries, <500ms for aggregations       |
| **Indexing Latency**        | Monitor `indexing.index_time`                              | Tracks indexing speed; high values indicate indexing bottlenecks.                                   | Optimize disk I/O if consistently high                               |
| **GC Pause Time**           | Track `jvm.gc.collection_time`                             | Excessive GC pause time (>100ms) can degrade performance, especially on data nodes.                 | Keep heap usage <75% to avoid frequent GC pauses                     |
| **Disk Utilization**        | `disk.used_percent`                                       | Ensure disk usage remains within optimal limits to prevent resource contention.                     | Monitor for high usage, keep below 75%                               |
| **Heap Usage per Node**     | `jvm.heap_used_percent`                                   | Monitor heap usage across nodes; values near 100% can trigger frequent GC and degrade performance.  | Keep below 75% for stable performance                                |
| **Shard Count per Node**    | Shards should not exceed 50-75 per data node               | Optimal shard count balances memory usage and search latency.                                       | Distribute shards evenly to avoid bottlenecks                        |
| **Index Rollover Frequency** | Based on data ingestion and retention policy               | Use index rollover for high-ingestion use cases to manage shard size and count.                     | Time-based or size-based rollover (e.g., daily, 10GB)                |
| **Snapshot Frequency**      | Schedule during off-peak hours                             | Regular snapshots for backup without affecting active workloads.                                   | Daily or weekly snapshots for disaster recovery                      |
| **Ingest Node CPU Requirement** | Optimize for transformation-heavy workloads            | Ingest nodes need higher CPU for ETL tasks before indexing.                                         | ~8 CPUs per ingest node for transformation-heavy clusters            |
| **Write Thread Pool Size**  | Controlled via `thread_pool.write.size`                    | Configure thread pool size for write-heavy workloads.                                               | Default based on available processors; increase for high-write loads |
| **Bulk Thread Pool Size**   | Set via `thread_pool.bulk.size`                            | Bulk operations often have separate thread pools, useful for high-ingestion clusters.               | Default based on processors; increase if high bulk ingestion         |
| **Query Throughput**        | Measure `search.thread_pool.queue`                         | The search queue size indicates if the search load is too high, leading to delays.                  | Keep queue size low to avoid bottlenecks                             |
| **Bulk Queue Size**         | Monitor `bulk.thread_pool.queue`                           | Large bulk queue size indicates ingestion pressure; tune for high-ingestion needs.                 | Increase queue size or add ingest nodes for bulk-heavy workloads     |
| **Network Throughput**      | Monitor network interface utilization                      | High network usage can impact inter-node communication, especially during replication.              | Ensure sufficient network bandwidth for large clusters               |
| **Network Latency**         | Track round-trip time between nodes                        | Low-latency network is critical for distributed search and replication.                             | <1ms recommended within data centers, 1-5ms for cross-regions        |

---
## Questions

### 1. **SQL or NoSQL**: 
   - **NoSQL**: Elasticsearch is a NoSQL database designed for search and analytics on structured and unstructured data.
   - **Type of NoSQL**: Document-oriented, storing data as JSON documents.
   - **Supports Polymorphic?**: Yes, Elasticsearch can store documents with varying structures, making it flexible for polymorphic data storage.

### 2. **Main Feature, Origin, and Design**:
   - **Main Feature**: Optimized for full-text search, analytics, and distributed data storage.
   - **Built For**: Real-time search, logging, and analytics, especially for large datasets.
   - **Developer**: Developed by Shay Banon, later maintained by Elastic.co.
   - **Based on**: Built on top of Apache Lucene.

### 3. **OLAP or OLTP**:
   - **Primarily OLAP**: Elasticsearch is designed for OLAP workloads like analytics, aggregations, and complex search queries.
   - **Reason**: Its architecture optimizes for high-throughput search and analysis rather than transactional processing.

### 4. **ACID or BASE**:
   - **BASE**: Elasticsearch is eventually consistent and follows BASE principles. It supports near real-time (NRT) search, not strong consistency.
   - **ACID**: Limited ACID compliance, mostly per-document operations.

### 5. **CAP Theorem**:
   - **CAP Stance**: Elasticsearch prioritizes Availability and Partition tolerance, with eventual Consistency.
     - **Consistency**: Provides consistency in the sense that all nodes have the latest data after eventual replication.
     - **Availability**: If a node fails, Elasticsearch reroutes shards to ensure continuous availability.
     - **Partition Tolerance**: Designed to operate in distributed environments with node communication issues.

### 6. **Cluster Structure**:
   - **Cluster**: Comprises multiple nodes working together. Nodes store data and perform search and indexing.
   - **From Cluster to Records**:
     - **Cluster** → **Nodes** (Master, Data, Coordinating, Ingest) → **Indices** → **Shards** → **Documents** → **Fields/Records**.

### 7. **Fundamental Building Blocks**:
   - **Cluster** → **Nodes** → **Indices** → **Shards** → **Segments** → **Documents** → **Fields**.

### 8. **Multi-Master Support**:
   - No, Elasticsearch supports only a single active master node at any time. Multiple master-eligible nodes can be elected, but only one acts as the master.

### 9. **Master-Data Node Relation**:
   - Follows **Master-Slave** model: The master node manages cluster state and shard allocation, while data nodes store and manage data.

### 10. **Node Structures in Cluster**:
   - **Master Nodes**: Manage the cluster state.
   - **Data Nodes**: Store data and execute search and indexing.
   - **Coordinating Nodes**: Route requests and handle query aggregation.
   - **Ingest Nodes**: Pre-process documents before indexing.

### 11. **Cluster Scaling Support**:
   - **Horizontal Scaling**: Adding more nodes is preferred for scalability and balancing.
   - **Vertical Scaling**: Increasing node capacity is less flexible but possible.

### 12. **High Availability**:
   - High availability is achieved through replicas and shard redistribution. If a node fails, replicas ensure data accessibility.

### 13. **Fault Tolerance**:
   - If nodes fail, Elasticsearch automatically reallocates shards to other nodes to ensure continued operation.

### 14. **Replication**: 
  - Each shard has one or more replicas. Replicas provide redundancy and improve read throughput.

### 15. **Partition**:
   - Data is divided into partitions known as shards, which can be distributed across nodes.

### 16. **Sharding**:
   - An index is divided into shards. Sharding allows horizontal scaling by spreading data across nodes.

### 17. **Caching in Depth**:
   - **Filesystem Cache**: OS-level cache for segments and indices.
   - **Shard-level Cache**: Field data cache, query cache, and request cache for faster response on repeated queries.

### 18. **Storage Type**:
   - **Tree Structure**: Uses BKD trees and inverted indices for fast search on structured and unstructured data.
   - **Heap and Disk Storage**: Field data and segments stored in-memory and disk for efficient retrieval.

### 19. **Segment/Page Approach**:
   - **Segments**: Data is written to immutable segments, which are merged periodically.

### 20. **Trees for Storage**:
   - **BKD Trees**: Used for indexing numeric and geospatial data.
   - **Inverted Indices**: Store mappings for full-text search.

### 21. **Indexing**:
   - **How**: Data is indexed via inverted indices and BKD trees.
   - **Trees Used**: BKD trees for numeric and geospatial data.

### 22. **Routing**:
   - Documents are assigned to shards using hashing, ensuring distribution across nodes.

### 23. **Latency**:
   - **Write Latency**: Typically 5-10ms.
   - **Read Latency**: 5-20ms.
   - **Indexing Latency**: 10-30ms depending on shard count and node capacity.
   - **Replication Latency**: 20-50ms depending on network and shard replication.

### 24. **Versioning**:
   - Elasticsearch supports optimistic concurrency control with versioning at the document level.

### 25. **Locking and Concurrency**:
   - Optimistic locking, avoiding traditional locks to allow for distributed updates.

### 26. **Write-Ahead Log (WAL)**:
   - Elasticsearch does not use traditional WAL but relies on translog for write durability.

### 27. **Change Data Capture (CDC)**:
   - **CDC Support**: Limited; CDC can be simulated with the use of versioning and update tracking.

### 28. **Query Type and Query**:
   - **Types**: Full-text, term, range, Boolean, geospatial, and aggregations.
   - **In Depth**: Supports complex queries, scoring, and aggregations for analytics.

### 29. **Query Optimizers**:
   - Elasticsearch optimizes queries by caching, narrowing the search, and efficiently reading segments.

### 30. **SQL Support**:
   - Partial SQL support, primarily for querying via Elasticsearch SQL API.

### 31. **Circuit Breakers**:
   - Protects cluster stability by setting limits on memory use per request, query, or field data.

### 32. **Data Retention / Lifecycle Management**:
   - **Hot-Warm-Cold Architecture**: Data can be moved between hot (frequent access), warm (less frequent access), and cold (archived) nodes.

### 33. **Other Features**:
   - **Use Cases**: Log analysis, monitoring, analytics, and search-based applications.

### 34. **Modules or Libraries**:
   - **Extensions**: Elasticsearch can be extended with plugins for monitoring, security, and ingestion.

### 35. **Optimization and Tuning**:
   - Includes shard allocation, cache configuration, and query tuning.

### 36. **Backup and Recovery**:
   - Snapshots can be created for backup and restored for recovery.

### 37. **Security**:
   - Role-based access control, SSL/TLS encryption, and audit logging.

### 38. **Migration**:
   - Tools like reindexing API and snapshot/restore enable data migration between clusters.

### 39. **Recommended Cluster Setup**:
   - **Ideal Configuration**: Three master-eligible nodes, at least three data nodes, and coordinating nodes as needed.

### 40. **Basic Cluster Setup**:
   - **Minimum**: Three nodes for a small distributed setup, with roles depending on workload.

---
## References

1. https://www.elastic.co/guide/en/elasticsearch/reference/8.15/index.html
1. https://www.hellointerview.com/learn/system-design/deep-dives/elasticsearch
1. https://j.blaszyk.me/tech-blog/exploring-apache-lucene-index/
1. https://medium.com/swlh/bkd-trees-used-in-elasticsearch-40e8afd2a1a4
1. https://www.paradedb.com/blog/elasticsearch_vs_postgres
1. https://nsvarun14.medium.com/capacity-planning-for-elasticsearch-cde3c0693add
1. https://fdv.github.io/running-elasticsearch-fun-profit/004-cluster-design/004-cluster-design.html
1. https://medium.com/@sureshkumar.pawar/sizing-your-elk-elasticsearch-logstash-kibana-cluster-for-high-performance-398fe6e591d4
1. https://www.infoq.com/articles/similarity-scoring-elasticsearch/
1. https://www.elastic.co/blog/practical-bm25-part-2-the-bm25-algorithm-and-its-variables
1. [https://medium.com/@_niteshsaini_/how-elasticsearch-calculates-its-relevance-score-e762c6274004](https://medium.com/@_niteshsaini_/how-elasticsearch-calculates-its-relevance-score-e762c6274004)

---


