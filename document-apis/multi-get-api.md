
### Multi Get API
一次获取多个文档


```
MultiGetResponse multiGetItemResponses = client.prepareMultiGet()
    .add("twitter", "tweet", "1") //一个id的方式
    .add("twitter", "tweet", "2", "3", "4") //多个id的方式
    .add("another", "type", "foo")  //可以从另外一个索引获取
    .get();

for (MultiGetItemResponse itemResponse : multiGetItemResponses) { //迭代返回值
    GetResponse response = itemResponse.getResponse();
    if (response.isExists()) {      //判断是否存在                
        String json = response.getSourceAsString(); //_source 字段
    }
}
```
更多请浏览REST [multi get](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/docs-multi-get.html) 文档
