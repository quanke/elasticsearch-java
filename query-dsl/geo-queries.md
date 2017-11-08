
### Geo queries 地理位置查询

Elasticsearch支持两种类型的地理数据：geo_point类型支持成对的纬度/经度，geo_shape类型支持点、线、圆、多边形、多个多边形等。
在这组的查询中：

- geo_shape查询

查找要么相交，包含的，要么指定形状不相交的地理形状的文档。

查看[Geo Shape Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-geo-shape-query.html)

> `geo_shape` 类型使用 [`Spatial4J`](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22org.locationtech.spatial4j%22%20AND%20a%3A%22spatial4j%22) 和 [`JTS`](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.vividsolutions%22%20AND%20a%3A%22jts%22) ，这两者都是可选的依赖项。 因此，必须将 [`Spatial4J`](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22org.locationtech.spatial4j%22%20AND%20a%3A%22spatial4j%22) 和 [`JTS`](http://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.vividsolutions%22%20AND%20a%3A%22jts%22) 添加到 `classpath ` 中才能使用此类型：

```
<dependency>
    <groupId>org.locationtech.spatial4j</groupId>
    <artifactId>spatial4j</artifactId>
    <version>0.6</version>                        
</dependency>

<dependency>
    <groupId>com.vividsolutions</groupId>
    <artifactId>jts</artifactId>
    <version>1.13</version>                         
    <exclusions>
        <exclusion>
            <groupId>xerces</groupId>
            <artifactId>xercesImpl</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```


```
// Import ShapeRelation and ShapeBuilder
import org.elasticsearch.common.geo.ShapeRelation;
import org.elasticsearch.common.geo.builders.ShapeBuilder;
```


```
List<Coordinate> points = new ArrayList<>();
points.add(new Coordinate(0, 0));
points.add(new Coordinate(0, 10));
points.add(new Coordinate(10, 10));
points.add(new Coordinate(10, 0));
points.add(new Coordinate(0, 0));

QueryBuilder qb = geoShapeQuery(
        "pin.location",                         //field
        ShapeBuilders.newMultiPoint(points)     //shape
        .relation(ShapeRelation.WITHIN);        //relation 可以是 ShapeRelation.CONTAINS, ShapeRelation.WITHIN, ShapeRelation.INTERSECTS 或 ShapeRelation.DISJOINT
```




```
// Using pre-indexed shapes
QueryBuilder qb = geoShapeQuery(
        "pin.location",                  //field
        "DEU",                           //The ID of the document that containing the pre-indexed shape.
        "countries")                     //Index type where the pre-indexed shape is.
        .relation(ShapeRelation.WITHIN)) //relation
        .indexedShapeIndex("shapes")     //Name of the index where the pre-indexed shape is. Defaults to shapes.
        .indexedShapePath("location");   //The field specified as path containing the pre-indexed shape. Defaults to shape.
```


- geo_bounding_box 查询

查找落入指定的矩形地理点的文档。

查看[Geo Bounding Box Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-geo-bounding-box-query.html)


```
QueryBuilder qb = geoBoundingBoxQuery("pin.location") //field
    .setCorners(40.73, -74.1,                         //bounding box top left point
                40.717, -73.99);                      //bounding box bottom right point
```


- geo_distance 查询

查找在一个中心点指定范围内的地理点文档。

查看[Geo Distance Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-geo-distance-query.html)


```
QueryBuilder qb = geoDistanceQuery("pin.location")  //field
    .point(40, -70)                                 //center point
    .distance(200, DistanceUnit.KILOMETERS);        //distance from center point
```


- geo_polygon 查询


查找指定多边形内地理点的文档。

查看[Geo Polygon Query](https://www.elastic.co/guide/en/elasticsearch/reference/5.6/query-dsl-geo-polygon-query.html)


```
List<GeoPoint> points = new ArrayList<>();             //add your polygon of points a document should fall within
points.add(new GeoPoint(40, -70));
points.add(new GeoPoint(30, -80));
points.add(new GeoPoint(20, -90));

QueryBuilder qb =
        geoPolygonQuery("pin.location", points);       //initialise the query with field and points
```
