<template>
  <v-row>
    <v-col cols="12" md="4">
      <v-card>
        <v-card-title class="align-start">
          <span>Giá trung bình theo loại bất động sản</span>
          <v-spacer></v-spacer>
        </v-card-title>
        <v-card-text>
          <vue-apex-charts :options="chartOptions" :series="chartData" height="210"> </vue-apex-charts>
        </v-card-text>
      </v-card>
    </v-col>
    <v-col cols="12" md="8">
      <v-card>
        <v-card-title>
          <span class="me-3">Các dự án tại khu vực này</span>
        </v-card-title>
        <v-card-text>
          <v-list>
            <v-list-item
              v-for="(data, index) in deposit"
              :key="data.img"
              :class="`d-flex px-0 ${index > 0 ? 'mt-4' : ''}`"
            >
              <v-img contain max-height="30" max-width="30" :src="data.img" class="me-3"></v-img>
              <div class="d-flex align-center flex-grow-1 flex-wrap">
                <div class="me-auto pe-2">
                  <h4 class="font-weight-semibold">
                    {{ data.title }}
                  </h4>
                  <span class="text-xs">{{ data.source }}</span>
                </div>
              </div>
            </v-list-item>
          </v-list>
        </v-card-text>
      </v-card>
    </v-col>
    <v-col cols="12" md="12"
    >
    <v-card>
        <v-card-title>
          <span class="me-3">Các dự án tại khu vực này</span>
        </v-card-title>
        <v-card-text>
          <v-list>
            <v-list-item
              v-for="(data, index) in deposit"
              :key="data.img"
              :class="`d-flex px-0 ${index > 0 ? 'mt-4' : ''}`"
            >
              <v-img contain max-height="30" max-width="30" :src="data.img" class="me-3"></v-img>
              <div class="d-flex align-center flex-grow-1 flex-wrap">
                <div class="me-auto pe-2">
                  <h4 class="font-weight-semibold">
                    {{ data.title }}
                  </h4>
                  <span class="text-xs">{{ data.source }}</span>
                </div>
              </div>
            </v-list-item>
          </v-list>
        </v-card-text>
      </v-card>
    </v-col>
  </v-row>
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
  setup() {
    const deposit = [
      {
        img: require('@/assets/images/logos/gumroad.png'),
        title: 'Gumroad Account',
        subtitle: 'Sell UI Kit',
        source: 'batdongsan.vn',
      },
      {
        img: require('@/assets/images/logos/master.png'),
        title: 'Mastercard',
        subtitle: 'Wallet deposit',
        source: 'batdongsan.vn',
      },
      {
        img: require('@/assets/images/logos/stripe-account.png'),
        title: 'Stripe Account',
        subtitle: 'iOS Application',
        source: 'batdongsan.vn',
      },
      {
        img: require('@/assets/images/logos/american-bank.png'),
        title: 'American Bank',
        subtitle: 'Bank Transfer',
        source: 'batdongsan.vn',
      },
      {
        img: require('@/assets/images/logos/bank-account.png'),
        title: 'Bank Account',
        subtitle: 'Wallet deposit',
        source: 'batdongsan.vn',
      },
    ]
    const ins = getCurrentInstance()?.proxy
    const $vuetify = ins && ins.$vuetify ? ins.$vuetify : null
    const customChartColor = $vuetify.theme.isDark ? '#3b3559' : '#f5f5f5'

    const chartOptions = {
      colors: [
        customChartColor,
        customChartColor,
        customChartColor,
        customChartColor,
        $vuetify.theme.currentTheme.primary,
        customChartColor,
        customChartColor,
      ],
      chart: {
        type: 'bar',
        toolbar: {
          show: false,
        },
        offsetX: -15,
      },
      plotOptions: {
        bar: {
          columnWidth: '40%',
          distributed: true,
          borderRadius: 8,
          startingShape: 'rounded',
          endingShape: 'rounded',
        },
      },
      dataLabels: {
        enabled: false,
      },
      legend: {
        show: false,
      },
      xaxis: {
        categories: ['S', 'M', 'T', 'W', 'T', 'F', 'S'],
        axisBorder: {
          show: false,
        },
        axisTicks: {
          show: false,
        },
        tickPlacement: 'on',
        labels: {
          show: false,
          style: {
            fontSize: '12px',
          },
        },
      },
      yaxis: {
        show: true,
        tickAmount: 4,
        labels: {
          offsetY: 3,
          formatter: value => `$${value}`,
        },
      },
      stroke: {
        width: [2, 2],
      },
      grid: {
        strokeDashArray: 12,
        padding: {
          right: 0,
        },
      },
    }

    const chartData = [
      {
        data: [40, 60, 50, 60, 75, 60, 50, 65],
      },
    ]

    return {
      chartOptions,
      chartData,
      deposit,
      icons: {
        mdiDotsVertical,
        mdiTrendingUp,
        mdiCurrencyUsd,
      },
    }
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
