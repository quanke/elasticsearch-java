
### Bucket aggregations 桶分聚合

> Bucket aggregations  不像 metrics aggregations 那样计算指标，恰恰相反，它创建文档的buckets，每个buckets与标准（取决于聚合类型）相关联，它决定了当前上下文中的文档是否会“falls”到它。换句话说，bucket可以有效地定义文档集合。除了buckets本身，bucket集合还计算并返回“落入”每个bucket的文档数量。

> 与度量聚合相比，Bucket聚合可以保存子聚合，这些子聚合将针对由其“父”bucket聚合创建的bucket进行聚合。

> 有不同的bucket聚合器，每个具有不同的“bucketing”策略,一些定义一个单独的bucket，一些定义多个bucket的固定数量，另一些定义在聚合过程中动态创建bucket


####  Global Aggregation 全局聚合

> 定义搜索执行上下文中的所有文档的单个bucket，这个上下文由索引和您正在搜索的文档类型定义，但不受搜索查询本身的影响。


> 全局聚合器只能作为顶层聚合器放置，因为将全局聚合器嵌入到另一个分组聚合器中是没有意义的。


下面是如何使用 `Java API` 使用[全局聚合](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-bucket-global-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
AggregationBuilders
    .global("agg")
    .subAggregation(AggregationBuilders.terms("genders").field("gender"));
```

##### 使用聚合请求


```
import org.elasticsearch.search.aggregations.bucket.global.Global;

```

```
// sr is here your SearchResponse object
Global agg = sr.getAggregations().get("agg");
agg.getDocCount(); // Doc count
```

####  Filter Aggregation 过滤聚合

> 过滤聚合——基于一个条件，来对当前的文档进行过滤的聚合。



下面是如何使用 `Java API` 使用[过滤聚合](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-bucket-filter-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
AggregationBuilders
    .filter("agg", QueryBuilders.termQuery("gender", "male"));
```

##### 使用聚合请求


```
import org.elasticsearch.search.aggregations.bucket.filter.Filter;

```

```
// sr is here your SearchResponse object
Filter agg = sr.getAggregations().get("agg");
agg.getDocCount(); // Doc count
```

####  Filters Aggregation 多过滤聚合

> 多过滤聚合——基于多个过滤条件，来对当前文档进行【过滤】的聚合，每个过滤都包含所有满足它的文档（多个bucket中可能重复）。



下面是如何使用 `Java API` 使用[多过滤聚合](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-bucket-filters-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
AggregationBuilder aggregation =
    AggregationBuilders
        .filters("agg",
            new FiltersAggregator.KeyedFilter("men", QueryBuilders.termQuery("gender", "male")),
            new FiltersAggregator.KeyedFilter("women", QueryBuilders.termQuery("gender", "female")));
```

##### 使用聚合请求


```
import org.elasticsearch.search.aggregations.bucket.filters.Filters;

```

```
// sr is here your SearchResponse object
Filters agg = sr.getAggregations().get("agg");

// For each entry
for (Filters.Bucket entry : agg.getBuckets()) {
    String key = entry.getKeyAsString();            // bucket key
    long docCount = entry.getDocCount();            // Doc count
    logger.info("key [{}], doc_count [{}]", key, docCount);
}
```

大概输出


```
key [men], doc_count [4982]
key [women], doc_count [5018]
```

#### Missing Aggregation 基于字段数据的单桶聚合

> 基于字段数据的单桶聚合，创建当前文档集上下文中缺少字段值的所有文档的bucket（桶）（有效地，丢失了一个字段或配置了NULL值集），此聚合器通常与其他字段数据桶聚合器（例如范围）结合使用，以返回由于缺少字段数据值而无法放置在任何其他存储区中的所有文档的信息。



下面是如何使用 `Java API` 使用[基于字段数据的单桶聚合](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-bucket-missing-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
AggregationBuilders.missing("agg").field("gender");

```

##### 使用聚合请求


```
import org.elasticsearch.search.aggregations.bucket.missing.Missing;

```

```
// sr is here your SearchResponse object
Missing agg = sr.getAggregations().get("agg");
agg.getDocCount(); // Doc count
```

#### Nested Aggregation 嵌套类型聚合

> 基于嵌套（nested）数据类型，把该【嵌套类型的信息】聚合到单个桶里，然后就可以对嵌套类型做进一步的聚合操作。



下面是如何使用 `Java API` 使用[嵌套类型聚合](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-bucket-nested-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
AggregationBuilders
    .nested("agg", "resellers");

```

##### 使用聚合请求


```
import org.elasticsearch.search.aggregations.bucket.nested.Nested;

```

```
// sr is here your SearchResponse object
Nested agg = sr.getAggregations().get("agg");
agg.getDocCount(); // Doc count
```

#### Reverse nested Aggregation

> 一个特殊的单桶聚合，可以从嵌套文档中聚合父文档。实际上，这种聚合可以从嵌套的块结构中跳出来，并链接到其他嵌套的结构或根文档.这允许嵌套不是嵌套对象的一部分的其他聚合在嵌套聚合中。
reverse_nested 聚合必须定义在nested之中



下面是如何使用 `Java API` 使用[Reverse nested Aggregation](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-bucket-reverse-nested-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
AggregationBuilder aggregation =
    AggregationBuilders
        .nested("agg", "resellers")
        .subAggregation(
                AggregationBuilders
                        .terms("name").field("resellers.name")
                        .subAggregation(
                                AggregationBuilders
                                        .reverseNested("reseller_to_product")
                        )
        );
```

##### 使用聚合请求


```
import org.elasticsearch.search.aggregations.bucket.nested.Nested;
import org.elasticsearch.search.aggregations.bucket.nested.ReverseNested;
import org.elasticsearch.search.aggregations.bucket.terms.Terms;

```

```
// sr is here your SearchResponse object
Nested agg = sr.getAggregations().get("agg");
Terms name = agg.getAggregations().get("name");
for (Terms.Bucket bucket : name.getBuckets()) {
    ReverseNested resellerToProduct = bucket.getAggregations().get("reseller_to_product");
    resellerToProduct.getDocCount(); // Doc count
}
```


#### Children Aggregation

> 一种特殊的单桶聚合，可以将父文档类型上的桶聚合到子文档上。




下面是如何使用 `Java API` 使用[Children Aggregation](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-bucket-children-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
AggregationBuilder aggregation =
    AggregationBuilders
        .children("agg", "reseller"); // agg 是聚合名，reseller 是子类型
```

##### 使用聚合请求


```
import org.elasticsearch.join.aggregations.Children;


```

```
// sr is here your SearchResponse object
Children agg = sr.getAggregations().get("agg");
agg.getDocCount(); // Doc count
```

#### Terms Aggregation 词元聚合

> 基于某个field，该 field 内的每一个【唯一词元】为一个桶，并计算每个桶内文档个数。默认返回顺序是按照文档个数多少排序。当不返回所有 buckets 的情况，文档个数可能不准确。




下面是如何使用 `Java API` 使用[Terms Aggregation](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-bucket-terms-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
AggregationBuilders
    .terms("genders")
    .field("gender");
```

##### 使用聚合请求


```
import org.elasticsearch.search.aggregations.bucket.terms.Terms;

```

```
// sr is here your SearchResponse object
Terms genders = sr.getAggregations().get("genders");

// For each entry
for (Terms.Bucket entry : genders.getBuckets()) {
    entry.getKey();      // Term
    entry.getDocCount(); // Doc count
}
```

#### Order 排序

通过 `doc_count` 按升序排列：


```
AggregationBuilders
    .terms("genders")
    .field("gender")
    .order(Terms.Order.count(true))
```

按字词顺序，升序排列：

```
AggregationBuilders
    .terms("genders")
    .field("gender")
    .order(Terms.Order.term(true))
```

按metrics 子聚合排列（标示为聚合名）


```
AggregationBuilders
    .terms("genders")
    .field("gender")
    .order(Terms.Order.aggregation("avg_height", false))
    .subAggregation(
        AggregationBuilders.avg("avg_height").field("height")
    )
```



#### Significant Terms Aggregation

> 返回集合中感兴趣的或者不常见的词条的聚合



下面是如何使用 `Java API` 使用[Significant Terms Aggregation](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-bucket-significantterms-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
AggregationBuilder aggregation =
        AggregationBuilders
                .significantTerms("significant_countries")
                .field("address.country");

// Let say you search for men only
SearchResponse sr = client.prepareSearch()
        .setQuery(QueryBuilders.termQuery("gender", "male"))
        .addAggregation(aggregation)
        .get();
```

##### 使用聚合请求


```
import org.elasticsearch.search.aggregations.bucket.significant.SignificantTerms;

```

```
// sr is here your SearchResponse object
SignificantTerms agg = sr.getAggregations().get("significant_countries");

// For each entry
for (SignificantTerms.Bucket entry : agg.getBuckets()) {
    entry.getKey();      // Term
    entry.getDocCount(); // Doc count
}
```


#### Range Aggregation 范围聚合

> 基于某个值（可以是 field 或 script），以【字段范围】来桶分聚合。范围聚合包括 from 值，不包括 to 值（区间前闭后开）。


下面是如何使用 `Java API` 使用[Range Aggregation](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-bucket-range-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
AggregationBuilder aggregation =
        AggregationBuilders
                .range("agg")
                .field("height")
                .addUnboundedTo(1.0f)               // from -infinity to 1.0 (excluded)
                .addRange(1.0f, 1.5f)               // from 1.0 to 1.5 (excluded)
                .addUnboundedFrom(1.5f);            // from 1.5 to +infinity
```

##### 使用聚合请求


```
import org.elasticsearch.search.aggregations.bucket.range.Range;

```

```
// sr is here your SearchResponse object
Range agg = sr.getAggregations().get("agg");

// For each entry
for (Range.Bucket entry : agg.getBuckets()) {
    String key = entry.getKeyAsString();             // Range as key
    Number from = (Number) entry.getFrom();          // Bucket from
    Number to = (Number) entry.getTo();              // Bucket to
    long docCount = entry.getDocCount();    // Doc count

    logger.info("key [{}], from [{}], to [{}], doc_count [{}]", key, from, to, docCount);
}
```

输出：


```
key [*-1.0], from [-Infinity], to [1.0], doc_count [9]
key [1.0-1.5], from [1.0], to [1.5], doc_count [21]
key [1.5-*], from [1.5], to [Infinity], doc_count [20]
```

#### Date Range Aggregation 日期范围聚合

> 日期范围聚合——基于日期类型的值，以【日期范围】来桶分聚合。

> 日期范围可以用各种 Date Math 表达式。

> 同样的，包括 from 的值，不包括 to 的值。




下面是如何使用 `Java API` 使用[Date Range Aggregation](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-bucket-daterange-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
AggregationBuilder aggregation =
        AggregationBuilders
                .dateRange("agg")
                .field("dateOfBirth")
                .format("yyyy")
                .addUnboundedTo("1950")    // from -infinity to 1950 (excluded)
                .addRange("1950", "1960")  // from 1950 to 1960 (excluded)
                .addUnboundedFrom("1960"); // from 1960 to +infinity
```

##### 使用聚合请求


```
import org.elasticsearch.search.aggregations.bucket.range.Range;
```

```
// sr is here your SearchResponse object
Range agg = sr.getAggregations().get("agg");

// For each entry
for (Range.Bucket entry : agg.getBuckets()) {
    String key = entry.getKeyAsString();                // Date range as key
    DateTime fromAsDate = (DateTime) entry.getFrom();   // Date bucket from as a Date
    DateTime toAsDate = (DateTime) entry.getTo();       // Date bucket to as a Date
    long docCount = entry.getDocCount();                // Doc count

    logger.info("key [{}], from [{}], to [{}], doc_count [{}]", key, fromAsDate, toAsDate, docCount);
}
```

输出：


```
key [*-1950], from [null], to [1950-01-01T00:00:00.000Z], doc_count [8]
key [1950-1960], from [1950-01-01T00:00:00.000Z], to [1960-01-01T00:00:00.000Z], doc_count [5]
key [1960-*], from [1960-01-01T00:00:00.000Z], to [null], doc_count [37]
```

#### Ip Range Aggregation Ip范围聚合


下面是如何使用 `Java API` 使用[Ip Range Aggregation](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-bucket-iprange-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
AggregatorBuilder<?> aggregation =
        AggregationBuilders
                .ipRange("agg")
                .field("ip")
                .addUnboundedTo("192.168.1.0")             // from -infinity to 192.168.1.0 (excluded)
                .addRange("192.168.1.0", "192.168.2.0")    // from 192.168.1.0 to 192.168.2.0 (excluded)
                .addUnboundedFrom("192.168.2.0");          // from 192.168.2.0 to +infinity

```

##### 使用聚合请求


```
import org.elasticsearch.search.aggregations.bucket.range.Range;
```

```
// sr is here your SearchResponse object
Range agg = sr.getAggregations().get("agg");

// For each entry
for (Range.Bucket entry : agg.getBuckets()) {
    String key = entry.getKeyAsString();            // Ip range as key
    String fromAsString = entry.getFromAsString();  // Ip bucket from as a String
    String toAsString = entry.getToAsString();      // Ip bucket to as a String
    long docCount = entry.getDocCount();            // Doc count

    logger.info("key [{}], from [{}], to [{}], doc_count [{}]", key, fromAsString, toAsString, docCount);
}
```

输出：


```
key [*-1950], from [null], to [1950-01-01T00:00:00.000Z], doc_count [8]
key [1950-1960], from [1950-01-01T00:00:00.000Z], to [1960-01-01T00:00:00.000Z], doc_count [5]
key [1960-*], from [1960-01-01T00:00:00.000Z], to [null], doc_count [37]
```


#### Histogram Aggregation 直方图聚合

> 基于文档中的某个【数值类型】字段，通过计算来动态的分桶。

下面是如何使用 `Java API` 使用[Histogram Aggregation](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-bucket-histogram-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
AggregationBuilder aggregation =
        AggregationBuilders
                .histogram("agg")
                .field("height")
                .interval(1);
```

##### 使用聚合请求


```
import org.elasticsearch.search.aggregations.bucket.histogram.Histogram;
```

```
// sr is here your SearchResponse object
Histogram agg = sr.getAggregations().get("agg");

// For each entry
for (Histogram.Bucket entry : agg.getBuckets()) {
    Number key = (Number) entry.getKey();   // Key
    long docCount = entry.getDocCount();    // Doc count

    logger.info("key [{}], doc_count [{}]", key, docCount);
}

```


####  Date Histogram Aggregation 日期范围直方图聚合

> 与直方图类似的多bucket聚合，但只能应用于日期值.。

下面是如何使用 `Java API` 使用[ Date Histogram Aggregation](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-bucket-datehistogram-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
AggregationBuilder aggregation =
        AggregationBuilders
                .dateHistogram("agg")
                .field("dateOfBirth")
                .dateHistogramInterval(DateHistogramInterval.YEAR);

```

或者把时间间隔设置为10天


```
AggregationBuilder aggregation =
        AggregationBuilders
                .dateHistogram("agg")
                .field("dateOfBirth")
                .dateHistogramInterval(DateHistogramInterval.days(10));
```


##### 使用聚合请求


```
import org.elasticsearch.search.aggregations.bucket.histogram.Histogram;
```

```
// sr is here your SearchResponse object
Histogram agg = sr.getAggregations().get("agg");

// For each entry
for (Histogram.Bucket entry : agg.getBuckets()) {
    DateTime key = (DateTime) entry.getKey();    // Key
    String keyAsString = entry.getKeyAsString(); // Key as String
    long docCount = entry.getDocCount();         // Doc count

    logger.info("key [{}], date [{}], doc_count [{}]", keyAsString, key.getYear(), docCount);
}


```

输出：

```
key [1942-01-01T00:00:00.000Z], date [1942], doc_count [1]
key [1945-01-01T00:00:00.000Z], date [1945], doc_count [1]
key [1946-01-01T00:00:00.000Z], date [1946], doc_count [1]
...
key [2005-01-01T00:00:00.000Z], date [2005], doc_count [1]
key [2007-01-01T00:00:00.000Z], date [2007], doc_count [2]
key [2008-01-01T00:00:00.000Z], date [2008], doc_count [3]
```


####  Geo Distance Aggregation 地理距离聚合

> 在geo_point字段上工作的多bucket聚合和概念上的工作非常类似于range(范围)聚合.用户可以定义原点的点和距离范围的集合。聚合计算每个文档值与原点的距离，并根据范围确定其所属的bucket(桶)（如果文档和原点之间的距离落在bucket(桶)的距离范围内，则文档属于bucket(桶) )


下面是如何使用 `Java API` 使用[  Geo Distance Aggregation](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-bucket-geodistance-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
AggregationBuilder aggregation =
        AggregationBuilders
                .geoDistance("agg", new GeoPoint(48.84237171118314,2.33320027692004))
                .field("address.location")
                .unit(DistanceUnit.KILOMETERS)
                .addUnboundedTo(3.0)
                .addRange(3.0, 10.0)
                .addRange(10.0, 500.0);

```


##### 使用聚合请求


```
import org.elasticsearch.search.aggregations.bucket.range.Range;

```

```
// sr is here your SearchResponse object
Range agg = sr.getAggregations().get("agg");

// For each entry
for (Range.Bucket entry : agg.getBuckets()) {
    String key = entry.getKeyAsString();    // key as String
    Number from = (Number) entry.getFrom(); // bucket from value
    Number to = (Number) entry.getTo();     // bucket to value
    long docCount = entry.getDocCount();    // Doc count

    logger.info("key [{}], from [{}], to [{}], doc_count [{}]", key, from, to, docCount);
}

```

输出：

```
key [*-3.0], from [0.0], to [3.0], doc_count [161]
key [3.0-10.0], from [3.0], to [10.0], doc_count [460]
key [10.0-500.0], from [10.0], to [500.0], doc_count [4925]
```

####  Geo Hash Grid Aggregation GeoHash网格聚合

> 在geo_point字段和组上工作的多bucket聚合将指向网格中表示单元格的bucket。生成的网格可以是稀疏的，并且只包含具有匹配数据的单元格。每个单元格使用具有用户可定义精度的 [geohash](http://en.wikipedia.org/wiki/Geohash) 进行标记。



下面是如何使用 `Java API` 使用[Geo Hash Grid Aggregation](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-bucket-geohashgrid-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
AggregationBuilder aggregation =
        AggregationBuilders
                .geohashGrid("agg")
                .field("address.location")
                .precision(4);

```


##### 使用聚合请求


```
import org.elasticsearch.search.aggregations.bucket.geogrid.GeoHashGrid;
```

```
// sr is here your SearchResponse object
GeoHashGrid agg = sr.getAggregations().get("agg");

// For each entry
for (GeoHashGrid.Bucket entry : agg.getBuckets()) {
    String keyAsString = entry.getKeyAsString(); // key as String
    GeoPoint key = (GeoPoint) entry.getKey();    // key as geo point
    long docCount = entry.getDocCount();         // Doc count

    logger.info("key [{}], point {}, doc_count [{}]", keyAsString, key, docCount);
}

```

输出：

```
key [gbqu], point [47.197265625, -1.58203125], doc_count [1282]
key [gbvn], point [50.361328125, -4.04296875], doc_count [1248]
key [u1j0], point [50.712890625, 7.20703125], doc_count [1156]
key [u0j2], point [45.087890625, 7.55859375], doc_count [1138]
...
