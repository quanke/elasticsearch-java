
### Span queries

- span_term查询

等同于 [term query](https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/java-term-level-queries.html#java-query-dsl-term-query) ，但与其他Span查询一起使用。

查看 [ Span Term Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-span-term-query.html)


```
QueryBuilder qb = spanTermQuery(
    "user",                                     //field
    "kimchy"                                    //value
);
```


- span_multi查询
 

包含term, range, prefix, wildcard, regexp 或者 fuzzy 查询。

查看[Span Multi Term Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-span-multi-term-query.html)

```
QueryBuilder qb = spanMultiTermQueryBuilder(
    prefixQuery("user", "ki")                   //可以是MultiTermQueryBuilder 的 扩展  比如：FuzzyQueryBuilder, PrefixQueryBuilder, RangeQueryBuilder, RegexpQueryBuilder ， WildcardQueryBuilder。
);
```

- span_first查询

接受另一个Span查询，其匹配必须出现在字段的前N个位置。

查看[Span First Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-span-first-query.html)

```
QueryBuilder qb = spanFirstQuery(
    spanTermQuery("user", "kimchy"),            //query
    3                                           //max end position
);
```

- span_near查询

接受多个Span查询，其匹配必须在彼此的指定距离内，并且可能顺序相同。

查看[Span Near Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-span-near-query.html)

```
QueryBuilder qb = spanNearQuery(
    spanTermQuery("field","value1"),            //span term queries
    12)                                         //slop factor: the maximum number of intervening unmatched positions
    .addClause(spanTermQuery("field","value2")) //span term queries
    .addClause(spanTermQuery("field","value3")) //span term queries
    .inOrder(false);                            //whether matches are required to be in-order
```

- span_or查询

组合多个Span查询 - 返回与任何指定查询匹配的文档。

查看[Span Or Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-span-or-query.html)


```
QueryBuilder qb = spanOrQuery(
    spanTermQuery("field","value1"))               
    .addClause(spanTermQuery("field","value2"))    
    .addClause(spanTermQuery("field","value3"));   //span term queries
```


- span_not查询

包装另一个Span查询，并排除与该查询匹配的所有文档。

查看[Span Not Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-span-not-query.html)


```
QueryBuilder qb = spanNotQuery(
    spanTermQuery("field","value1"),   //span query whose matches are filtered
    spanTermQuery("field","value2"));  //span query whose matches must not overlap those returned

```


- span_containing 查询

接受Span查询的列表，但仅返回与第二个Spans查询匹配的Span。

查看[ Span Containing Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-span-containing-query.html)


```
QueryBuilder qb = spanContainingQuery(
    spanNearQuery(spanTermQuery("field1","bar"), 5) //big part
          .addClause(spanTermQuery("field1","baz")) 
          .inOrder(true),
    spanTermQuery("field1","foo"));                 //little part
```

- span_within查询

只要其 span 位于由其他Span查询列表返回的范围内，就会返回单个Span查询的结果，

查看[Span Within Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-span-within-query.html)

```
QueryBuilder qb = spanWithinQuery(
    spanNearQuery(spanTermQuery("field1", "bar"), 5)  //big part
        .addClause(spanTermQuery("field1", "baz"))
        .inOrder(true),
    spanTermQuery("field1", "foo"));                  //little part
```

