
### Structuring aggregations

结构化聚合

如 [ Aggregations guide](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations.html) 中所述，可以在聚合中定义子聚合。

聚合可能是 `Metrics` 聚合(一个跟踪和计算指标的聚合)或者 `Bucket` 聚合 (构建桶聚合)


例如，这里是一个3级聚合组成的聚合：

- Terms aggregation (bucket)
- Date Histogram aggregation (bucket)
- Average aggregation (metric)


```
SearchResponse sr = node.client().prepareSearch()
    .addAggregation(
        AggregationBuilders.terms("by_country").field("country")
        .subAggregation(AggregationBuilders.dateHistogram("by_year")
            .field("dateOfBirth")
            .dateHistogramInterval(DateHistogramInterval.YEAR)
            .subAggregation(AggregationBuilders.avg("avg_children").field("children"))
        )
    )
    .execute().actionGet();
```
