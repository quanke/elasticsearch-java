
### MultiSearch API

multi search API 允许在同一API中执行多个搜索请求。它的端点（endpoint）是 _msearch 。

首先请看[MultiSearch API Query ](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-multi-search.html) 文档

```
SearchRequestBuilder srb1 = client
    .prepareSearch().setQuery(QueryBuilders.queryStringQuery("elasticsearch")).setSize(1);
SearchRequestBuilder srb2 = client
    .prepareSearch().setQuery(QueryBuilders.matchQuery("name", "kimchy")).setSize(1);

MultiSearchResponse sr = client.prepareMultiSearch()
        .add(srb1)
        .add(srb2)
        .get();

// You will get all individual responses from MultiSearchResponse#getResponses()
long nbHits = 0;
for (MultiSearchResponse.Item item : sr.getResponses()) {
    SearchResponse response = item.getResponse();
    nbHits += response.getHits().getTotalHits();
}
```

#### 实例


```

```