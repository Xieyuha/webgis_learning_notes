
## 投影坐标&空间参考系
### EPSG
- `EPSG:4326`：WGS84坐标系，单位为度(经纬度)[116.40,39.90]
- `EPSG:3857`：Web墨卡托投影坐标系，单位为米(常用于Web地图)[12958343.40,4855504.63]
- `EPSG:4490`：CGCS2000地理坐标系，中国国家测绘局制定，单位为度(经纬度)
- CSCS2000投影坐标系，取决于不同的分带
- `EPSG:2362`：西安80坐标系，单位为米(常用于中国的老旧测量数据)
- `EPSG:2383`：北京54坐标系，单位为米(常用于中国的老旧测量数据)
### 火星坐标系 `GCJ-02 ` 地理坐标系
- 中国大陆地区的坐标系，基于WGS84坐标系加密偏移
- GCJ->WGS gcoord转换
- BD-09 百度坐标系，基于GCJ-02再次加密偏移
## 瓦片切分逻辑
瓦片影像：ESPG:3857 投影坐标系下的地图切片
### 原理层
#### 瓦片金字塔
瓦片金字塔（Tile Pyramid）是 WebGIS 把整张地图切成不同“清晰度层级”的瓦片，缩放越大，瓦片越多、越清晰，从而实现按需加载与快速渲染。
- 地图按 zoom 级别分层
- 每层瓦片数量 = 2^z × 2^z
- 每个瓦片大小固定（通常 256×256）
- 随 zoom 提供不同分辨率影像
- 视图只加载当前屏幕可见瓦片
### 编号体系
#### XYZ瓦片坐标系
XYZ 瓦片坐标 = (Z 层级, X 横向编号, Y 纵向编号) 的瓦片 ID，用来定位地图瓦片图像。
- Z：缩放级别（zoom level），数值越大表示地图越详细
- X：从左到右的瓦片编号（最左是 0）
- Y：从上到下的瓦片编号（最上是 0）
OSM, Google, Mapbox, OL 默认
#### TMS瓦片坐标系 
Tile Map Service（老标准）
#### QuadKey瓦片编号
四叉树
### 协议层(API)
- XYZ URL 模板
- WMTS 瓦片矩阵集
- WMS 不切片
## 地图渲染引擎
## 服务端地图服务
### 切片服务
### web服务
- WMS(Web Map Service)：地图影像服务(渲染好的地图)
- WFS(Web Feature Service)：矢量数据服务(原始数据，可交互查询、编辑)
- WCS(Web Coverage Service)：栅格数据服务(遥感影像、高程数据等)
- WMTS(Web Map Tile Service)：瓦片地图服务(预先切好的地图瓦片)

## 前端业务层
### 数据格式
#### GeoJSON
[经,纬]
规范坐标必须是`EPSG:4326`
```json
{
  "type": "FeatureCollection",
  "name": "demo_geojson",
  //数组格式
  "features": [
    {
      "type": "Feature",
      "properties": {
        "id": 1,
        "name": "Point A",
        "value": 8.5
      },
      "geometry": {
        "type": "Point",
        "coordinates": [120.15, 30.28]
      }
    },
    {
      "type": "Feature",
      "properties": {
        "id": 2,
        "name": "Line A",
        "road_type": "highway"
      },
      "geometry": {
        "type": "LineString",
        "coordinates": [
          [120.15, 30.28],
          [120.16, 30.30],
          [120.18, 30.32]
        ]
      }
    },
    {
      "type": "Feature",
      "properties": {
        "id": 3,
        "name": "Polygon A",
        "area": 12000
      },
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          [
            [120.10, 30.20],
            [120.20, 30.20],
            [120.20, 30.30],
            [120.10, 30.30],
            [120.10, 30.20]
          ]
        ]
      }
    }
  ]
}
```