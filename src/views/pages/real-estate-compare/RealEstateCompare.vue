<template>
  <v-row>
    <v-col cols="12" md="6">
      <choose-location-card @choosed="setFirstLocation" :cityId="1"></choose-location-card>
    </v-col>
    <v-col cols="12" md="6">
      <choose-location-card @choosed="setSecondLocation" :cityId="2"></choose-location-card>
    </v-col>

    <v-col cols="12" md="12">
      <v-btn @click="compare" color="primary">So sánh 2 khu vực</v-btn>
    </v-col>
    <v-spacer></v-spacer>
    <v-layout row justify-center>
      <v-dialog v-model="loading" persistent fullscreen content-class="loading-dialog">
        <v-container fill-height>
          <v-layout row justify-center align-center>
            <v-progress-circular indeterminate :size="70" :width="7" color="purple"></v-progress-circular>
          </v-layout>
        </v-container>
      </v-dialog>
    </v-layout>
    <!-- Biểu đồ giá trung bình theo loại bất động sản -->
    <v-slide-y-transition mode="out-in">
      <v-col cols="12" md="12" v-show="expand">
        <v-card>
          <v-card-title class="align-start">
            <span>Biểu đồ giá trung bình theo loại bất động sản</span>
            <v-spacer></v-spacer>
          </v-card-title>
          <v-card-text>
            <vue-apex-charts :options="chartOptions" :series="barChartDatas" height="250"></vue-apex-charts>
          </v-card-text>
        </v-card>
      </v-col>
    </v-slide-y-transition>
    <!-- Biểu đồ giá trung bình khu vực theo thời gian -->
    <v-slide-y-reverse-transition mode="out-in">
      <v-col cols="12" md="12" v-show="expand">
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
    <v-snackbar v-model="snackbar">
      Không thể so sánh 2 địa điểm giống nhau :)

      <template v-slot:action="{ attrs }">
        <v-btn color="pink" text v-bind="attrs" @click="snackbar = false"> Close </v-btn>
      </template>
    </v-snackbar>
  </v-row>
</template>

<script>
import ChooseLocationCard from '@/components/choose-location-card/ChooseLocationCard.vue'
import axios from 'axios'
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
    let customChartColor = $vuetify.theme.isDark ?  '#f5f5f5' :'#3b3559'
    const currentTheme =  $vuetify.theme.isDark;
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
    const areaChartOptions =  {
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
          categories: ['Tháng 1',
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
          'Tháng 12',],
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
      currentTheme
    }
  },

  data() {
    return {
      firstSelectedLocationObject: {
        cityId: '5e5501caeb80a7245175dddb',
        districtId: '5e5501caeb80a7245175de2d',
        wardId: '5e5501cbeb80a7245175e13b',
        cityName: 'Hà Nội',
        districtName: 'Hai Bà Trưng',
        wardName: 'Phố Huế',
      },
      secondSelectedLocationObject: {
        cityId: '5e5501caeb80a7245175dddb',
        districtId: '5e5501caeb80a7245175de2d',
        wardId: '5e5501cbeb80a7245175e13b',
        cityName: 'Hà Nội',
        districtName: 'Hai Bà Trưng',
        wardName: 'Phố Huế',
      },
      firstSelectedLocationString: '',
      secondSelectedLocationString: '',
      barChartDatas: [
        {
          name: this.firstSelectedLocationString,
          data: [44, 55],
        },
        {
          name: this.secondSelectedLocationString,
          data: [53, 32],
        },
      ],
      areaChartDatas: [],
      viewBy: 'house',
      expand: false,
      loading: false,
      snackbar: false,
      firstLocationChangeByTimeHousePriceArr : [],
      firstLocationChangeByTimeApartmentPriceArr :[],
      secondLocationChangeByTimeHousePriceArr :[],
      secondLocationChangeByTimeApartmentPriceArr :[],
      darkTheme : this.$vuetify.theme.isDark
    }
  },

  methods: {
    viewByChange(value) {
      this.createChartDatas(value)
    },

    // tạo dữ liệu cho area chart
    createChartDatas(value) {
      let tempChartDatas = [];

      if (value == 'apartment') {
        tempChartDatas = [
          {
            name: this.firstSelectedLocationString,
            data: this.firstLocationChangeByTimeApartmentPriceArr,
          },
          {
            name: this.secondSelectedLocationString,
            data: this.secondLocationChangeByTimeApartmentPriceArr,
          },
        ]
      } 
      else if (value == 'house'){
        tempChartDatas = [
          {
            name: this.firstSelectedLocationString,
            data: this.firstLocationChangeByTimeHousePriceArr,
          },
          {
            name: this.secondSelectedLocationString,
            data: this.secondLocationChangeByTimeHousePriceArr,
          },
        ]
      }
      this.areaChartDatas = tempChartDatas
    },
    // compare :
    compare() {
      if (this.checkLocationValid()) {
        let tempBarChartData = [
          {
            name: this.firstSelectedLocationString,
            data: [44, 55],
          },
          {
            name: this.secondSelectedLocationString,
            data: [53, 32],
          },
        ]
        let tempAreaChartDatas = [
          {
            name: 'series1',
            data: [31, 40, 28, 51, 42, 109, 100, 20, 40, 10, 11, 12],
          },
          {
            name: 'series2',
            data: [11, 32, 45, 32, 34, 52, 41, 40, 20, 43, 55, 21],
          },
        ]
        this.loading = true
        this.expand = false
        this.firstSelectedLocationString = this.buildLocationString(this.firstSelectedLocationObject)
        this.secondSelectedLocationString = this.buildLocationString(this.secondSelectedLocationObject)

        let firstLocationHouse = this.buildFirstObjectUlr('average-price', '&type=HOUSE')
        let firstLocationApartment = this.buildFirstObjectUlr('average-price', '&type=APARTMENT')
        let secondLocationHouse = this.buildSecondObjectUrl('average-price', '&type=HOUSE')
        let secondLocationApartment = this.buildSecondObjectUrl('average-price', '&type=APARTMENT')
        let firstLocationChangeByTimeHousePrice = this.buildFirstObjectUlr('average-price-in-year', '&year=2021&type=HOUSE')
        let secondLocationChangeByTimeHousePrice = this.buildSecondObjectUrl('average-price-in-year', '&year=2021&type=HOUSE')
        let firstLocationChangeByTimeApartmentPrice = this.buildFirstObjectUlr('average-price-in-year', '&year=2021&type=APARTMENT')
        let secondLocationChangeByTimeApartmentPrice = this.buildSecondObjectUrl('average-price-in-year', '&year=2021&type=APARTMENT')


        const requestOne = axios.get(firstLocationHouse)
        const requestTwo = axios.get(firstLocationApartment)
        const requestThree = axios.get(secondLocationHouse)
        const requestFour = axios.get(secondLocationApartment)
        const requestFive = axios.get(firstLocationChangeByTimeHousePrice)
        const requestSix = axios.get(secondLocationChangeByTimeHousePrice)
        const requestSeven = axios.get(firstLocationChangeByTimeApartmentPrice);
        const requestEight = axios.get(secondLocationChangeByTimeApartmentPrice)


        axios
          .all([requestOne, requestTwo, requestThree, requestFour, requestFive, requestSix,requestSeven,requestEight])
          .then(
            axios.spread((...responses) => {
              this.loading = false
              const responseOne = responses[0]
              const responseTwo = responses[1]
              const responseThree = responses[2]
              const responseFour = responses[3]
              const responseFive = responses[4]
              const responseSix = responses[5]
              const responseSeven = responses[6]
              const responseEight = responses[7]

              if (responseOne && responseOne.data) {
                tempBarChartData[0].data[0] = responseOne.data.data.averagePrice
              }
              if (responseTwo && responseTwo.data) {
                tempBarChartData[0].data[1] = responseTwo.data.data.averagePrice
              }

              if (responseThree && responseThree.data) {
                tempBarChartData[1].data[0] = responseThree.data.data.averagePrice
              }
              if (responseFour && responseFour.data) {
                tempBarChartData[1].data[1] = responseFour.data.data.averagePrice
              }

              if (responseFive && responseFive.data) {
                this.viewBy  = 'house';
                tempAreaChartDatas[0].data = responseFive.data.data
                this.firstLocationChangeByTimeHousePriceArr =  responseFive.data.data
              }
              if (responseSix && responseSix.data) {
                this.viewBy  = 'house';
                tempAreaChartDatas[1].data = responseSix.data.data
                this.secondLocationChangeByTimeHousePriceArr = responseSix.data.data
              }
              if (responseSeven && responseSeven.data) {
                this.firstLocationChangeByTimeApartmentPriceArr = responseSeven.data.data
              }
              if (responseEight && responseEight.data) {
                this.secondLocationChangeByTimeApartmentPriceArr = responseEight.data.data
              }
              // rename of the series
              this.setNameForChartDatas(tempBarChartData, tempAreaChartDatas)
              // set data for barchart -> for reactive of vue
              this.barChartDatas = tempBarChartData
              // set data for areachart -> for reactive of vue
              this.areaChartDatas = tempAreaChartDatas
              this.expand = true
            }),
          )
          .catch(errors => {})
      } else {
        this.snackbar = true
      }
    },
    setFirstLocation(type, value) {
      this.firstSelectedLocationObject[type + 'Id'] = value ? value.id : null
      this.firstSelectedLocationObject[type + 'Name'] = value ? value.name : null
    },
    setSecondLocation(type, value) {
      this.secondSelectedLocationObject[type + 'Id'] = value ? value.id : null
      this.secondSelectedLocationObject[type + 'Name'] = value ? value.name : null
    },
    buildLocationString(locationObject) {
      let returnString = ''
      if (locationObject.wardName) {
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
    setNameForChartDatas(tempBarChart, tempAreaChart) {
      
        tempBarChart[0].name = this.firstSelectedLocationString
        tempBarChart[1].name = this.secondSelectedLocationString
  
        tempAreaChart[0].name = this.firstSelectedLocationString
        tempAreaChart[1].name = this.secondSelectedLocationString
      
    },
    checkLocationValid() {
      if (
        this.firstSelectedLocationObject.districtId == this.secondSelectedLocationObject.districtId &&
        this.firstSelectedLocationObject.wardId == this.secondSelectedLocationObject.wardId
      ) {
        return false
      }
      return true
    },
    buildFirstObjectUlr(controller, type = null) {
      let baseUrl = this.baseUrl + '/post/' + controller + '?'
      if (this.firstSelectedLocationObject.wardId) {
        baseUrl += 'wardId=' + this.firstSelectedLocationObject.wardId + '&'
      }
      if (this.firstSelectedLocationObject.districtId) {
        baseUrl += 'districtId=' + this.firstSelectedLocationObject.districtId + '&'
      }
      if (this.firstSelectedLocationObject.cityId) {
        baseUrl += 'cityId=' + this.firstSelectedLocationObject.cityId
      }
      if (type != null) {
        baseUrl += type
      }
      return baseUrl
    },
    buildSecondObjectUrl(controller, type = null) {
      let baseUrl = 'http://20.89.155.32/api/post/' + controller + '?'
      if (this.secondSelectedLocationObject.wardId) {
        baseUrl += 'wardId=' + this.secondSelectedLocationObject.wardId + '&'
      }
      if (this.secondSelectedLocationObject.districtId) {
        baseUrl += 'districtId=' + this.secondSelectedLocationObject.districtId + '&'
      }
      if (this.secondSelectedLocationObject.cityId) {
        baseUrl += 'cityId=' + this.secondSelectedLocationObject.cityId
      }
      if (type != null) {
        baseUrl += type
      }
      return baseUrl
    },
  },
  watch: {
    viewBy: {
      immediate: true,
      handler(value) {
        this.createChartDatas(value)
      },
    },
    darkTheme :{
      immediate: false,
      handler(value){
        console.log(value);
      }
    }
  },
}
</script>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.5s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0.05;
}
</style>