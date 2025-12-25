## option
表格的配置对象
```js
const barOption = {
  title: {              // 标题
    text: '示例柱状图'
  },
  tooltip: {            // 悬浮提示
    trigger: 'axis'
  },
  legend:{
    data: ['数量']      // 图例
  },
  dataset: {            // 数据集————一张表格
    dimensions: ['name', 'value'],
    source: [
        {name: 'A', value: 10},
        {name: 'B', value: 20},
        {name: 'C', value: 30}
    //   上面的 source 可以用下面的数组形式代替
    //   ['A', 10],
    //   ['B', 20],
    //   ['C', 30]
    ]
    //多个时必须写成数组格式
  },
  xAxis: {              // x 轴
    type: 'category',
    data: ['A', 'B', 'C'],
    axisTick: {
        alignWithLabel: true
    }
  },
  yAxis: {              // y 轴
    type: 'value'
  },
  series: [             // 数据系列（最关键）
    {
      name: '数量',
      type: 'bar',      // ← 这里的 bar, line, pie(饼),scatter(散点),map(地图) 等等，决定了图表的类型
      data: [10, 20, 30],
      showBackground: true, // 显示柱子背景
      backgroundStyle: {
        color: 'rgba(180, 180, 180, 0.2)'
      }
    }
  ]
}
```