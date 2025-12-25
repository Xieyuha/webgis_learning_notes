# 方法
1. 找入口
    - map
    - layers
    - view
    - events
2. 找数据源
    - source.fromURL/GeoJSON/XYZ
3. 找渲染逻辑
    - style
    - heatmap weight
    - cluster size
4. 找响应式点
    - changed
    - watchers
    - events

# Map
- 获取鼠标所在像素要素
```js
map.forEachFeatureAtPixel(event.pixel, function (feature, layer){});
```