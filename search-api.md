搜索API可以支持搜索查询，返回查询匹配的结果，它可以搜索一个index / type 或者多个index / type，可以使用 [query Java API](https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/java-query-dsl.html) 作为查询条件，下面是例子：


```
import org.elasticsearch.action.search.SearchResponse;
import org.elasticsearch.action.search.SearchType;
import org.elasticsearch.index.query.QueryBuilders.*;
```

```
SearchResponse response = client.prepareSearch("index1", "index2")
        .setTypes("type1", "type2")
        .setSearchType(SearchType.DFS_QUERY_THEN_FETCH)
        .setQuery(QueryBuilders.termQuery("multi", "test"))                 // Query 查询条件
        .setPostFilter(QueryBuilders.rangeQuery("age").from(12).to(18))     // Filter 过滤
        .setFrom(0).setSize(60).setExplain(true)
        .get();
```

所有的参数都是可选的，下面是最小调用：


```
// MatchAll on the whole cluster with all default options
SearchResponse response = client.prepareSearch().get();
```

> 尽管Java API默认提供`QUERY_AND_FETCH` 和 `DFS_QUERY_AND_FETCH` 两种 search types ，但是这种模式应该由系统选择，用户不要手动指定

更多请移步 [REST search](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search.html) 文档