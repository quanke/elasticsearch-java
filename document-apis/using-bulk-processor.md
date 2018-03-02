
### 使用 Bulk Processor
BulkProcessor 提供了一个简单的接口，在给定的大小数量上定时批量自动请求

#### 创建`BulkProcessor`实例

首先创建`BulkProcessor`实例

```
import org.elasticsearch.action.bulk.BackoffPolicy;
import org.elasticsearch.action.bulk.BulkProcessor;
import org.elasticsearch.common.unit.ByteSizeUnit;
import org.elasticsearch.common.unit.ByteSizeValue;
import org.elasticsearch.common.unit.TimeValue;
```

```
BulkProcessor bulkProcessor = BulkProcessor.builder(
        client,  //增加elasticsearch客户端
        new BulkProcessor.Listener() {
            @Override
            public void beforeBulk(long executionId,
                                   BulkRequest request) { ... } //调用bulk之前执行 ，例如你可以通过request.numberOfActions()方法知道numberOfActions

            @Override
            public void afterBulk(long executionId,
                                  BulkRequest request,
                                  BulkResponse response) { ... } //调用bulk之后执行 ，例如你可以通过request.hasFailures()方法知道是否执行失败

            @Override
            public void afterBulk(long executionId,
                                  BulkRequest request,
                                  Throwable failure) { ... } //调用失败抛 Throwable
        })
        .setBulkActions(10000) //每次10000请求
        .setBulkSize(new ByteSizeValue(5, ByteSizeUnit.MB)) //拆成5mb一块
        .setFlushInterval(TimeValue.timeValueSeconds(5)) //无论请求数量多少，每5秒钟请求一次。
        .setConcurrentRequests(1) //设置并发请求的数量。值为0意味着只允许执行一个请求。值为1意味着允许1并发请求。
        .setBackoffPolicy(
            BackoffPolicy.exponentialBackoff(TimeValue.timeValueMillis(100), 3))//设置自定义重复请求机制，最开始等待100毫秒，之后成倍更加，重试3次，当一次或多次重复请求失败后因为计算资源不够抛出 EsRejectedExecutionException 异常，可以通过BackoffPolicy.noBackoff()方法关闭重试机制
        .build();
```
#### BulkProcessor 默认设置
- bulkActions  1000 
- bulkSize 5mb
- 不设置flushInterval
- concurrentRequests 为 1 ，异步执行
- backoffPolicy 重试 8次，等待50毫秒

#### 增加requests
然后增加`requests`到`BulkProcessor`
```
bulkProcessor.add(new IndexRequest("twitter", "tweet", "1").source(/* your doc here */));
bulkProcessor.add(new DeleteRequest("twitter", "tweet", "2"));
```
#### 关闭 Bulk Processor
当所有文档都处理完成，使用`awaitClose` 或 `close` 方法关闭`BulkProcessor`:


```
bulkProcessor.awaitClose(10, TimeUnit.MINUTES);

```
或

```
bulkProcessor.close();

```

#### 在测试中使用Bulk Processor

如果你在测试种使用`Bulk Processor`可以执行同步方法
```
BulkProcessor bulkProcessor = BulkProcessor.builder(client, new BulkProcessor.Listener() { /* Listener methods */ })
        .setBulkActions(10000)
        .setConcurrentRequests(0)
        .build();

// Add your requests
bulkProcessor.add(/* Your requests */);

// Flush any remaining requests
bulkProcessor.flush();

// Or close the bulkProcessor if you don't need it anymore
bulkProcessor.close();

// Refresh your indices
client.admin().indices().prepareRefresh().get();

// Now you can start searching!
client.prepareSearch().get();
```