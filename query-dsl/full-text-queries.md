
### Full text queries 全文搜索

高级别的全文搜索通常用于在全文字段（例如：一封邮件的正文）上进行全文搜索。它们了解如何分析查询的字段，并在执行之前将每个字段的分析器（或搜索分析器）应用于查询字符串。

这样的查询有以下这些：

- 匹配查询（match query）

用于执行全文查询的标准查询，包括模糊匹配和词组或邻近程度的查询

查看[ Match Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-match-query.html) 


```
QueryBuilder qb = matchQuery(
    "name",                  //field 字段
    "kimchy elasticsearch"   // text 
);
```

- 多字段查询（multi_match query）

可以用来对多个字段的版本进行匹配查询

查看 [Multi Match Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-multi-match-query.html)


```
QueryBuilder qb = multiMatchQuery(
    "kimchy elasticsearch", //text
    "user", "message"       //fields 多个字段
);
```


- 常用术语查询（common_terms query）

可以对一些比较专业的偏门词语进行的更加专业的查询

查看[Common Terms Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-common-terms-query.html)


```
QueryBuilder qb = commonTermsQuery("name",    //field 字段
                                   "kimchy");  // value
```


- 查询语句查询（query_string query）

与lucene查询语句的语法结合的更加紧密的一种查询，允许你在一个查询语句中使用多个 特殊条件关键字（如：AND|OR|NOT ）对多个字段进行查询，当然这种查询仅限专家用户去使用。

查看[Query String Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-query-string-query.html)


```
QueryBuilder qb = queryStringQuery("+kimchy -elasticsearch");   //text   

```


- 简单查询语句（simple_query_string）

是一种适合直接暴露给用户的简单的且具有非常完善的查询语法的查询语句

查看[Simple Query String Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-simple-query-string-query.html)


```
QueryBuilder qb = simpleQueryStringQuery("+kimchy -elasticsearch");    //text
```
