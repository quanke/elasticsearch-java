
### Delete API

根据ID删除：

```
DeleteResponse response = client.prepareDelete("twitter", "tweet", "1").get();

```

更多请查看 [delete API](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/docs-delete.html) 文档

#### 配置线程

`operationThreaded` 设置为 `true` 是在不同的线程里执行此次操作

下面的例子是`operationThreaded` 设置为 `false` ：
```
GetResponse response = client.prepareGet("twitter", "tweet", "1")
        .setOperationThreaded(false)
        .get();
```

```
DeleteResponse response = client.prepareDelete("twitter", "tweet", "1")
        .setOperationThreaded(false)
        .get();
```
