
### Delete By Query API

通过查询条件删除

```
BulkByScrollResponse response =
    DeleteByQueryAction.INSTANCE.newRequestBuilder(client)
        .filter(QueryBuilders.matchQuery("gender", "male")) //查询条件
        .source("persons") //index(索引名)
        .get();  //执行

long deleted = response.getDeleted(); //删除文档的数量
```

如果需要执行的时间比较长，可以使用异步的方式处理,结果在回调里面获取


```
DeleteByQueryAction.INSTANCE.newRequestBuilder(client)
    .filter(QueryBuilders.matchQuery("gender", "male"))      //查询            
    .source("persons")                //index(索引名)                                    
    .execute(new ActionListener<BulkByScrollResponse>() {     //回调监听     
        @Override
        public void onResponse(BulkByScrollResponse response) {
            long deleted = response.getDeleted();   //删除文档的数量                 
        }
        @Override
        public void onFailure(Exception e) {
            // Handle the exception
        }
    });
```
