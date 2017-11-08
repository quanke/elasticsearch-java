
### Transport Client

#### 不设置集群名称

```
// on startup

//此步骤添加IP，至少一个，如果设置了"client.transport.sniff"= true 一个就够了，因为添加了自动嗅探配置
TransportClient client = new PreBuiltTransportClient(Settings.EMPTY)
        .addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("host1"), 9300))
        .addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName("host2"), 9300));

// on shutdown  关闭client

client.close();
```

#### 设置集群名称

```
Settings settings = Settings.builder()
        .put("cluster.name", "myClusterName").build();  //设置ES实例的名称
TransportClient client = new PreBuiltTransportClient(settings);  //自动嗅探整个集群的状态，把集群中其他ES节点的ip添加到本地的客户端列表中
//Add transport addresses and do something with the client...
```

#### 增加自动嗅探配置
```
Settings settings = Settings.builder()
        .put("client.transport.sniff", true).build();
TransportClient client = new PreBuiltTransportClient(settings);
```

#### 其他配置

```
client.transport.ignore_cluster_name  //设置 true ，忽略连接节点集群名验证
client.transport.ping_timeout       //ping一个节点的响应时间 默认5秒
client.transport.nodes_sampler_interval //sample/ping 节点的时间间隔，默认是5s
```
> 对于ES Client，有两种形式，一个是TransportClient，一个是NodeClient。两个的区别为：
TransportClient作为一个外部访问者，通过HTTP去请求ES的集群，对于集群而言，它是一个外部因素。
NodeClient顾名思义，是作为ES集群的一个节点，它是ES中的一环，其他的节点对它是感知的，不像TransportClient那样，ES集群对它一无所知。NodeClient通信的性能会更好，但是因为是ES的一环，所以它出问题，也会给ES集群带来问题。NodeClient可以设置不作为数据节点，在elasticsearch.yml中设置，这样就不会在此节点上分配数据。

如果用ES的节点，大家仁者见仁智者见智，各按所需。

#### 实例

```
Settings esSettings = Settings.builder()

    .put("cluster.name", clusterName) //设置ES实例的名称

    .put("client.transport.sniff", true) //自动嗅探整个集群的状态，把集群中其他ES节点的ip添加到本地的客户端列表中

    .build();

    client = new PreBuiltTransportClient(esSettings);//初始化client较老版本发生了变化，此方法有几个重载方法，初始化插件等。

    //此步骤添加IP，至少一个，其实一个就够了，因为添加了自动嗅探配置

    client.addTransportAddress(new InetSocketTransportAddress(InetAddress.getByName(ip), esPort));

```