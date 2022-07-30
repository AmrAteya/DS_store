<template>
  <div class="c-chart__container">
    <v-chart ref="chart" :option="chartOptions" />
  </div>
</template>

<script>
import moment from "moment";
import Axios from "axios";
import { use } from "echarts/core";
import { CanvasRenderer } from "echarts/renderers";
import { LineChart } from "echarts/charts";
import {
  TitleComponent,
  TooltipComponent,
  GridComponent,
  VisualMapComponent,
} from "echarts/components";
import VChart from "vue-echarts";

use([
  CanvasRenderer,
  LineChart,
  TitleComponent,
  TooltipComponent,
  GridComponent,
  VisualMapComponent,
]);

export default {
  name: "PerformanceChartComponent",

  components: {
    VChart,
  },

  data() {
    return {
      chartData: []
    };
  },
  created() {
    this.getDataFromApi();
  },
  computed: {
    initOptions() {
      return {
        width: "auto",
        height: "300px",
      };
    },

    chartOptions() {
      return {
        title: {
          text: "Team Performance Index",
          left: "center",
        },
        tooltip: {
          trigger: 'axis',
          transitionDuration: 0,
          confine: false,
          hideDelay: 0,
          padding: 0,
        },
        grid: {
          left: "30px",
          right: "12px",
          bottom: "2px",
          top: "6px",
          containLabel: true,
        },
        xAxis: {
          type: "category",
          showGrid: false,
          data: this.xAxisData,
          axisLine: {
            show: true,
          },
          axisTick: {
            show: false,
          },
          axisLabel: {
            fontSize: 11,
          },
        },
        yAxis: {
          axisLabel: { show: true },
          axisTick: { show: true },
          splitLine: { show: true },
        },
        visualMap: {
          top: 50,
          right: 10,
          pieces: [
          {
            gt: 0,
            lte: 50,
            color: 'red'
          },
          {
            gt: 50,
            lte: 80,
            color: 'yellow'
          },
          {
            gt: 80,
            lte: 100,
            color: 'green'
          },
        ],
        outOfRange: {
          color: '#999'
        }
        },
        tooltip: {
          trigger: "axis",
          backgroundColor: '#16253F',
          textStyle: {
            fontSize: 12,
            color: 'white'
          },
          formatter: (params) => {
            return `
              <div style="font-weight: bold; text-align: center; font-size: 14px; padding: 0; margin: 0">${params[0].axisValue}</div> 
              <div> ${params[0].marker} Team performance index: ${params[0].value} %</div>
            `
          },
        },
        series: [
          {
            data: this.yAxisData,
            type: "line",
            symbol: "circle",
            symbolSize: 2,
            cursor: "default",
            lineStyle: {
              width: 2,
            },
          }
        ],
      };
    },

    xAxisData() {
      return this.chartData.map((item) => this.formatDate(item.date_ms));
    },

    yAxisData() {
      return this.chartData.map((item) => +item.performance * 100);
    },
  },

  methods: {
    formatDate(dateInMs) {
      return moment(dateInMs).format("DD MMM YYYY");
    },
    getDataFromApi() {
      Axios.get("https://fe-task.getsandbox.com/performance")
        .then((response) => {
          this.chartData = response.data;
        })
        .catch((error) => {
          console.log(error);
        });
    },
  },
};
</script>
