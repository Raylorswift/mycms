<template>
    <div class="hello">
        <div class="charts">
            <x-chart :id="id" :option="option"></x-chart>
        </div>
        <el-row :gutter="20">
          <el-col :sm="6"><div class="grid-content bg-purple"></div></el-col>
          <el-col :sm="6"><div class="grid-content bg-purple"></div></el-col>
          <el-col :sm="6"><div class="grid-content bg-purple"></div></el-col>
          <el-col :sm="6"><div class="grid-content bg-purple"></div></el-col>
        </el-row>
    </div>
</template>
<script>
  import XChart from './chart.vue'
  export default {
        // 验证类型
        data(){
          return {
            id:'visContainer',
            option:{
              chart:{
                // type:
          // 设置图表和区域的距离
          margin:[0,0,0,0],
        },
        colors:['#63b919','#cccccc'],
        title: {
          text: ''
        },
        credits:{
          enabled:false
        },
        xAxis: {
          visible:false,
          title: {
            text: ''
          }
        },
        yAxis: {
          visible:false,
          title: {
            text: ''
          }
        },
        legend: {
          // enabled:false,
          floating:true,
          layout: 'vertical',
          align: 'right',
          verticalAlign: 'top'
        },
        plotOptions: {
          series: {
            label: {
              connectorAllowed: false
            },
            pointStart: 2010
          }
        },series: [{
          name: 'PageViews',
          data: [43934, 52503, 57177, 69658, 97031, 119931, 137133, 154175,43934, 52503, 57177, 69658, 97031, 119931, 137133, 154175]
        }, {
          name: 'Visitors',
          data: [24916, 24064, 29742, 29851, 32490, 30282, 38121, 40434,24916, 24064, 29742, 29851, 32490, 30282, 38121, 40434]
        }]
            },

          }
        },
        components:{
          XChart
        }
    }
</script>