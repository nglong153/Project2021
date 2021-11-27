<template>
  <v-card>
    <v-card-title class="align-start">
      <span>Biểu đồ biến đổi giá trung bình</span>

      <v-spacer></v-spacer>
    </v-card-title>
    <v-card-actions>
      <v-spacer></v-spacer>
      <v-radio-group v-model="viewBy" row @change="viewByChange">
        <v-radio label="ngày" value="day"> </v-radio>
        <v-radio label="tháng" value="month"> </v-radio>
        <v-radio label="năm" value="year"> </v-radio>
      </v-radio-group>
    </v-card-actions>
    <v-card-text>
      <!-- Chart -->
      <vue-apex-charts :options="areaChartOptions" :series="areaChartDatas" height="210"></vue-apex-charts>
    </v-card-text>
  </v-card>
</template>

<script>
import VueApexCharts from 'vue-apexcharts'
// eslint-disable-next-line object-curly-newline
import { mdiDotsVertical, mdiTrendingUp, mdiCurrencyUsd } from '@mdi/js'
import { getCurrentInstance } from '@vue/composition-api'

export default {
  components: {
    VueApexCharts,
  },
  data: function () {
    return {
      areaChartDatas: [],
      areaChartOptions: {},
      viewBy: 'month',
    }
  },
  methods: {
    /**
     * Thay đổi xem chart theo ngày / tháng / năm
     */
    viewByChange(value) {
      this.createChartOptions(value)
      this.createChartDatas(value)
    },

    // tạo chartOptions cho ApexCharts
    createChartOptions(value) {
      let tempChartOptions = {
        colors: ['#9155fd', '#3b3559'],
        chart: {
          height: 350,
          type: 'area',
        },
        dataLabels: {
          enabled: false,
        },
        stroke: {
          curve: 'smooth',
        },
        xaxis: {
          type: 'category',
          categories: ['MON', 'TUE', 'WED', 'THU', 'FRI', 'SAT', 'SUN'],
        },
        tooltip: {
          x: {
            format: 'dd/MM/yy HH:mm',
          },
        },
      }
      if (value == 'day') {
        tempChartOptions.xaxis.categories = ['MON', 'TUE', 'WED', 'THU', 'FRI', 'SAT', 'SUN']
      } else if (value == 'month') {
        tempChartOptions.xaxis.categories = [
          'JAN',
          'FEB',
          'MAR',
          'APR',
          'MAY',
          'JUN',
          'JUL',
          'AUG',
          'SEP',
          'OCT',
          'NOV',
          'DEC',
        ]
      } else if (value == 'year') {
        tempChartOptions.xaxis.categories = ['2015', '2016', '2017', '2018', '2019', '2020', '2021']
      }
      this.areaChartOptions = tempChartOptions
    },

    createChartDatas(value) {
      let tempChartDatas = [
        {
          name: 'series1',
          data: [31, 40, 28, 51, 42, 109, 100],
        },
        {
          name: 'series2',
          data: [11, 32, 45, 32, 34, 52, 41],
        },
      ]
      if (value == 'day') {
        tempChartDatas = [
          {
            name: 'region1',
            data: [31, 40, 28, 51, 42, 109, 100],
          },
          {
            name: 'region2',
            data: [11, 32, 45, 32, 34, 52, 41],
          },
        ]
      } else if (value == 'month') {
        tempChartDatas = [
          {
            name: 'series1',
            data: [31, 40, 28, 51, 42, 109, 100, 20, 40, 10, 11, 12],
          },
          {
            name: 'series2',
            data: [11, 32, 45, 32, 34, 52, 41, 40, 20, 43, 55, 21],
          },
        ]
      } else if (value == 'year') {
        tempChartDatas = [
          {
            name: 'region1',
            data: [31, 40, 28, 51, 42, 109, 100],
          },
          {
            name: 'region2',
            data: [11, 32, 45, 32, 34, 52, 41],
          },
        ]
      }
      this.areaChartDatas = tempChartDatas
    },
  },
  watch: {
    viewBy: {
      immediate: true,
      handler(value) {
        this.createChartOptions(value)
        this.createChartDatas(value)
      },
    },
  },
}
</script>
