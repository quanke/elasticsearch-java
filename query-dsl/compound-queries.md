
### Compound queries

复合查询用来包装其他复合或者叶子查询，一方面可综合其结果和分数，从而改变它的行为，另一方面可从查询切换到过滤器上下文。此类查询包含：

- constant_score 查询

这是一个包装其他查询的查询，并且在过滤器上下文中执行。与此查询匹配的所有文件都需要返回相同的“常量” _score 。

查看[Constant Score Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-constant-score-query.html)


```
QueryBuilder qb = constantScoreQuery(
        termQuery("name","kimchy")      //查询语句
    )
    .boost(2.0f);   //分数
```


- bool 查询

组合多个叶子并发查询或复合查询条件的默认查询类型，例如must, should, must_not, 以及 filter 条件。 在 must 和 should 子句他们的分数相结合-匹配条件越多，预期越好-而 must_not 和 filter 子句在过滤器上下文中执行。

查看[Bool Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-bool-query.html)

```
QueryBuilder qb = boolQuery()
    .must(termQuery("content", "test1"))    //must query
    .must(termQuery("content", "test4"))    
    .mustNot(termQuery("content", "test2")) //must not query
    .should(termQuery("content", "test3"))  // should query
    .filter(termQuery("content", "test5// 与一般查询作用一样，只不过不参与评分
```

- dis_max 查询

支持多并发查询的查询，并可返回与任意查询条件子句匹配的任何文档类型。与 bool 查询可以将所有匹配查询的分数相结合使用的方式不同的是，dis_max 查询只使用最佳匹配查询条件的分数。

查看[Dis Max Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-dis-max-query.html)

```
QueryBuilder qb = disMaxQuery()
    .add(termQuery("name", "kimchy"))        
    .add(termQuery("name", "elasticsearch")) 
    .boost(1.2f)                   //boost factor         
    .tieBreaker(0.7f);   //tie breaker
```

- function_score 查询

使用函数修改主查询返回的分数，以考虑诸如流行度，新近度，距离或使用脚本实现的自定义算法等因素。

查看[Function Score Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-function-score-query.html)


```
import static org.elasticsearch.index.query.functionscore.ScoreFunctionBuilders.*;

```


```
FilterFunctionBuilder[] functions = {
        new FunctionScoreQueryBuilder.FilterFunctionBuilder(
                matchQuery("name", "kimchy"),                //根据查询添加第一个function
                randomFunction("ABCDEF")),                    //根据给定的种子随机分数
        new FunctionScoreQueryBuilder.FilterFunctionBuilder(
                exponentialDecayFunction("age", 0L, 1L))     //根据年龄字段添加另一个function
 
};
QueryBuilder qb = QueryBuilders.functionScoreQuery(functions);
```


- boosting 查询

返回匹配 positive 查询的文档，并且当减少文档的分数时其结果也匹配 negative 查询。


查看[Boosting Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-boosting-query.html)


```
QueryBuilder qb = boostingQuery(
        termQuery("name","kimchy"),    
        termQuery("name","dadoonet"))  
    .negativeBoost(0.2f);   
```

- indices 查询

对指定的索引执行一个查询，对其他索引执行另一个查询。

查看[Indices Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-indices-query.html)


> 在5.0.0中已弃用。用搜索 _index 字段来代替

```
// Using another query when no match for the main one
QueryBuilder qb = indicesQuery(
        termQuery("tag", "wow"),             
        "index1", "index2"                   
    ).noMatchQuery(termQuery("tag", "kow")); 

```


```
// Using all (match all) or none (match no documents)
QueryBuilder qb = indicesQuery(
        termQuery("tag", "wow"),            
        "index1", "index2"                  
    ).noMatchQuery("all");                  
```

