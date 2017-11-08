
### Using Aggregations

下面的代码演示了如何在搜索中添加两个聚合：

> 聚合框架有助于根据搜索查询提供聚合数据。它是基于简单的构建块也称为整合，整合就是将复杂的数据摘要有序的放在一块。      
 
> 聚合可以被看做是从一组文件中获取分析信息的一系列工作的统称。聚合的实现过程就是定义这个文档集的过程（例如，在搜索请求的基础上，执行查询/过滤，才能得到高水平的聚合结果）。  

```
SearchResponse sr = client.prepareSearch()
    .setQuery(QueryBuilders.matchAllQuery())
    .addAggregation(
            AggregationBuilders.terms("agg1").field("field")
    )
    .addAggregation(
            AggregationBuilders.dateHistogram("agg2")
                    .field("birth")
                    .dateHistogramInterval(DateHistogramInterval.YEAR)
    )
    .get();

// Get your facet results
Terms agg1 = sr.getAggregations().get("agg1");
Histogram agg2 = sr.getAggregations().get("agg2");
```

详细文档请看 [Aggregations Java API](https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/java-aggs.html)
