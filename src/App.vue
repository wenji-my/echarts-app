<template>
  <div id="app">
    <div id="main"></div>
  </div>
</template>

<script>
// 引入 ECharts 主模块
var echarts = require("echarts/lib/echarts");
// 引入柱状图
require("echarts/lib/chart/bar");
// 引入提示框和标题组件
require("echarts/lib/component/tooltip");
require("echarts/lib/component/title");

export default {
  name: "App",
  data() {
    return {};
  },
  methods: {
    splitData(rawData) {
      var categoryData = [];
      var values = [];
      var volumns = [];
      for (var i = 0; i < rawData.length; i++) {
        categoryData.push(rawData[i].splice(0, 1)[0]);
        values.push(rawData[i]);
        volumns.push(rawData[i][4]);
      }
      return {
        categoryData: categoryData,
        values: values,
        volumns: volumns
      };
    },
    calculateMA(dayCount, data) {
      var result = [];
      for (var i = 0, len = data.values.length; i < len; i++) {
        if (i < dayCount) {
          result.push("-");
          continue;
        }
        var sum = 0;
        for (var j = 0; j < dayCount; j++) {
          sum += data.values[i - j][1];
        }
        result.push(+(sum / dayCount).toFixed(3));
      }
      return result;
    }
  },
  mounted() {
    var rawData = require("./test.json");
    var data = this.splitData(rawData);
    console.log(data);
    // 基于准备好的dom，初始化echarts实例
    var myChart = echarts.init(document.getElementById("main"));
    myChart.resize({
      height: 200
    });
    // 绘制图表
    myChart.setOption(
      {
        backgroundColor: "#eee",
        animation: false,
        grid: [
          {
            show: false,
            left: "10%",
            right: "8%",
            height: "50%"
          },
          {
            show: false,
            left: "10%",
            right: "8%",
            bottom: "20%",
            height: "15%"
          }
        ],
        xAxis: [
          {
            type: "category",
            data: data.categoryData,
            scale: true,
            boundaryGap: false,
            axisLine: { onZero: false },
            splitLine: { show: false },
            splitNumber: 3,
            min: "dataMin",
            max: "dataMax"
          },
          {
            type: "category",
            gridIndex: 1,
            data: data.categoryData,
            scale: true,
            boundaryGap: false,
            axisLine: { onZero: false },
            axisTick: { show: false },
            splitLine: { show: false },
            axisLabel: { show: false },
            splitNumber: 3,
            min: "dataMin",
            max: "dataMax"
          }
        ],
        yAxis: [
          {
            scale: true,
            splitArea: {
              show: true
            }
          },
          {
            scale: true,
            gridIndex: 1,
            splitNumber: 5,
            axisLabel: { show: false },
            axisLine: { show: false },
            axisTick: { show: false },
            splitLine: { show: false }
          }
        ],
        dataZoom: [
          {
            type: "inside",
            xAxisIndex: [0, 1],
            start: 0,
            end: 1
          }
        ],
        series: [
          {
            name: "Dow-Jones index",
            type: "candlestick",
            data: data.values,
            itemStyle: {
              normal: {
                color: "#06B800",
                color0: "#FA0000",
                borderColor: null,
                borderColor0: null
              }
            }
          }
        ]
      }
    );
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
