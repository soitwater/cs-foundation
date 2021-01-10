# g2

## 绘制流程

### 新建
```js
if (this.chart) {
  this.chart.destroy();
}
this.chart = new Chart({
  container: 'app-version-graph',
  autoFit: true,
  padding: [30, 0, 60, 0]
});
```

### data
```js
this.chart.data(this.graphData);
```

### scale
```js
this.chart.scale('data2', { 
  range: [0, 1],
  nice: true,
  alias: "版本",
  min: 0,
  max: 1000,
});
```

### toolTip
```js
this.chart.tooltip({
  title: "name",
  showTitle: false,
  showMarkers: false,
});
```

### legend
```js
this.chart.legend('version', { 
  position: 'bottom', 
  flipPage: false,
});
```

### axis
```js
this.chart.axis('version', {
  grid: null,
  tickLine: null,
  line: null,
  label: {
    style: {
      fill: '#595959'
    },
    formatter: (val) => {
      return +val;
    },
  }
});
```

## coordinate
```js
this.chart.coordinate().transpose();
```

## interaction
```js
this.chart.interaction('element-active');
```

## 绘制图表
```js
import { Chart, Util, registerShape } from "@antv/g2";

// 自定义形状
let sliceNumber = 0.0015;
registerShape('interval', 'slice-shape', {
  draw(cfg, container) {
    const points = cfg.points;
    let path = [];
    path.push(['M', points[0].x, points[0].y]);
    path.push(['L', points[1].x, points[1].y - sliceNumber]);
    path.push(['L', points[2].x, points[2].y - sliceNumber]);
    path.push(['L', points[3].x, points[3].y]);
    path.push('Z');
    path = this.parsePath(path);
    return container.addShape('path', {
      attrs: {
        fill: cfg.color,
        path,
      },
    });
  },
});

this.chart
  .interval()
  .position('version*data')
  .color('version', ["#fbd489", "#fac078", "#f9b770", "#f8a360", "#f89957"])
  .color('version', 'rgb(252,143,72)-rgb(255,215,135)')
  // 自定义形状
  .shape("slice-shape")
  .size(50)
  .style({ opacity: 0.4 })
  .label('data', {
    offset: 6,
    position: 'right',
    content: (data) => {
      return data.data;
    },
  })
  .tooltip('version*data')
  .state({
    active: {
      style: (element) => ({ matrix: Util.zoom(element.shape, 1.1) })
    }
  });
```

## 分面
```js
this.genderChart.facet("mirror", {
  fields: ["sex"],
  padding: [0, 1, 0, 0],
  transpose: true,
  showTitle: false,
  eachView: (view, facet) => {
    const facetIndex = facet.columnIndex;
    view.axis('name', false);
    const color = facetIndex === 0 ? "#1890ff" : "#F08080";
    view
      .interval()
      .position("name*data")
      .color(color)
      .size(10)
      .label("sex*data", (sex, data) => {
        let offset = (facetIndex === 1) ? -4 : 4;
        let shadowBlur = 2;
        let fill = '#666';
        return {
          position: "right",
          content: sex + ' ' + (data / sum * 100).toFixed(2) + '%',
          offsetY: -20,
          style: {
            fill,
            stroke: null,
            shadowBlur,
            shadowColor: '#ccc',
            textAlign: "center"
          }
        };
      });
  },
});
```

## 地图
```js
this.chartMap = new Chart({
  container: "user-distribution-graph",
  autoFit: true,
  padding: [30]
});
this.chartMap.tooltip({
  showTitle: false,
  showMarkers: false,
  shared: true,
});
// 同步度量
this.chartMap.scale({
  longitude: {sync: true},
  latitude: {sync: true},
});
this.chartMap.axis(false);
this.chartMap.legend(false);

// 绘制世界地图背景
const ds = new DataSet();
const worldMap = ds.createView('back').source(mapData, { type: 'GeoJSON' });
const worldMapView = this.chartMap.createView();
worldMapView.data(worldMap.rows);
worldMapView.tooltip(false);
worldMapView.polygon().position('longitude*latitude').style({
  fill: '#fff',
  stroke: '#ccc',
  lineWidth: 1
});
// 可视化用户数据
let userData = JSON.parse(JSON.stringify(this.graphData));

const userDv = ds.createView()
  .source(userData)
  .transform({
    geoDataView: worldMap,
    field: 'name',
    type: 'geo.region',
    as: ['longitude', 'latitude']
  })
const userView = this.chartMap.createView();
userView.data(userDv.rows);
userView.scale({ countryCode: { alias: "国家区号" } });
userView.scale({ name: { alias: "名称(英文)" } });
userView.scale({ chineseName: { alias: "名称(中文)" } });
userView.scale({ data: { alias: "数量" } });
userView.polygon()
  .position('longitude*latitude')
  .color('data', ["#f2e2ae", "#f3da9e", "#f3d28e", "#f4ca7e", "#f5c26e", "#f5ba5e", "#f6b24e", "#f6aa3e", "#f7a22e", "#f89a1e"])
  .tooltip('countryCode*name*chineseName*data')
  .style({
    fillOpacity: 0.85
  })
  .state({active:{style:{stroke:'red'}}});
userView.interaction('element-active');
this.chartMap.render();
```