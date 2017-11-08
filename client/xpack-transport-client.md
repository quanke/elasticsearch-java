
### XPackTransportClient
如果 `ElasticSearch ` 服务安装了 `x-pack` 插件，需要`PreBuiltXPackTransportClient`实例才能访问


使用Maven管理项目，把下面代码增加到`pom.xml`;

> 一定要修改默认仓库地址为https://artifacts.elastic.co/maven，因为这个库没有上传到Maven中央仓库

```
<project ...>

   <repositories>
      <!-- add the elasticsearch repo -->
      <repository>
         <id>elasticsearch-releases</id>
         <url>https://artifacts.elastic.co/maven</url>
         <releases>
            <enabled>true</enabled>
         </releases>
         <snapshots>
            <enabled>false</enabled>
         </snapshots>
      </repository>
      ...
   </repositories>
   ...

   <dependencies>
      <!-- add the x-pack jar as a dependency -->
      <dependency>
         <groupId>org.elasticsearch.client</groupId>
         <artifactId>x-pack-transport</artifactId>
         <version>5.6.3</version>
      </dependency>
      ...
   </dependencies>
   ...

 </project>
```

#### 实例


```
Settings settings = Settings.builder().put("cluster.name", "xxx")
                    .put("xpack.security.transport.ssl.enabled", false)
                    .put("xpack.security.user", "xxx:xxx")
                    .put("client.transport.sniff", true).build();
try {
    client = new PreBuiltXPackTransportClient(settings)
            .addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("xxx.xxx.xxx.xxx"), 9300))
            .addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("xxx.xxx.xxx.xxx"), 9300));
} catch (UnknownHostException e) {
    e.printStackTrace();
}
```

更多请浏览 dayu-spring-boot-starter 开源项目