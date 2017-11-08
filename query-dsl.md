
## Query DSL

> Elasticsearch 提供了一个基于 JSON 的完整的查询 DSL 来定义查询。

Elasticsearch以类似于REST [Query DSL](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl.html) 的方式提供完整的Java查询dsl。 查询构建器的工厂是 `QueryBuilders`。 一旦的查询准备就绪，就可以使用[Search API](https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/java-search.html) 。

要使用QueryBuilder，只需将它们导入到类中：

```
import static org.elasticsearch.index.query.QueryBuilders.*;

```

> 注意，可以使用`QueryBuilder`对象上的`toString（）`方法打印。

`QueryBuilder`可以用于接受任何查询API，如`count`和`search`。

### Match All Query

> 最简单的查询，它匹配所有文档

查看 [Match All Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-match-all-query.html) 


```
QueryBuilder qb = matchAllQuery();
```