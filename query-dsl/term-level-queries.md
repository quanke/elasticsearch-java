
### Term level queries 术语查询

虽然全文查询将在执行之前分析查询字符串，但是项级别查询对存储在反向索引中的确切项进行操作。
 
通常用于结构化数据，如数字、日期和枚举，而不是全文字段。或者，在分析过程之前，它允许你绘制低级查询。
 
这样的查询有以下这些：

- Term Query（项查询）

查询包含在指定字段中指定的确切值的文档。

查看[Term Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-term-query.html)

```

QueryBuilder qb = termQuery(
    "name",    //field
    "kimchy"   //text
);
```

- Terms Query（多项查询）

查询包含任意一个在指定字段中指定的多个确切值的文档。

查看[Terms Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-terms-query.html)

```
QueryBuilder qb = termsQuery("tags",    //field
    "blue", "pill");                    //values
```

- Range Query（范围查询）

查询指定字段包含指定范围内的值（日期，数字或字符串）的文档。

查看[Range Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-range-query.html)

方法：

1. gte() :范围查询将匹配字段值大于或等于此参数值的文档。
2. gt() :范围查询将匹配字段值大于此参数值的文档。
3. lte() :范围查询将匹配字段值小于或等于此参数值的文档。
4. lt() :范围查询将匹配字段值小于此参数值的文档。
5. from() 开始值 to() 结束值 这两个函数与includeLower()和includeUpper()函数配套使用。
6. includeLower(true) 表示 from() 查询将匹配字段值大于或等于此参数值的文档。
7. includeLower(false) 表示 from() 查询将匹配字段值大于此参数值的文档。
8. includeUpper(true) 表示 to() 查询将匹配字段值小于或等于此参数值的文档。
9. includeUpper(false) 表示 to() 查询将匹配字段值小于此参数值的文档。

```
QueryBuilder qb = rangeQuery("price")   //field
    .from(5)                            //开始值，与includeLower()和includeUpper()函数配套使用
    .to(10)                             //结束值，与includeLower()和includeUpper()函数配套使用
    .includeLower(true)                 // true: 表示 from() 查询将匹配字段值大于或等于此参数值的文档;   false:表示 from() 查询将匹配字段值大于此参数值的文档。
    .includeUpper(false);               //true:表示 to() 查询将匹配字段值小于或等于此参数值的文档;   false:表示 to() 查询将匹配字段值小于此参数值的文档。
```

#### 实例


```
// Query
RangeQueryBuilder rangeQueryBuilder = QueryBuilders.rangeQuery("age");
rangeQueryBuilder.from(19);
rangeQueryBuilder.to(21);
rangeQueryBuilder.includeLower(true);
rangeQueryBuilder.includeUpper(true);
//RangeQueryBuilder rangeQueryBuilder = QueryBuilders.rangeQuery("age").gte(19).lte(21);
// Search
SearchRequestBuilder searchRequestBuilder = client.prepareSearch(index);
searchRequestBuilder.setTypes(type);
searchRequestBuilder.setQuery(rangeQueryBuilder);
// 执行
SearchResponse searchResponse = searchRequestBuilder.execute().actionGet();
```

上面代码中的查询语句与下面的是等价的：


```
QueryBuilder queryBuilder = QueryBuilders.rangeQuery("age").gte(19).lte(21);

```

- Exists Query（存在查询）

查询指定的字段包含任何非空值的文档,如果指定字段上至少存在一个no-null的值就会返回该文档。

查看[Exists Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-exists-query.html)


```
QueryBuilder qb = existsQuery("name");       

```
#### 实例


```
// Query
ExistsQueryBuilder existsQueryBuilder = QueryBuilders.existsQuery("name");
// Search
SearchRequestBuilder searchRequestBuilder = client.prepareSearch(index);
searchRequestBuilder.setTypes(type);
searchRequestBuilder.setQuery(existsQueryBuilder);
// 执行
SearchResponse searchResponse = searchRequestBuilder.get();
```

举例说明，下面的几个文档都会得到上面代码的匹配：


```
{ "name": "yoona" }
{ "name": "" }
{ "name": "-" }
{ "name": ["yoona"] }
{ "name": ["yoona", null ] }
```

第一个是字符串，是一个非null的值。

第二个是空字符串，也是非null。

第三个使用标准分析器的情况下尽管不会返回词条，但是原始字段值是非null的（Even though the standard analyzer would emit zero tokens, the original field is non-null）。

第五个中至少有一个是非null值。

下面几个文档不会得到上面代码的匹配：


```
{ "name": null }
{ "name": [] }
{ "name": [null] }
{ "user":  "bar" }
```

第一个是null值。

第二个没有值。

第三个只有null值，至少需要一个非null值。

第四个与指定字段不匹配。
- Prefix Query（前缀查询）

查找指定字段包含以指定的精确前缀开头的值的文档。

查看[Prefix Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-prefix-query.html)


```
QueryBuilder qb = prefixQuery(
    "brand",    //field
    "heine"     //prefix
);
```


- Wildcard Query（通配符查询）

查询指定字段包含与指定模式匹配的值的文档，其中该模式支持单字符通配符（？）和多字符通配符（*）,和前缀查询一样，通配符查询指定字段是未分析的（not analyzed）

可以使用星号代替0个或多个字符，使用问号代替一个字符。星号表示匹配的数量不受限制，而后者的匹配字符数则受到限制。这个技巧主要用于英文搜索中，如输入““computer*”，就可以找到“computer、computers、computerised、computerized”等单词，而输入“comp?ter”，则只能找到“computer、compater、competer”等单词。注意的是通配符查询不太注重性能，在可能时尽量避免，特别是要避免前缀通配符（以通配符开始的词条）。


```
QueryBuilder qb = wildcardQuery("user", "k?mc*");

```


#### 实例

```
// Query
WildcardQueryBuilder wildcardQueryBuilder = QueryBuilders.wildcardQuery("country", "西*牙");
// Search
SearchRequestBuilder searchRequestBuilder = client.prepareSearch(index);
searchRequestBuilder.setTypes(type);
searchRequestBuilder.setQuery(wildcardQueryBuilder);
// 执行
SearchResponse searchResponse = searchRequestBuilder.get();
```


查看[Wildcard Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-wildcard-query.html)

- Regexp Query（正则表达式查询）

查询指定的字段包含与指定的正则表达式匹配的值的文档。

和前缀查询一样，正则表达式查询指定字段是未分析的（not analyzed）。正则表达式查询的性能取决于所选的正则表达式。如果我们的正则表达式匹配许多词条，查询将很慢。一般规则是，正则表达式匹配的词条数越高，查询越慢。


查看[Regexp Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-regexp-query.html)



```
QueryBuilder qb = regexpQuery(
    "name.first",        //field
    "s.*y");             //regexp
```

#### 实例


```
// Query
RegexpQueryBuilder regexpQueryBuilder = QueryBuilders.regexpQuery("country", "(西班|葡萄)牙");

// Search
SearchRequestBuilder searchRequestBuilder = client.prepareSearch(index);
searchRequestBuilder.setTypes(type);
searchRequestBuilder.setQuery(regexpQueryBuilder);
// 执行
SearchResponse searchResponse = searchRequestBuilder.get();
```


- Fuzzy Query（模糊查询）

查询指定字段包含与指定术语模糊相似的术语的文档。模糊性测量为1或2的 Levenshtein。

如果指定的字段是string类型，模糊查询是基于编辑距离算法来匹配文档。编辑距离的计算基于我们提供的查询词条和被搜索文档。如果指定的字段是数值类型或者日期类型，模糊查询基于在字段值上进行加减操作来匹配文档（The fuzzy query uses similarity based on Levenshtein edit distance for string fields, and a +/-margin on numeric and date fields）。此查询很占用CPU资源，但当需要模糊匹配时它很有用，例如，当用户拼写错误时。另外我们可以在搜索词的尾部加上字符 “~” 来进行模糊查询。


查看[Fuzzy Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-fuzzy-query.html)


```
QueryBuilder qb = fuzzyQuery(
    "name",     //field
    "kimzhy"    //text
);
```


- Type Query（类型查询）

查询指定类型的文档。

查看[Type Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-type-query.html)


```
QueryBuilder qb = typeQuery("my_type"); //type

```




- Ids Query（ID查询）

查询具有指定类型和 ID 的文档。

查看[Ids Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-ids-query.html)


```
QueryBuilder qb = idsQuery("my_type", "type2")
    .addIds("1", "4", "100");

QueryBuilder qb = idsQuery()  // type 是可选择的，可以不写
    .addIds("1", "4", "100");
```
