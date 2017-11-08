### Index API
Index API 允许我们存储一个JSON格式的文档，使数据可以被搜索。文档通过index、type、id唯一确定。我们可以自己提供一个id，或者也使用Index API 为我们自动生成一个。

这里有几种不同的方式来产生JSON格式的文档(document)：

- 手动方式，使用原生的byte[]或者String
- 使用Map方式，会自动转换成与之等价的JSON
- 使用第三方库来序列化beans，如Jackson
- 使用内置的帮助类 XContentFactory.jsonBuilder()

#### 手动方式

[数据格式](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/mapping-date-format.html)
```
String json = "{" +
        "\"user\":\"kimchy\"," +
        "\"postDate\":\"2013-01-30\"," +
        "\"message\":\"trying out Elasticsearch\"" +
    "}";
```
##### 实例

```
/**  
 * 手动生成JSON  
 */  
@Test  
public void CreateJSON(){  
      
    String json = "{" +  
            "\"user\":\"fendo\"," +  
            "\"postDate\":\"2013-01-30\"," +  
            "\"message\":\"Hell word\"" +  
        "}";  
      
    IndexResponse response = client.prepareIndex("fendo", "fendodate")  
            .setSource(json)  
            .get();  
    System.out.println(response.getResult());  
      
}  
```

####  Map方式
Map是key:value数据类型，可以代表json结构.

```
Map<String, Object> json = new HashMap<String, Object>();
json.put("user","kimchy");
json.put("postDate",new Date());
json.put("message","trying out Elasticsearch");
```
##### 实例

```
 /**  
 * 使用集合  
 */  
@Test  
public void CreateList(){  
      
    Map<String, Object> json = new HashMap<String, Object>();  
    json.put("user","kimchy");  
    json.put("postDate","2013-01-30");  
    json.put("message","trying out Elasticsearch");  
      
    IndexResponse response = client.prepareIndex("fendo", "fendodate")  
            .setSource(json)  
            .get();  
    System.out.println(response.getResult());  
      
}  
```

####  序列化方式
ElasticSearch已经使用了jackson，可以直接使用它把javabean转为json.

```
import com.fasterxml.jackson.databind.*;

// instance a json mapper
ObjectMapper mapper = new ObjectMapper(); // create once, reuse

// generate json
byte[] json = mapper.writeValueAsBytes(yourbeaninstance);
```
##### 实例

```
/**  
 * 使用JACKSON序列化  
 * @throws Exception  
 */  
@Test  
public void CreateJACKSON() throws Exception{  
      
    CsdnBlog csdn=new CsdnBlog();  
    csdn.setAuthor("fendo");  
    csdn.setContent("这是JAVA书籍");  
    csdn.setTag("C");  
    csdn.setView("100");  
    csdn.setTitile("编程");  
    csdn.setDate(new Date().toString());  
      
    // instance a json mapper  
    ObjectMapper mapper = new ObjectMapper(); // create once, reuse  

    // generate json  
    byte[] json = mapper.writeValueAsBytes(csdn);  
      
    IndexResponse response = client.prepareIndex("fendo", "fendodate")  
            .setSource(json)  
            .get();  
    System.out.println(response.getResult());  
}  
```

####  XContentBuilder帮助类方式
ElasticSearch提供了一个内置的帮助类XContentBuilder来产生JSON文档

```
// Index name
String _index = response.getIndex();
// Type name
String _type = response.getType();
// Document ID (generated or not)
String _id = response.getId();
// Version (if it's the first time you index this document, you will get: 1)
long _version = response.getVersion();
// status has stored current instance statement.
RestStatus status = response.status();
```

##### 实例

```
/**  
 * 使用ElasticSearch 帮助类  
 * @throws IOException   
 */  
@Test  
public void CreateXContentBuilder() throws IOException{  
      
    XContentBuilder builder = XContentFactory.jsonBuilder()  
            .startObject()  
                .field("user", "ccse")  
                .field("postDate", new Date())  
                .field("message", "this is Elasticsearch")  
            .endObject();  
      
    IndexResponse response = client.prepareIndex("fendo", "fendodata").setSource(builder).get();  
    System.out.println("创建成功!");  
      
      
}  
```

#### 综合实例
```
 
import java.io.IOException;  
import java.net.InetAddress;  
import java.net.UnknownHostException;  
import java.util.Date;  
import java.util.HashMap;  
import java.util.Map;  
  
import org.elasticsearch.action.index.IndexResponse;  
import org.elasticsearch.client.transport.TransportClient;  
import org.elasticsearch.common.settings.Settings;  
import org.elasticsearch.common.transport.InetSocketTransportAddress;  
import org.elasticsearch.common.xcontent.XContentBuilder;  
import org.elasticsearch.common.xcontent.XContentFactory;  
import org.elasticsearch.transport.client.PreBuiltTransportClient;  
import org.junit.Before;  
import org.junit.Test;  
  
import com.fasterxml.jackson.core.JsonProcessingException;  
import com.fasterxml.jackson.databind.ObjectMapper;  
  
public class CreateIndex {  
  
    private TransportClient client;  
      
    @Before  
    public void getClient() throws Exception{  
        //设置集群名称  
        Settings settings = Settings.builder().put("cluster.name", "my-application").build();// 集群名  
        //创建client  
        client  = new PreBuiltTransportClient(settings)  
                .addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("127.0.0.1"), 9300));  
    }  
      
    /**  
     * 手动生成JSON  
     */  
    @Test  
    public void CreateJSON(){  
          
        String json = "{" +  
                "\"user\":\"fendo\"," +  
                "\"postDate\":\"2013-01-30\"," +  
                "\"message\":\"Hell word\"" +  
            "}";  
          
        IndexResponse response = client.prepareIndex("fendo", "fendodate")  
                .setSource(json)  
                .get();  
        System.out.println(response.getResult());  
          
    }  
      
      
    /**  
     * 使用集合  
     */  
    @Test  
    public void CreateList(){  
          
        Map<String, Object> json = new HashMap<String, Object>();  
        json.put("user","kimchy");  
        json.put("postDate","2013-01-30");  
        json.put("message","trying out Elasticsearch");  
          
        IndexResponse response = client.prepareIndex("fendo", "fendodate")  
                .setSource(json)  
                .get();  
        System.out.println(response.getResult());  
          
    }  
      
    /**  
     * 使用JACKSON序列化  
     * @throws Exception  
     */  
    @Test  
    public void CreateJACKSON() throws Exception{  
          
        CsdnBlog csdn=new CsdnBlog();  
        csdn.setAuthor("fendo");  
        csdn.setContent("这是JAVA书籍");  
        csdn.setTag("C");  
        csdn.setView("100");  
        csdn.setTitile("编程");  
        csdn.setDate(new Date().toString());  
          
        // instance a json mapper  
        ObjectMapper mapper = new ObjectMapper(); // create once, reuse  
  
        // generate json  
        byte[] json = mapper.writeValueAsBytes(csdn);  
          
        IndexResponse response = client.prepareIndex("fendo", "fendodate")  
                .setSource(json)  
                .get();  
        System.out.println(response.getResult());  
    }  
      
    /**  
     * 使用ElasticSearch 帮助类  
     * @throws IOException   
     */  
    @Test  
    public void CreateXContentBuilder() throws IOException{  
          
        XContentBuilder builder = XContentFactory.jsonBuilder()  
                .startObject()  
                    .field("user", "ccse")  
                    .field("postDate", new Date())  
                    .field("message", "this is Elasticsearch")  
                .endObject();  
          
        IndexResponse response = client.prepareIndex("fendo", "fendodata").setSource(builder).get();  
        System.out.println("创建成功!");  
          
          
    }  
      
}  
```


> 你还可以通过startArray(string)和endArray()方法添加数组。.field()方法可以接受多种对象类型。你可以给它传递数字、日期、甚至其他XContentBuilder对象。
