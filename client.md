## Client

Java 客户端连接 Elasticsearch

一个是`TransportClient`，一个是`NodeClient`，还有一个`XPackTransportClient`

- TransportClient：
作为一个外部访问者，请求ES的集群，对于集群而言，它是一个外部因素。


- NodeClient

作为ES集群的一个节点，它是ES中的一环，其他的节点对它是感知的。

- XPackTransportClient：

服务安装了 `x-pack` 插件