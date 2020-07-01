<template>
  <el-card>
    <div id="main" style="width:600px; height:400px;"></div>
  </el-card>
</template>

<script>
import echarts from 'echarts'
import _ from 'lodash'
export default {
  name: 'Report',
  data () {
    return {
      options: {
        title: {
          text: '用户来源'
        },
        tooltip: {
          trigger: 'axis',
          axisPointer: {
            type: 'cross',
            label: {
              backgroundColor: '#E9EEF3'
            }
          }
        },
        grid: {
          left: '3%',
          right: '4%',
          bottom: '3%',
          containLabel: true
        },
        xAxis: [
          {
            boundaryGap: false
          }
        ],
        yAxis: [
          {
            type: 'value'
          }
        ]
      }
    }
  },
  created () {},
  async mounted () {
    // 基于准备好的dom，初始化echarts实例
    var myChart = echarts.init(document.getElementById('main'))

    const { data: res } = await this.$http.get('reports/type/1')

    if (res.meta.status !== 200) {
      return this.$message.error('获取数据失败')
    }
    console.log(res.data)
    // 指定图表的配置项和数据
    const resultAA = res.data
    const result = _.merge(res.data, this.options)
    console.log(result)

    // const optionCircle = {
    //   series: [
    //     {
    //       name: '访问来源',
    //       type: 'pie',
    //       radius: '55%',
    //       data: [
    //         { value: 235, name: '视频广告' },
    //         { value: 274, name: '联盟广告' },
    //         { value: 310, name: '邮件营销' },
    //         { value: 335, name: '直接访问' },
    //         { value: 400, name: '搜索引擎' }
    //       ]
    //     }
    //   ]
    // }
    // 使用刚指定的配置项和数据显示图表。
    myChart.setOption(resultAA)
  },
  methods: {}
}
</script>

<style lang="less" scoped></style>
