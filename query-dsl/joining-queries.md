
### Joining queries

在像 ElasticSearch 这样的分布式系统中执行全 SQL 风格的连接查询代价昂贵，是不可行的。相应地，为了实现水平规模地扩展，ElasticSearch 提供了两种形式的 join。
 
- nested query (嵌套查询)

文档中可能包含嵌套类型的字段，这些字段用来索引一些数组对象，每个对象都可以作为一条独立的文档被查询出来(用嵌套查询)

查看[Nested Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-nested-query.html)

```
QueryBuilder qb = nestedQuery(
        "obj1",                       //nested 嵌套文档的路径
        boolQuery()                   // 查询 查询中引用的任何字段都必须使用完整路径（fully qualified）。
                .must(matchQuery("obj1.name", "blue"))
                .must(rangeQuery("obj1.count").gt(5)),
        ScoreMode.Avg                 // score 模型 ScoreMode.Max, ScoreMode.Min, ScoreMode.Total, ScoreMode.Avg or ScoreMode.None
    );
```

- has_child (有子查询)  and has_parent (有父查询) queries

一类父子关系可以存在单个的索引的两个类型的文档之间。has_child 查询将返回其子文档能满足特定的查询的父文档，而 has_parent 则返回其父文档能满足特定查询的子文档
 
 
#### Has Child Query

查看[Has Child Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-has-child-query.html)

使用 `has_child` 查询时，必须使用`PreBuiltTransportClient`而不是常规 `Client`，这个点很重要：


```
Settings settings = Settings.builder().put("cluster.name", "elasticsearch").build();
TransportClient client = new PreBuiltTransportClient(settings);
client.addTransportAddress(new InetSocketTransportAddress(new InetSocketAddress(InetAddresses.forString("127.0.0.1"), 9300)));

```

否则，`parent-join` 模块不会被加载，并且不能从transport client 使用`has_child`查询。


```
QueryBuilder qb = JoinQueryBuilders.hasChildQuery(
    "blog_tag",                     //要查询的子类型
    termQuery("tag","something"),   //查询
    ScoreMode.Avg                   //score 模型 ScoreMode.Avg, ScoreMode.Max, ScoreMode.Min, ScoreMode.None or ScoreMode.Total
);
```


#### Has Parent Query
 
查看[Has Parent](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-has-parent-query.html)

使用`has_parent`查询时，必须使用`PreBuiltTransportClient`而不是常规 `Client`，这个点很重要：


```
Settings settings = Settings.builder().put("cluster.name", "elasticsearch").build();
TransportClient client = new PreBuiltTransportClient(settings);
client.addTransportAddress(new InetSocketTransportAddress(new InetSocketAddress(InetAddresses.forString("127.0.0.1"), 9300)));

```

否则，`parent-join` 模块不会被加载，并且不能从transport client 使用`has_child`查询。


```
QueryBuilder qb = JoinQueryBuilders.hasParentQuery(
    "blog",                         //要查询的子类型
    termQuery("tag","something"),   //查询
    false                           //是否从父hit的score 传给子 hit
);
```

参考 term 查询中的[terms-lookup mechanism](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-terms-query.html#query-dsl-terms-lookup) ，它允许你在另一个文档的值中创建一个term 查询。
