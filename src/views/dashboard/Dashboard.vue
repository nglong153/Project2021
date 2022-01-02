<template>
  <v-row>
    <v-col cols="12" md="12">
      <choose-location-card
        :hidden="false"
        @choosed="setLocation"
        @choosePriceRange="handleChoosePriceRange"
        @chooseAreaRange="handleChooseAreaRange"
      ></choose-location-card>
    </v-col>
    <v-layout row justify-center>
      <v-dialog v-model="loading" persistent fullscreen content-class="loading-dialog">
        <v-container fill-height>
          <v-layout row justify-center align-center>
            <v-progress-circular indeterminate :size="70" :width="7" color="purple"></v-progress-circular>
          </v-layout>
        </v-container>
      </v-dialog>
    </v-layout>
    <v-col cols="12" md="12">
      <v-btn @click="handleViewDashboard" color="primary">Xem tổng quan</v-btn>
    </v-col>
    <!-- Biểu đồ giá trung bình theo loại bất động sản -->
    <v-slide-y-transition mode="out-in">
      <v-col cols="12" md="12" v-show="viewDashboard">
        <v-card>
          <v-card-title class="align-start">
            <span>Biểu đồ giá trung bình theo loại bất động sản</span>
            <v-spacer></v-spacer>
          </v-card-title>
          <v-card-text>
            <vue-apex-charts :options="chartOptions" :series="barChartDatas" height="200"></vue-apex-charts>
          </v-card-text>
        </v-card>
      </v-col>
    </v-slide-y-transition>
    <!-- Biểu đồ giá trung bình khu vực theo thời gian -->
    <v-slide-y-reverse-transition mode="out-in">
      <v-col cols="12" md="12" v-show="viewDashboard">
        <v-card>
          <v-card-title class="align-start">
            <span>Biểu đồ giá trung bình khu vực theo thời gian</span>
            <v-spacer></v-spacer>
          </v-card-title>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-radio-group v-model="viewBy" row @change="viewByChange">
              <v-radio label="Nhà nguyên căn" value="house"> </v-radio>
              <v-radio label="Chung cư" value="apartment"> </v-radio>
            </v-radio-group>
          </v-card-actions>
          <v-card-text>
            <!-- Chart -->
            <vue-apex-charts :options="areaChartOptions" :series="areaChartDatas" height="210"></vue-apex-charts>
          </v-card-text>
        </v-card>
      </v-col>
    </v-slide-y-reverse-transition>
  </v-row>
</template>

<script>
// eslint-disable-next-line object-curly-newline
import VueApexCharts from 'vue-apexcharts'
import ChooseLocationCard from '@/components/choose-location-card/ChooseLocationCard.vue'
import axios from 'axios'
// eslint-disable-next-line object-curly-newline
import { getCurrentInstance } from '@vue/composition-api'

// demos

export default {
  components: {
    ChooseLocationCard,
    VueApexCharts,
  },
  setup() {
    const ins = getCurrentInstance()?.proxy
    const $vuetify = ins && ins.$vuetify ? ins.$vuetify : null
    let customChartColor = $vuetify.theme.isDark ? '#f5f5f5' : '#3b3559'
    const currentTheme = $vuetify.theme.isDark
    const baseUrl = 'http://20.89.155.32/api'
    const chartOptions = {
      colors: [$vuetify.theme.currentTheme.primary, '#56ca00'],
      chart: {
        type: 'bar',
        height: 100,
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
        enabled: false,
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
        categories: ['Nhà nguyên căn', 'Chung cư'],
      },
    }
    const areaChartOptions = {
      colors: [$vuetify.theme.currentTheme.primary, '#56ca00'],
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
        categories: [
          'Tháng 1',
          'Tháng 2',
          'Tháng 3',
          'Tháng 4',
          'Tháng 5',
          'Tháng 6',
          'Tháng 7',
          'Tháng 8',
          'Tháng 9',
          'Tháng 10',
          'Tháng 11',
          'Tháng 12',
        ],
      },
      tooltip: {
        x: {
          format: 'dd/MM/yy HH:mm',
        },
      },
    }
    return {
      chartOptions,
      areaChartOptions,
      baseUrl,
      customChartColor,
      currentTheme,
    }
  },
  data: function () {
    return {
      areaChartDatas: [],
      barChartDatas: [],
      viewBy: 'house',
      viewDashboard: false,
      selectedLocationObject: {
        cityId: '5e5501caeb80a7245175dddb',
        districtId: '5e5501caeb80a7245175de2d',
        wardId: '5e5501cbeb80a7245175e13b',
        cityName: 'Hà Nội',
        districtName: 'Hai Bà Trưng',
        wardName: 'Phố Huế',
      },
      locationString: '',
      loading: false,
      locationChangeByTimeApartmentPriceArr: [],
      locationChangeByTimeHousePriceArr: [],
      areaRange: [],
      priceRange: [],
    }
  },
  methods: {
    setLocation(type, value) {
      this.selectedLocationObject[type + 'Id'] = value ? value.id : null
      this.selectedLocationObject[type + 'Name'] = value ? value.name : null
    },
    buildLocationString(locationObject) {
      let returnString = ''
      if (locationObject.wardName){
         returnString += locationObject.wardName + ','
      }
      if (locationObject.districtName) {
        returnString += locationObject.districtName + ','
      }
      if (locationObject.cityName) {
        returnString += locationObject.cityName
      }
      return returnString
    },
    handleViewDashboard() {
      let tempBarChartData = [
        {
          name: this.locationString,
          data: [44, 55],
        },
      ]
      let tempAreaChartDatas = [
          {
            name: this.locationString,
            data: [31, 40, 28, 51, 42, 109, 100, 20, 40, 10, 11, 12],
          },
        ]
      this.loading = true
      this.showDetail = false
      this.locationString = this.buildLocationString(this.selectedLocationObject)

      let locationHouse = this.buildUlr('/post/average-price', '&type=HOUSE')
      let locationApartment = this.buildUlr('/post/average-price', '&type=APARTMENT')
      let listTempProjects = this.buildUlr('/post/average-price-in-year', '&year=2021&type=HOUSE')
      let listTempPosts = this.buildUlr('/post/average-price-in-year', '&year=2021&type=APARTMENT')
      const requestOne = axios.get(locationHouse)
      const requestTwo = axios.get(locationApartment)
      const requestThree = axios.get(listTempProjects)
      const requestFour = axios.get(listTempPosts)

      axios
        .all([requestOne, requestTwo, requestThree, requestFour])
        .then(
          axios.spread((...responses) => {
            this.loading = false

            const responseOne = responses[0]
            const responseTwo = responses[1]
            const responseThree = responses[2]
            const responseFour = responses[3]
            if (responseOne && responseOne.data) {
              tempBarChartData[0].data[0] = responseOne.data.data.averagePrice
            }
          
            if (responseTwo && responseTwo.data) {
              tempBarChartData[0].data[1] = responseTwo.data.data.averagePrice
            }

            if (responseThree && responseThree.data) {
              tempAreaChartDatas[0].data = responseThree.data.data
              this.locationChangeByTimeHousePriceArr = responseThree.data.data
            }
            if (responseFour && responseFour.data) {
              this.listPosts = responseFour.data.data
              this.locationChangeByTimeApartmentPriceArr = responseFour.data.data
            }

            console.log(tempBarChartData);
            // rename of the series
            this.setNameForChartDatas(tempBarChartData,tempAreaChartDatas)
            // set data for barchart -> for reactive of vue
            this.barChartDatas = tempBarChartData
            this.areaChartDatas = tempAreaChartDatas
            this.viewDashboard = true
          }),
        )
        .catch(errors => {})
    },
    buildUlr(controller, type = null) {
      let baseUrl = 'http://20.89.155.32/api' + controller + '?'
      if (this.selectedLocationObject.districtId) {
        baseUrl += 'districtId=' + this.selectedLocationObject.districtId + '&'
      }
      if (this.selectedLocationObject.cityId) {
        baseUrl += 'cityId=' + this.selectedLocationObject.cityId
      }
      if (type != null) {
        baseUrl += type
      }
      if (this.priceRange !== undefined && this.priceRange.length > 0) {
        baseUrl += `&priceFrom=${this.priceRange[0]*1000000000}&priceTo=${this.priceRange[1]*1000000000}`
      }
      if (this.areaRange !== undefined && this.areaRange.length > 0) {
        baseUrl += `&areaFrom=${this.areaRange[0]}&areaTo=${this.areaRange[1]}`
      }
      return baseUrl
    },
    setNameForChartDatas(tempBarChart,tempAreaChartDatas) {
      tempBarChart[0].name = this.locationString
      tempAreaChartDatas[0].name = this.locationString;
    },
    viewByChange(value) {
      this.createChartDatas(value)
    },
    createChartDatas(value) {
      let tempChartDatas = []

      if (value == 'apartment') {
        tempChartDatas = [
          {
            name: this.locationString,
            data: this.locationChangeByTimeApartmentPriceArr,
          },
        ]
      } else if (value == 'house') {
        tempChartDatas = [
          {
            name: this.locationString,
            data: this.locationChangeByTimeHousePriceArr,
          },
        ]
      }
      this.areaChartDatas = tempChartDatas
    },
    handleChoosePriceRange(value) {
      this.priceRange = value
    },
    handleChooseAreaRange(value) {
      this.areaRange = value
    },
  },
}
</script>
