
### Specialized queries

- more_like_this query(相似度查询)

这个查询能检索到与指定文本、文档或者文档集合相似的文档。

查看[More Like This Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-mlt-query.html)

```
String[] fields = {"name.first", "name.last"};                 //fields
String[] texts = {"text like this one"};                       //text
Item[] items = null;

QueryBuilder qb = moreLikeThisQuery(fields, texts, items)
    .minTermFreq(1)                                            //ignore threshold
    .maxQueryTerms(12);                                        //max num of Terms in generated queries
```

- script query

该查询允许脚本充当过滤器。 另请参阅 [function_score query](https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/java-compound-queries.html#java-query-dsl-function-score-query) 。


查看[Script Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-script-query.html)



```
QueryBuilder qb = scriptQuery(
    new Script("doc['num1'].value > 1") //inlined script
);
```

如果已经在每个数据节点上存储名为 `myscript.painless 的脚本，请执行以下操作：

```
doc['num1'].value > params.param1
```

然后使用：

```
QueryBuilder qb = scriptQuery(
    new Script(
        ScriptType.FILE,                       //脚本类型 ScriptType.FILE, ScriptType.INLINE， ScriptType.INDEXED
        "painless",                            //Scripting engine 脚本引擎
        "myscript",                            //Script name 脚本名
        Collections.singletonMap("param1", 5)) //Parameters as a Map of <String, Object>
);
```

- Percolate Query

查看[Percolate Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-percolate-query.html)



```
Settings settings = Settings.builder().put("cluster.name", "elasticsearch").build();
TransportClient client = new PreBuiltTransportClient(settings);
client.addTransportAddress(new InetSocketTransportAddress(new InetSocketAddress(InetAddresses.forString("127.0.0.1"), 9300)));

```

在可以使用`percolate`查询之前，应该添加`percolator`映射，并且应该对包含`percolator`查询的文档建立索引：

```
// create an index with a percolator field with the name 'query':
client.admin().indices().prepareCreate("myIndexName")
                        .addMapping("query", "query", "type=percolator")
                        .addMapping("docs", "content", "type=text")
                        .get();

//This is the query we're registering in the percolator
QueryBuilder qb = termQuery("content", "amazing");

//Index the query = register it in the percolator
client.prepareIndex("myIndexName", "query", "myDesignatedQueryName")
    .setSource(jsonBuilder()
        .startObject()
            .field("query", qb) // Register the query
        .endObject())
    .setRefreshPolicy(RefreshPolicy.IMMEDIATE) // Needed when the query shall be available immediately
    .get();
```

在上面的index中query名为 `myDesignatedQueryName`

为了检查文档注册查询,使用这个代码:


```
//Build a document to check against the percolator
XContentBuilder docBuilder = XContentFactory.jsonBuilder().startObject();
docBuilder.field("content", "This is amazing!");
docBuilder.endObject(); //End of the JSON root object

PercolateQueryBuilder percolateQuery = new PercolateQueryBuilder("query", "docs", docBuilder.bytes());

// Percolate, by executing the percolator query in the query dsl:
SearchResponse response = client().prepareSearch("myIndexName")
        .setQuery(percolateQuery))
        .get();
//Iterate over the results
for(SearchHit hit : response.getHits()) {
    // Percolator queries as hit
}
```
