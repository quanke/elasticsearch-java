
### Using scrolls in Java

首先需要阅读 [scroll documentation](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-request-scroll.html)

> 一般搜索请求都是返回一"页"数据，无论数据量多大都一起返回给用户，Scroll API可以允许我们检索大量数据（甚至全部数据）。Scroll API允许我们做一个初始阶段搜索并且持续批量从Elasticsearch里拉取结果直到没有结果剩下。这有点像传统数据库里的cursors（游标）。
Scroll API的创建并不是为了实时的用户响应，而是为了处理大量的数据（Scrolling is not intended for real time user requests, but rather for processing large amounts of data）。从 scroll 请求返回的结果只是反映了 search 发生那一时刻的索引状态，就像一个快照(The results that are returned from a scroll request reflect the state of the index at the time that the initial search request was made, like a snapshot in time)。后续的对文档的改动（索引、更新或者删除）都只会影响后面的搜索请求。


```
import static org.elasticsearch.index.query.QueryBuilders.*;
```

```
QueryBuilder qb = termQuery("multi", "test");

SearchResponse scrollResp = client.prepareSearch(test)
        .addSort(FieldSortBuilder.DOC_FIELD_NAME, SortOrder.ASC)
        .setScroll(new TimeValue(60000)) //为了使用 scroll，初始搜索请求应该在查询中指定 scroll 参数，告诉 Elasticsearch 需要保持搜索的上下文环境多长时间（滚动时间）
        .setQuery(qb)
        .setSize(100).get(); //max of 100 hits will be returned for each scroll
//Scroll until no hits are returned
do {
    for (SearchHit hit : scrollResp.getHits().getHits()) {
        //Handle the hit...
    }

    scrollResp = client.prepareSearchScroll(scrollResp.getScrollId()).setScroll(new TimeValue(60000)).execute().actionGet();
} while(scrollResp.getHits().getHits().length != 0); // Zero hits mark the end of the scroll and the while loop.
```

> 如果超过滚动时间，继续使用该滚动ID搜索数据，则会报错：


```
Caused by: SearchContextMissingException[No search context found for id [2861]]
	at org.elasticsearch.search.SearchService.findContext(SearchService.java:613)
	at org.elasticsearch.search.SearchService.executeQueryPhase(SearchService.java:403)
	at org.elasticsearch.search.action.SearchServiceTransportAction$SearchQueryScrollTransportHandler.messageReceived(SearchServiceTransportAction.java:384)
	at org.elasticsearch.search.action.SearchServiceTransportAction$SearchQueryScrollTransportHandler.messageReceived(SearchServiceTransportAction.java:381)
	at org.elasticsearch.transport.TransportRequestHandler.messageReceived(TransportRequestHandler.java:33)
	at org.elasticsearch.transport.RequestHandlerRegistry.processMessageReceived(RequestHandlerRegistry.java:75)
	at org.elasticsearch.transport.TransportService$4.doRun(TransportService.java:376)
	at org.elasticsearch.common.util.concurrent.AbstractRunnable.run(AbstractRunnable.java:37)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
	at java.lang.Thread.run(Thread.java:745)
```
> 虽然当滚动有效时间已过，搜索上下文(Search Context)会自动被清除，但是一值保持滚动代价也是很大的，所以当我们不在使用滚动时要尽快使用Clear-Scroll API进行清除。

## 清除Scroll

```
  /**
     * 清除滚动ID
     * @param client
     * @param scrollIdList
     * @return
     */
    public static boolean clearScroll(Client client, List<String> scrollIdList){
        ClearScrollRequestBuilder clearScrollRequestBuilder = client.prepareClearScroll();
        clearScrollRequestBuilder.setScrollIds(scrollIdList);
        ClearScrollResponse response = clearScrollRequestBuilder.get();
        return response.isSucceeded();
    }
    /**
     * 清除滚动ID
     * @param client
     * @param scrollId
     * @return
     */
    public static boolean clearScroll(Client client, String scrollId){
        ClearScrollRequestBuilder clearScrollRequestBuilder = client.prepareClearScroll();
        clearScrollRequestBuilder.addScrollId(scrollId);
        ClearScrollResponse response = clearScrollRequestBuilder.get();
        return response.isSucceeded();
    }
```

#### 实例

```

public class ScrollsAPI extends ElasticsearchClientBase {

    private String scrollId;

    @Test
    public void testScrolls() throws Exception {

        SearchResponse scrollResp = client.prepareSearch("twitter")
                .addSort(FieldSortBuilder.DOC_FIELD_NAME, SortOrder.ASC)
                .setScroll(new TimeValue(60000)) //为了使用 scroll，初始搜索请求应该在查询中指定 scroll 参数，告诉 Elasticsearch 需要保持搜索的上下文环境多长时间（滚动时间）
                .setQuery(QueryBuilders.termQuery("user", "kimchy"))                 // Query 查询条件
                .setSize(5).get(); //max of 100 hits will be returned for each scroll
        //Scroll until no hits are returned

        scrollId = scrollResp.getScrollId();
        do {
            for (SearchHit hit : scrollResp.getHits().getHits()) {
                //Handle the hit...

                System.out.println("" + hit.getSource().toString());
            }

            scrollResp = client.prepareSearchScroll(scrollId).setScroll(new TimeValue(60000)).execute().actionGet();
        }
        while (scrollResp.getHits().getHits().length != 0); // Zero hits mark the end of the scroll and the while loop.
    }

    @Override
    public void tearDown() throws Exception {
        ClearScrollRequestBuilder clearScrollRequestBuilder = client.prepareClearScroll();
        clearScrollRequestBuilder.addScrollId(scrollId);
        ClearScrollResponse response = clearScrollRequestBuilder.get();

        if (response.isSucceeded()) {
            System.out.println("成功清除");
        }

        super.tearDown();
    }
}

```

- [ScrollsAPI.java](https://gitee.com/quanke/elasticsearch-java-study/blob/master/src/test/java/name/quanke/es/study/search/ScrollsAPI.java)

- [本手册完整实例](https://gitee.com/quanke/elasticsearch-java-study)