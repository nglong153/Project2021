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
    const customChartColor = $vuetify.theme.isDark ? '#3b3559' : '#f5f5f5'
    const baseUrl = 'http://20.89.155.32:3000/api'
    const chartOptions = {
      colors: [$vuetify.theme.currentTheme.primary, '#3b3559'],
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

    return {
      chartOptions,
      baseUrl,
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
        wardName: 'Hai Bà Trưng',
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
      areaChartOptions: {},
      viewBy: 'month',
      expand: false,
      loading: false,
      snackbar: false,
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
    compare() {
      if (this.checkLocationValid()) {
        this.loading = true;
        this.expand = false;
        this.firstSelectedLocationString = this.buildLocationString(this.firstSelectedLocationObject)
        this.secondSelectedLocationString = this.buildLocationString(this.secondSelectedLocationObject)
        this.setNameForChartDatas()
        let firstLocationHouse =
          'http://20.89.155.32:3000/api/post/average-price?wardId=' +
          this.firstSelectedLocationObject.wardId +
          '&districtId=' +
          this.firstSelectedLocationObject.districtId +
          '&cityId=' +
          this.firstSelectedLocationObject.cityId +
          '&type=HOUSE'
        let firstLocationApartment =
          'http://20.89.155.32:3000/api/post/average-price?wardId=' +
          this.firstSelectedLocationObject.wardId +
          '&districtId=' +
          this.firstSelectedLocationObject.districtId +
          '&cityId=' +
          this.firstSelectedLocationObject.cityId +
          '&type=APARTMENT'
        let secondLocationHouse =
          'http://20.89.155.32:3000/api/post/average-price?wardId=' +
          this.secondSelectedLocationObject.wardId +
          '&districtId=' +
          this.secondSelectedLocationObject.districtId +
          '&cityId=' +
          this.secondSelectedLocationObject.cityId +
          '&type=HOUSE'
        let secondLocationApartment =
          'http://20.89.155.32:3000/api/post/average-price?wardId=' +
          this.secondSelectedLocationObject.wardId +
          '&districtId=' +
          this.secondSelectedLocationObject.districtId +
          '&cityId=' +
          this.secondSelectedLocationObject.cityId +
          '&type=APARTMENT'

        let firstLocationChangeByTime =
          'http://20.89.155.32:3000/api/post/average-price-in-year?districtId=' +
          this.firstSelectedLocationObject.districtId +
          '&cityId=' +
          this.firstSelectedLocationObject.cityId +
          '&year=2021&' +
          this.firstSelectedLocationObject.wardId
        let secondLocationChangeByTime =
          'http://20.89.155.32:3000/api/post/average-price-in-year?districtId=' +
          this.secondSelectedLocationObject.districtId +
          '&cityId=' +
          this.secondSelectedLocationObject.cityId +
          '&year=2021&' +
          this.secondSelectedLocationObject.wardId

        const requestOne = axios.get(firstLocationHouse)
        const requestTwo = axios.get(firstLocationApartment)
        const requestThree = axios.get(secondLocationHouse)
        const requestFour = axios.get(secondLocationApartment)
        const requestFive = axios.get(firstLocationChangeByTime)
        const requestSix = axios.get(secondLocationChangeByTime)
        console.log(this.areaChartDatas);
        axios
          .all([requestOne, requestTwo, requestThree, requestFour,requestFive,requestSix])
          .then(
            axios.spread((...responses) => {
              this.loading = false
              const responseOne = responses[0]
              const responseTwo = responses[1]
              const responseThree = responses[2]
              const responseFour = responses[3]
              const responseFive = responses[4];
              const responseSix = responses[5];
              console.log(responseSix.data.data);
              if (responseOne && responseOne.data) {
                this.barChartDatas[0].data[0] = responseOne.data.data.averagePrice
              }
              if (responseTwo && responseTwo.data) {
                this.barChartDatas[0].data[1] = responseTwo.data.data.averagePrice
              }
              if (responseThree && responseThree.data) {
                this.barChartDatas[1].data[0] = responseThree.data.data.averagePrice
              }
              if (responseFour && responseFour.data) {
                this.barChartDatas[1].data[1] = responseFour.data.data.averagePrice
              }
              if (responseFive && responseFive.data) {
                this.areaChartDatas[0].data = responseFive.data.data
              }
              if (responseSix && responseSix.data) {
                this.areaChartDatas[1].data = responseSix.data.data
              }

              this.expand = true
            }),
          )
          .catch(errors => {})
      } else {
        this.snackbar = true
      }
    },
    setFirstLocation(type, value) {
      this.firstSelectedLocationObject[type + 'Id'] = value.id ? value.id : null
      this.firstSelectedLocationObject[type + 'Name'] = value.name ? value.name : null
    },
    setSecondLocation(type, value) {
      this.secondSelectedLocationObject[type + 'Id'] = value.id ? value.id : null
      this.secondSelectedLocationObject[type + 'Name'] = value.name ? value.name : null
    },
    buildLocationString(locationObject) {
      return locationObject.wardName + ', ' + locationObject.districtName + ', ' + locationObject.cityName
    },
    setNameForChartDatas() {
      this.barChartDatas[0].name = this.firstSelectedLocationString
      this.barChartDatas[1].name = this.secondSelectedLocationString
      this.areaChartDatas[0].name = this.firstSelectedLocationString
      this.areaChartDatas[1].name = this.secondSelectedLocationString
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