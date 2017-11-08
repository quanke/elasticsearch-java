## Client

Java 客户端连接 Elasticsearch

一个是`TransportClient`，一个是`NodeClient`，还有一个`XPackTransportClient`

- TransportClient：
作为一个外部访问者，请求ES的集群，对于集群而言，它是一个外部因素。


- NodeClient

作为ES集群的一个节点，它是ES中的一环，其他的节点对它是感知的。

- XPackTransportClient：

服务安装了 `x-pack` 插件

> 重要：客户端版本应该和服务端版本保持一致

> TransportClient旨在被Java高级REST客户端取代，该客户端执行HTTP请求而不是序列化的Java请求。 在即将到来的Elasticsearch版本中将不赞成使用TransportClient，建议使用Java高级REST客户端。

    上面的警告比较尴尬，但是在 5xx版本中使用还是没有问题的，可能使用rest 客户端兼容性更好做一些,我会继续跟进java rest 客户端