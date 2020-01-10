# Echarts

## 折线图
```html
<template>
  <div ref="echart-line" style="width:100%;height:360px;"></div>
</template>
<script>
import echarts from 'echarts'
export default {
  data() {
    return {
      option: {
        title: {
          text: 'Number of already read'
        },
        tooltip: {
          trigger: 'item',
          formatter(a) {
            let el = `
              <span style="width:70px; display:inline-block;">${a.name}</span><br/>
              <i style="display: inline-block;width: 10px;height: 10px;background:${a.color};margin-right: 5px;border-radius: 50%;}"></i>
              ${a.value}     
            `
            return el
          }
        },
        xAxis: {
          type: 'category',
          data: ['10-20', '10-21', '10-22', '10-23', '10-24', '10-25', '10-26', '10-27', '10-28', '10-29'],
          splitLine: { show: false }, //去除网格线
          splitArea: { show: false }, //保留网格区域
          axisLine: {
            lineStyle: {
              type: 'solid',
              color: '#aaa', //左边线的颜色
              width: '2' //坐标线的宽度
            }
          },
          axisLabel: {
            textStyle: {
              color: '#aaa' //坐标值得具体的颜色
            }
          }
        },
        yAxis: {
          type: 'value',
          splitLine: { show: false }, //去除网格线
          splitArea: { show: false }, //保留网格区域
          axisLine: {
            lineStyle: {
              type: 'solid',
              color: '#aaa',
              width: '2'
            }
          },
          axisLabel: {
            textStyle: {
              color: '#aaa'
            }
          }
        },
        series: [
          {
            data: [20, 32, 91, 94, 120, 30, 120, 60, 70, 80],
            type: 'line',
            itemStyle: {
              normal: {
                color: '#71bcf1', // 折点颜色
                lineStyle: {
                  color: '#71bcf1' // 折线颜色
                }
              }
            }
          }
        ]
      }
    }
  },
  mounted() {
    this.target = echarts.init(this.$refs['echart-line']) // echarts绑定控件
    this.target.setOption(this.option) // ecarts绘制饼图
  }
}
</script>
```

## 参考
- [Echarts数据可视化地理坐标系geo，开发全解+完美注释](https://blog.csdn.net/luanpeng825485697/article/details/76739684)