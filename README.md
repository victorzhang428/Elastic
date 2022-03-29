## Welcome to Elasticsearch Training Snippets

### Work With Index

#### 1. Create an index

```markdown
PUT /containers
```

#### 2. Get an index

```markdown
GET /containers
```

#### 3. Find an index

```markdown
HEAD /containers
```

#### 4. Close an index (index needs to be closed before updating its settings)

```markdown
POST /containers/_close
```

#### 5. Update settings of an index (Dynamic vs Static Setting)

```markdown
PUT /containers/_settings
{
      "index" : {
         "number_of_shards" : 3,
         "number_of_replicas" : 2
      }
}
```
#### 6. Open an index
```markdown
POST /containers/_open
```
#### 7. Delete an index
```markdown
DELETE /containers
```

### Work With Simple Document

#### 1. Create documents
```markdown
PUT /containers/container/id_1
{
    "cntr_no": "no_1",
    "customer_no": "BBB",
    "poa": "USMNT",
    "cntr_size": "20",
    "ocean_fgt": 1000,
    "last_user": "Tom",
    "last_datetime": "2022-03-20 22:58:12"
}
```

```markdown
PUT /containers/container/id_2
 {
    "cntr_no": "no_2",
    "customer_no": "CNS",
    "poa": "USMNT",
    "cntr_size": "40",
    "ocean_fgt": 2000,
    "last_user": "Jack",
    "last_datetime": "2022-03-23 22:33:29"
}
```
```markdown
PUT /containers/container/id_3
 {
    "cntr_no": "no_3",
    "customer_no": "FDS",
    "poa": "USMNT",
    "cntr_size": "45",
    "ocean_fgt": 3000,
    "last_user": "John",
    "last_datetime": "2022-03-26 22:05:17"
}
```
#### 2. Get document by ID

```markdown
GET /containers/container/id_1
```
#### 2. Get document by URI

```markdown
GET /containers/_search?q=cntr_no:no_1
```
#### 3. Get document by request body

```markdown
POST /containers/_search
{
    "fields": [
        "customer_no",
        "cntr_no",
        "last_datetime"
    ],
    "query": {
        "match": {
            "last_user": "John"
        }
    },
    "_source": false
}
```
#### 4. Get document by IN condition (where last_user in ("John, "Tom"))
 Why it doesn't work with uppcase explained [here](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-term-query.html#:~:text=To%20better%20search%20text%20fields,the%20exact%20term%20you%20provide.).


```markdown
GET /containers/_search
{
    "fields": [
        "customer_no",
        "cntr_no",
        "last_datetime"
    ],
    "query": {
        "terms": {
            "last_user": ["John", "Tom"]
        }
    },
    "_source": false
}
```
#### 5. Get document by pagination (select top 2 * from container order by ocean_fgt desc)

```markdown
POST /containers/_search
{
    "from": 0,
    "size": 2,
    "query": {
        "match_all": {}
    },
    "sort": [
        {
            "ocean_fgt": {
                "order": "desc"
            }
        }
    ]
}
```
#### 6. Get document by range (select * from container where ocean_fgt >=0 and ocean_fgt <= 2000 )

```markdown
POST /containers/_search
{
    "fields": [
        "customer_no",
        "cntr_no",
        "last_datetime",
        "ocean_fgt"
    ],
    "query": {
        "range": {
            "ocean_fgt": {
                "gte": 0,
                "lte": 2000
            }
        }
    },
    "_source": false
}
```
#### 7. Get document by date range (select * from container where last_datetime >='03/21/2022' and last_datetime >='03/25/2022' )

```markdown
POST /containers/_search
{
    "fields": [
        "customer_no",
        "cntr_no",
        "last_datetime",
        "ocean_fgt"
    ],
    "query": {
        "range": {
            "last_datetime": {
                "gte": "2022-03-21 00:00:00",
                "lte": "2022-03-25 00:00:00"
            }
        }
    },
    "_source": false
}
```
#### 8. Get MAX (select max_ocean_fgt=max(ocean_fgt) from container )

```markdown
POST /containers/_search
{
    "size": 0,
    "aggs": {
    "max_ocean_fgt": { "max": { "field": "ocean_fgt" } }
  }
}
```
#### 9. Update a document by ID

```markdown
POST /containers/container/id_1/_update
{
    "doc":{
        "last_user":"Victor"
    }
}
```
#### 10. Update a document by condition

```markdown
POST /containers/container/_update_by_query
{
    "script": {
        "source": "ctx._source.ocean_fgt=4000",
        "lang": "painless"
    },
    "query": {
        "match": {
            "last_user": "John"
        }
    }
}
```
### Work on Index with Mapping

#### 1. Create a new index with mapping

```markdown
PUT /containers2
{
    "settings": {
        "number_of_shards": 1
    },
    "mappings": {
          "properties": {
              "cntr_no": {
                  "type": "text",
                  "fields": {
                      "keyworld": {
                          "ignore_above": 30,
                          "type": "keyword"
                      }
                  }
              },
              "customer_no": {
                  "type": "text"
              },
              "poa": {
                  "type": "text"
              },
              "poa_loc": {
                  "type": "geo_point"
              },
              "cntr_size": {
                  "type": "integer"
              },
              "ocean_fgt": {
                  "type": "double"
              },
              "last_user": {
                  "type": "text",
                  "fields": {
                      "keyworld": {
                          "ignore_above": 256,
                          "type": "keyword"
                      }
                  }
              },
              "last_datetime": {
                  "type": "date",
                  "format": "yyyy-MM-dd HH:mm:ss||yyyy-MM-dd||epoch_millis"
              }
          }
     }    
}
```
#### 2. Add documents
```markdown
PUT /containers2/_doc/id_1
{
    "cntr_no": "no_1",
    "customer_no": "BBB",
    "poa": "USMNT",
    "poa_loc": {
      "lat": "32.3792",
      "lon": "86.3077"
    },
    "cntr_size": 20,
    "ocean_fgt": 1000,
    "last_user": "Tom",
    "last_datetime": "2022-03-23 22:58:12"
}

```
```markdown
PUT /containers2/_doc/id_2
PUT /containers/container/id_2
 {
   "cntr_no": "no_2",   
   "customer_no": "CNS",   
   "poa": "USLGB",   
   "poa_loc": {
      "lat": "33.7701",
      "lon": "118.1937"
    },
   "cntr_size": "40",   
   "ocean_fgt": 2000,   
   "last_user": "Jack",
   "last_datetime": "2022-03-23 22:33:29"
 }

```
```markdown
PUT /containers2/_doc/id_3
{
   "cntr_no": "no_3",   
   "customer_no": "FDS",   
    "poa": "USLAX",  
     "poa_loc": {
      "lat": "34.0522",
      "lon": "118.2437"
    },
    "cntr_size": "45",   
    "ocean_fgt": 3000,   
    "last_user": "John",
   "last_datetime": "2022-03-26 22:05:17"

}

```
### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/victorzhang428/Elastic/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
