
## Aggregations

聚合

Elasticsearch提供完整的Java API来使用聚合。 请参阅[聚合指南](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations.html)。


使用 `AggregationBuilders` 构建对象，增加到搜索请求中:

```
import org.elasticsearch.search.aggregations.AggregationBuilders;

```

```
SearchResponse sr = node.client().prepareSearch()
        .setQuery( /* your query */ )
        .addAggregation( /* add an aggregation */ )
        .execute().actionGet();
```
