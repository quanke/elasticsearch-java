

### Metrics aggregations
> 计算度量这类的聚合操作是以使用一种方式或者从文档中提取需要聚合的值为基础的。这些数据不但可以从文档（使用数据属性）的属性中提取出来，也可以使用脚本生成。

> 数值计量聚合操作是能够产生具体的数值的一种计量聚合操作。一些聚合操作输出单个的计量数值（例如avg），并且被称作single-value numeric metric aggregation，其他产生多个计量数值（例如 stats）的称作 multi-value numeric metrics aggregation。这两种不同的聚合操作只有在桶聚合的子聚合操作中才会有不同的表现（有些桶聚合可以基于每个的数值计量来对返回的桶进行排序）。

#### Min Aggregatione 最小值聚合

下面是如何用Java API 使用[最小值聚合](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-metrics-min-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
MinAggregationBuilder aggregation =
        AggregationBuilders
                .min("agg")
                .field("height");
```

##### 使用聚合请求


```
import org.elasticsearch.search.aggregations.metrics.min.Min;

```


```
// sr is here your SearchResponse object
Min agg = sr.getAggregations().get("agg");
double value = agg.getValue();
```

#### Max Aggregation 最大值聚合
下面是如何用Java API 使用[最大值聚合](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-metrics-max-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
MaxAggregationBuilder aggregation =
        AggregationBuilders
                .max("agg")
                .field("height");
```

##### 使用聚合请求


```
import org.elasticsearch.search.aggregations.metrics.max.Max;

```


```
// sr is here your SearchResponse object
Max agg = sr.getAggregations().get("agg");
double value = agg.getValue();
```


#### Sum Aggregation 求和聚合

下面是如何用Java API 使用[求和聚合](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-metrics-sum-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
SumAggregationBuilder aggregation =
        AggregationBuilders
                .sum("agg")
                .field("height");
```

##### 使用聚合请求


```
import org.elasticsearch.search.aggregations.metrics.sum.Sum;

```


```
// sr is here your SearchResponse object
Sum agg = sr.getAggregations().get("agg");
double value = agg.getValue();
```


#### Avg Aggregation 平均值聚合

下面是如何用Java API 使用[平均值聚合](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-metrics-avg-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
AvgAggregationBuilder aggregation =
        AggregationBuilders
                .avg("agg")
                .field("height");
```

##### 使用聚合请求


```
import org.elasticsearch.search.aggregations.metrics.avg.Avg;

```


```
// sr is here your SearchResponse object
Avg agg = sr.getAggregations().get("agg");
double value = agg.getValue();
```

#### Stats Aggregation 统计聚合

> 统计聚合——基于文档的某个值，计算出一些统计信息（min、max、sum、count、avg）, 用于计算的值可以是特定的数值型字段，也可以通过脚本计算而来。

下面是如何用Java API 使用[统计聚合](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-metrics-stats-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
StatsAggregationBuilder aggregation =
        AggregationBuilders
                .stats("agg")
                .field("height");
```

##### 使用聚合请求


```
import org.elasticsearch.search.aggregations.metrics.stats.Stats;

```

```
// sr is here your SearchResponse object
Stats agg = sr.getAggregations().get("agg");
double min = agg.getMin();
double max = agg.getMax();
double avg = agg.getAvg();
double sum = agg.getSum();
long count = agg.getCount();
```


#### Extended Stats Aggregation 扩展统计聚合

> 扩展统计聚合——基于文档的某个值，计算出一些统计信息（比普通的stats聚合多了sum_of_squares、variance、std_deviation、std_deviation_bounds）, 用于计算的值可以是特定的数值型字段，也可以通过脚本计算而来。



下面是如何用Java API 使用[扩展统计聚合](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-metrics-extendedstats-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
ExtendedStatsAggregationBuilder aggregation =
        AggregationBuilders
                .extendedStats("agg")
                .field("height");
```

##### 使用聚合请求


```
import org.elasticsearch.search.aggregations.metrics.stats.extended.ExtendedStats;


```

```
// sr is here your SearchResponse object
ExtendedStats agg = sr.getAggregations().get("agg");
double min = agg.getMin();
double max = agg.getMax();
double avg = agg.getAvg();
double sum = agg.getSum();
long count = agg.getCount();
double stdDeviation = agg.getStdDeviation();
double sumOfSquares = agg.getSumOfSquares();
double variance = agg.getVariance();
```


#### Value Count Aggregation 值计数聚合

> 值计数聚合——计算聚合文档中某个值的个数, 用于计算的值可以是特定的数值型字段，也可以通过脚本计算而来。

> 该聚合一般域其它 single-value 聚合联合使用，比如在计算一个字段的平均值的时候，可能还会关注这个平均值是由多少个值计算而来。


下面是如何用Java API 使用[值计数聚合](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-metrics-valuecount-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
ValueCountAggregationBuilder aggregation =
        AggregationBuilders
                .count("agg")
                .field("height");
```

##### 使用聚合请求


```
import org.elasticsearch.search.aggregations.metrics.valuecount.ValueCount;


```

```
// sr is here your SearchResponse object
ValueCount agg = sr.getAggregations().get("agg");
long value = agg.getValue();
```


#### Percentile Aggregation 百分百聚合

> 百分百聚合——基于聚合文档中某个数值类型的值，求这些值中的一个或者多个百分比, 用于计算的值可以是特定的数值型字段，也可以通过脚本计算而来。


下面是如何用Java API 使用[百分百聚合](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-metrics-percentile-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
PercentilesAggregationBuilder aggregation =
        AggregationBuilders
                .percentiles("agg")
                .field("height");
```

可以提供百分位数，而不是使用默认值：

```
PercentilesAggregationBuilder aggregation =
        AggregationBuilders
                .percentiles("agg")
                .field("height")
                .percentiles(1.0, 5.0, 10.0, 20.0, 30.0, 75.0, 95.0, 99.0);
```

##### 使用聚合请求


```
import org.elasticsearch.search.aggregations.metrics.percentiles.Percentile;
import org.elasticsearch.search.aggregations.metrics.percentiles.Percentiles;

```

```
// sr is here your SearchResponse object
Percentiles agg = sr.getAggregations().get("agg");
// For each entry
for (Percentile entry : agg) {
    double percent = entry.getPercent();    // Percent
    double value = entry.getValue();        // Value

    logger.info("percent [{}], value [{}]", percent, value);
}
```

大概输出：


```
percent [1.0], value [0.814338896154595]
percent [5.0], value [0.8761912455821302]
percent [25.0], value [1.173346540141847]
percent [50.0], value [1.5432023318692198]
percent [75.0], value [1.923915462033674]
percent [95.0], value [2.2273644908535335]
percent [99.0], value [2.284989339108279]
```

#### Percentile Ranks Aggregation 百分比等级聚合

> 一个multi-value指标聚合，它通过从聚合文档中提取数值来计算一个或多个百分比。这些值可以从特定数值字段中提取，也可以由提供的脚本生成。

> 注意：请参考百分比（通常）近视值（percentiles are (usually approximate)）和压缩（Compression）以获得关于近视值的建议和内存使用的百分比排位聚合。百分比排位展示那些在某一值之下的观测值的百分比。例如，假如某一直大于等于被观测值的95%，则称其为第95百分位数。假设你的数据由网页加载时间组成。你可能有一个服务协议，95%页面需要在15ms加载完全，99%页面在30ms加载完全。


下面是如何用Java API 使用[百分比等级聚合](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-metrics-percentile-rank-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
PercentilesAggregationBuilder aggregation =
        AggregationBuilders
                .percentiles("agg")
                .field("height");
```


##### 使用聚合请求


```
import org.elasticsearch.search.aggregations.metrics.percentiles.Percentile;
import org.elasticsearch.search.aggregations.metrics.percentiles.PercentileRanks;

```

```
// sr is here your SearchResponse object
PercentileRanks agg = sr.getAggregations().get("agg");
// For each entry
for (Percentile entry : agg) {
    double percent = entry.getPercent();    // Percent
    double value = entry.getValue();        // Value

    logger.info("percent [{}], value [{}]", percent, value);
}
```

大概输出：


```
percent [29.664353095090945], value [1.24]
percent [73.9335313461868], value [1.91]
percent [94.40095147327283], value [2.22]
```

#### Cardinality Aggregation 基数聚合

> 基于文档的某个值，计算文档非重复的个数（去重计数）。。这些值可以从特定数值字段中提取，也可以由提供的脚本生成。


下面是如何用Java API 使用[基数聚合](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-metrics-cardinality-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
CardinalityAggregationBuilder aggregation =
        AggregationBuilders
                .cardinality("agg")
                .field("tags");
```


##### 使用聚合请求


```
import org.elasticsearch.search.aggregations.metrics.cardinality.Cardinality;
```

```
// sr is here your SearchResponse object
Cardinality agg = sr.getAggregations().get("agg");
long value = agg.getValue();
```


####  Geo Bounds Aggregation 地理边界聚合

> 地理边界聚合——基于文档的某个字段（geo-point类型字段），计算出该字段所有地理坐标点的边界（左上角/右下角坐标点）。


下面是如何用Java API 使用[地理边界聚合](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-metrics-geobounds-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
GeoBoundsBuilder aggregation =
        GeoBoundsAggregationBuilder
                .geoBounds("agg")
                .field("address.location")
                .wrapLongitude(true);
```


##### 使用聚合请求


```
import org.elasticsearch.search.aggregations.metrics.geobounds.GeoBounds;

```

```
// sr is here your SearchResponse object
GeoBounds agg = sr.getAggregations().get("agg");
GeoPoint bottomRight = agg.bottomRight();
GeoPoint topLeft = agg.topLeft();
logger.info("bottomRight {}, topLeft {}", bottomRight, topLeft);
```

大概会输出：


```
bottomRight [40.70500764381921, 13.952946866893775], topLeft [53.49603022435221, -4.190029308156676]

```


####  Top Hits Aggregation 最高匹配权值聚合

> 最高匹配权值聚合——跟踪聚合中相关性最高的文档。该聚合一般用做 sub-aggregation，以此来聚合每个桶中的最高匹配的文档。



下面是如何用Java API 使用[最高匹配权值聚合](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-aggregations-metrics-top-hits-aggregation.html)

##### 准备聚合请求

下面是如何创建聚合请求的是示例：


```
AggregationBuilder aggregation =
    AggregationBuilders
        .terms("agg").field("gender")
        .subAggregation(
            AggregationBuilders.topHits("top")
        );
```

大多数标准的搜索选项可以使用，比如：`from`, `size`, `sort`, `highlight`, `explain` …


```
AggregationBuilder aggregation =
    AggregationBuilders
        .terms("agg").field("gender")
        .subAggregation(
            AggregationBuilders.topHits("top")
                .explain(true)
                .size(1)
                .from(10)
        );
```


##### 使用聚合请求


```
import org.elasticsearch.search.aggregations.bucket.terms.Terms;
import org.elasticsearch.search.aggregations.metrics.tophits.TopHits;

```

```
// sr is here your SearchResponse object
Terms agg = sr.getAggregations().get("agg");

// For each entry
for (Terms.Bucket entry : agg.getBuckets()) {
    String key = entry.getKey();                    // bucket key
    long docCount = entry.getDocCount();            // Doc count
    logger.info("key [{}], doc_count [{}]", key, docCount);

    // We ask for top_hits for each bucket
    TopHits topHits = entry.getAggregations().get("top");
    for (SearchHit hit : topHits.getHits().getHits()) {
        logger.info(" -> id [{}], _source [{}]", hit.getId(), hit.getSourceAsString());
    }
}
```

大概会输出：


```
key [male], doc_count [5107]
 -> id [AUnzSZze9k7PKXtq04x2], _source [{"gender":"male",...}]
 -> id [AUnzSZzj9k7PKXtq04x4], _source [{"gender":"male",...}]
 -> id [AUnzSZzl9k7PKXtq04x5], _source [{"gender":"male",...}]
key [female], doc_count [4893]
 -> id [AUnzSZzM9k7PKXtq04xy], _source [{"gender":"female",...}]
 -> id [AUnzSZzp9k7PKXtq04x8], _source [{"gender":"female",...}]
 -> id [AUnzSZ0W9k7PKXtq04yS], _source [{"gender":"female",...}]

```


####   Scripted Metric Aggregation

> 此功能为实验性的，不建议生产使用，所以也不做过多说明 有兴趣可以自己参考 [官方文档](https://www.elastic.co/guide/en/elasticsearch/client/java-api/current/_metrics_aggregations.html#java-aggs-metrics-scripted-metric)

