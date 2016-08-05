# 简单查询
##### 1、查询一个指定索引下的所有的文档，其中的from参数（0-based）从哪个文档开始，size参数指明从from参数开始，要返回多少个文档。注意，如果不指定from的值，它默认就是0。 size不指定默认查询10条  
    {
      "query": { 
        "match_all": {} 
      },
      "from": 0,
      "size": 10
    }
    
##### 2、根据指定字段查询，返回_logId为abcd-efg-123的结果并排序
    {
        "query": {
            "match": { "_logId": "abcd-efg-123" } 
        },
        "sort": [
            { "@timestamp": { "order": "asc" }},
            { "sequence": { "order": "desc" }}
        ]
    }

##### 3、或查询，返回包含mill或者lane的message
    {
        "query": {
            "match": { "message": "mill lane" } 
        }
    }
    或者
    {
      "query": {
        "bool": {
          "should": [
            { "match": { "message": "mill" } },
            { "match": { "message": "lane" } }
          ]
        }
      }
    }
    
##### 4、与查询，返回包含mill与lane的message
    {
      "query": {
        "bool": {
          "must": [
            { "match": { "message": "mill" } },
            { "match": { "message": "lane" } }
          ]
        }
      }
    }
    
##### 5、根据多个字段排序
    {
        "query": { "match_all": {} },
        "sort" : [
            {"@timestamp": { "order": "asc" }},
            {"sequence": { "order": "asc" }}
        ]
    }
    
##### 6、以_logId分组并根据平均时间降序排序（使用时把注释去掉）
    {
        "size": 0,  //不显示查询的结果，只显示分组的信息
        "aggs": {
            "group_by_logId": {
                "terms": {
                    "field": "_logId", //不能用分词字段来分组查询,如果分词了要使用_logId.raw
                    "size": 15,           //分组结果的显示数量
                    "order" : { "avg_time" : "desc" }    //排序
                },
                "aggs": {
                    "avg_time": {
                        "avg": {  //求平均
                            "field" : "@timestamp"
                        }
                    }
                }
            }
        }
    }
    
# 更多相关操作请自行查询官方文档
[Elasticsearch Reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html)
