<template>
  <div :class="className" :style="{height:height,width:width}" />
</template>

<script>
import echarts from 'echarts'
require('echarts/theme/macarons') // echarts theme
import { debounce } from '@/utils'

const animationDuration = 6000

export default {
  props: {
    className: {
      type: String,
      default: 'chart'
    },
    width: {
      type: String,
      default: '100%'
    },
    height: {
      type: String,
      default: '300px'
    }
  },
  data() {
    return {
      chart: null,
      day: [], num: []
    }
  },
  mounted() {
    this.initChart()
    this.__resizeHandler = debounce(() => {
      if (this.chart) {
        this.chart.resize()
      }
    }, 100)
    window.addEventListener('resize', this.__resizeHandler)
  },
  beforeDestroy() {
    if (!this.chart) {
      return
    }
    window.removeEventListener('resize', this.__resizeHandler)
    this.chart.dispose()
    this.chart = null
  },
  methods: {
    initChart() {
      this.chart = echarts.init(this.$el, 'macarons')
      this.$http({
        url: this.$http.adornUrl("/admin/statistics/chartPost"),
        method: "get",
      }).then(({ data }) => {
      if (data && data.code === 0) {
        
        var _info = data.result.postChart
        var day = []
        var num = []
        _info.forEach(function(item) {
          day.push(item.time)
          num.push(item.num)
        })
        this.chart.setOption({
          tooltip: {
            trigger: 'axis',
            axisPointer: { // 坐标轴指示器，坐标轴触发有效
              type: 'shadow' // 默认为直线，可选为：'line' | 'shadow'
            }
          },
          grid: {
            top: 10,
            left: '2%',
            right: '2%',
            bottom: '3%',
            containLabel: true
          },
          xAxis: [{
            type: 'category',
            data: day,
            axisTick: {
              alignWithLabel: true
            }
          }],
          yAxis: [{
            type: 'value',
            axisTick: {
              show: false
            }
          }],
          series: [{
            name: '发帖数',
            type: 'line',
            itemStyle:{
            normal:{
                lineStyle:{
                    width:2,
                    // type:'dotted',  //'dotted'点型虚线 'solid'实线 'dashed'线性虚线
                    color:'#ff9999'
                }
            }
            },
            stack: 'vistors',
            barWidth: '60%',
            data: num,
            animationDuration
          }]
        })
        }
      });
     
    }
  }
}
</script>
