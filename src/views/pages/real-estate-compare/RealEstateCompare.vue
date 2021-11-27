<template>
  <v-row>
    <v-col col="12" md="6">
      <choose-location-card @choosed="compare" :cityId="1"></choose-location-card>
    </v-col>
    <v-col col="12" md="6">
      <choose-location-card :cityId="2"></choose-location-card>
    </v-col>

    <v-col>
      <v-btn color="primary">Compare </v-btn>
    </v-col>
    <v-col col="12" md="12">
      <v-card>
        <vue-apex-charts :options="chartOptions" :series="series" height="430"></vue-apex-charts>
      </v-card>
    </v-col>
    <v-col col="12" md="12">
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
    </v-col>
  </v-row>
</template>

<script>
import ChooseLocationCard from '@/components/choose-location-card/ChooseLocationCard.vue'

import VueApexCharts from 'vue-apexcharts'
import { getCurrentInstance } from '@vue/composition-api'
const ins = getCurrentInstance()?.proxy
const $vuetify = ins && ins.$vuetify ? ins.$vuetify : null
export default {
  components: {
    ChooseLocationCard,
    VueApexCharts,
  },
  setup() {
    const ins = getCurrentInstance()?.proxy
    const $vuetify = ins && ins.$vuetify ? ins.$vuetify : null
    const customChartColor = $vuetify.theme.isDark ? '#3b3559' : '#f5f5f5'

    const chartOptions = {
      colors: [$vuetify.theme.currentTheme.primary, '#3b3559'],
      chart: {
        type: 'bar',
        height: 430,
      },
      plotOptions: {
        bar: {
          horizontal: true,
          dataLabels: {
            position: 'top',
            offset: -6,
          },
        },
      },
      dataLabels: {
        enabled: true,
        style: {
          colors: ['#fff'],
        },
        offsetX: 30,
      },
      stroke: {
        show: true,
        width: 1,
        colors: ['#fff'],
      },
      tooltip: {
        shared: true,
        intersect: false,
      },
      xaxis: {
        categories: [2001, 2002, 2003, 2004, 2005, 2006, 2007],
      },
    }

    return {
      chartOptions,
    }
  },

  data() {
    return {
      select: ['Hanoi', 'HCM'],
      cities: ['Hanoi', 'HCM', 'Da Nang', 'Bac Giang'],
      districts: ['Quan Hoang Mai', 'Quan Hoan Kiem', 'Quan Cau Giay'],
      wards: ['Yen So', 'Tran Phu', 'Linh Nam', 'Hai Ba Trung'],
      firstSelectedLocation: 'Yen So, Hoang Mai, Hanoi',
      secondSelectedLocation: 'Tran Phu, Quan Hoang Kiem, Hanoi',
      series: [
        {
          data: [44, 55, 41, 64, 22, 43, 21],
        },
        {
          data: [53, 32, 33, 52, 13, 44, 32],
        },
      ],
      areaChartDatas: [],
      areaChartOptions: {},
      viewBy:'month'
    }
  },
  methods: {
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
    // tạo dữ liệu cho area chart
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
    // compare : 
    compare(value){
      console.log(value);
    }
  },
  watch: {
    viewBy: {
      immediate: true,
      handler(value) {
        this.createChartOptions(value)
        this.createChartDatas(value)
      },
    },
  }
}
</script>