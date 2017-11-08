

### Search Template


首先查看 [Search Template](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/search-template.html) 文档

> /_search/template endpoint 允许我们在执行搜索请求和使用模板参数填充现有模板之前，能够使用 mustache 语言预先呈现搜索请求。

将模板参数定义为 `Map <String，Object>`：

```
Map<String, Object> template_params = new HashMap<>();
template_params.put("param_gender", "male");
```

可以在`config/scripts`中使用存储的 `search templates`。 例如，有一个名为 `config/scripts/template_gender.mustache` 的文件，其中包含：

```
{
    "query" : {
        "match" : {
            "gender" : "{{param_gender}}"
        }
    }
}
```

创建`search templates` 请求：


```
SearchResponse sr = new SearchTemplateRequestBuilder(client)
    .setScript("template_gender")                 //template 名
    .setScriptType(ScriptService.ScriptType.FILE) //template 存储在 gender_template.mustache 磁盘上
    .setScriptParams(template_params)             //参数
    .setRequest(new SearchRequest())              //设置执行的context（ie: 这里定义索引名称）
    .get()                                        
    .getResponse();
```


还可以将 `template` 存储在 `cluster state` 中:

> cluster state是全局性信息, 包含了整个群集中所有分片的元信息(规则, 位置, 大小等信息), 并保持每个每节的信息同步。 参考： [《为什么ElasticSearch应用开发者需要了解cluster state》](https://segmentfault.com/a/1190000008812263)


```
client.admin().cluster().preparePutStoredScript()
    .setScriptLang("mustache")
    .setId("template_gender")
    .setSource(new BytesArray(
        "{\n" +
        "    \"query\" : {\n" +
        "        \"match\" : {\n" +
        "            \"gender\" : \"{{param_gender}}\"\n" +
        "        }\n" +
        "    }\n" +
        "}")).get();
```

使用`ScriptService.ScriptType.STORED` 执行一个存储的 `templates`:


```
SearchResponse sr = new SearchTemplateRequestBuilder(client)
        .setScript("template_gender")          //template 名
        .setScriptType(ScriptType.STORED)      //template 存储在 cluster state 上
        .setScriptParams(template_params)      //参数
        .setRequest(new SearchRequest())       //设置执行的context（ie: 这里定义索引名称）
        .get()                                 //执行获取template 请求
        .getResponse(); 
```

也可以执行 内联(`inline`) `templates`：



```
sr = new SearchTemplateRequestBuilder(client)
        .setScript("{\n" +                            //template 名      
                "        \"query\" : {\n" +
                "            \"match\" : {\n" +
                "                \"gender\" : \"{{param_gender}}\"\n" +
                "            }\n" +
                "        }\n" +
                "}")
        .setScriptType(ScriptType.INLINE)    //template 是内联传递的
        .setScriptParams(template_params)    //参数             
        .setRequest(new SearchRequest())     //设置执行的context（ie: 这里定义索引名称）               
        .get()                               //执行获取template 请求             
        .getResponse();
```
