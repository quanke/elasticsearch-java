
### Get API
get api可以通过id查看文档：

```
GetResponse response = client.prepareGet("twitter", "tweet", "1").get();

```
更多请查看 [rest get API](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/docs-get.html) 文档

#### 配置线程

`operationThreaded` 设置为 `true` 是在不同的线程里执行此次操作

下面的例子是`operationThreaded` 设置为 `false` ：
```
GetResponse response = client.prepareGet("twitter", "tweet", "1")
        .setOperationThreaded(false)
        .get();
```