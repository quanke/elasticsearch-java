
### Update API
更新索引

有两种方式更新索引：
- 创建 `UpdateRequest`,通过client发送；
- 使用 `prepareUpdate()` 方法；

#### 使用UpdateRequest

```
UpdateRequest updateRequest = new UpdateRequest();
updateRequest.index("index");
updateRequest.type("type");
updateRequest.id("1");
updateRequest.doc(jsonBuilder()
        .startObject()
            .field("gender", "male")
        .endObject());
client.update(updateRequest).get();
```

#### 使用 `prepareUpdate()` 方法

```
client.prepareUpdate("ttl", "doc", "1")
        .setScript(new Script("ctx._source.gender = \"male\""  ,ScriptService.ScriptType.INLINE, null, null))//脚本可以是本地文件存储的，如果使用文件存储的脚本，需要设置 ScriptService.ScriptType.FILE 
        .get();

client.prepareUpdate("ttl", "doc", "1")
        .setDoc(jsonBuilder()   //合并到现有文档
            .startObject()
                .field("gender", "male")
            .endObject())
        .get();
```

#### Update by script

使用脚本更新文档

```
UpdateRequest updateRequest = new UpdateRequest("ttl", "doc", "1")
        .script(new Script("ctx._source.gender = \"male\""));
client.update(updateRequest).get();

```

#### Update by merging documents

合并文档

```
UpdateRequest updateRequest = new UpdateRequest("index", "type", "1")
        .doc(jsonBuilder()
            .startObject()
                .field("gender", "male")
            .endObject());
client.update(updateRequest).get();
```


#### Upsert
更新插入,如果存在文档就更新，如果不存在就插入


```
IndexRequest indexRequest = new IndexRequest("index", "type", "1")
        .source(jsonBuilder()
            .startObject()
                .field("name", "Joe Smith")
                .field("gender", "male")
            .endObject());
UpdateRequest updateRequest = new UpdateRequest("index", "type", "1")
        .doc(jsonBuilder()
            .startObject()
                .field("gender", "male")
            .endObject())
        .upsert(indexRequest); //如果不存在此文档 ，就增加 `indexRequest`
client.update(updateRequest).get();
```

如果 `index/type/1` 存在，类似下面的文档：


```
{
    "name"  : "Joe Dalton",
    "gender": "male"        
}
```

如果不存在，会插入新的文档：


```
{
    "name" : "Joe Smith",
    "gender": "male"
}
```
