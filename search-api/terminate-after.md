
### Terminate After

获取文档的最大数量，如果设置了，需要通过`SearchResponse`对象里的`isTerminatedEarly()` 判断返回文档是否达到设置的数量：


```
SearchResponse sr = client.prepareSearch(INDEX)
    .setTerminateAfter(1000)    //如果达到这个数量，提前终止
    .get();

if (sr.isTerminatedEarly()) {
    // We finished early
}
```