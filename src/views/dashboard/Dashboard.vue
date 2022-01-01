<template>
  <v-row>
    <v-col cols="12" md="12">
      <choose-location-card :hidden="false"></choose-location-card>
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
  </v-row>
</template>

<script>
// eslint-disable-next-line object-curly-newline
import { mdiPoll, mdiLabelVariantOutline, mdiCurrencyUsd, mdiHelpCircleOutline } from '@mdi/js'
import StatisticsCardVertical from '@/components/statistics-card/StatisticsCardVertical.vue'
import ChooseLocationCard from '@/components/choose-location-card/ChooseLocationCard.vue'
import axios from 'axios'
import VueApexCharts from 'vue-apexcharts'
import { getCurrentInstance } from '@vue/composition-api'

// demos

export default {
  components: {
    StatisticsCardVertical,
    ChooseLocationCard,
  },
  setup() {
    const totalProfit = {
      statTitle: 'Total Profit',
      icon: mdiPoll,
      color: 'success',
      subtitle: 'Weekly Project',
      statistics: '$25.6k',
      change: '+42%',
    }

    const totalSales = {
      statTitle: 'Refunds',
      icon: mdiCurrencyUsd,
      color: 'secondary',
      subtitle: 'Past Month',
      statistics: '$78',
      change: '-15%',
    }

    // vertical card options
    const newProject = {
      statTitle: 'New Project',
      icon: mdiLabelVariantOutline,
      color: 'primary',
      subtitle: 'Yearly Project',
      statistics: '862',
      change: '-18%',
    }

    const salesQueries = {
      statTitle: 'Sales Quries',
      icon: mdiHelpCircleOutline,
      color: 'warning',
      subtitle: 'Last week',
      statistics: '15',
      change: '-18%',
    }

    return {
      totalProfit,
      totalSales,
      newProject,
      salesQueries,
    }
  },
  data: function () {
    return {
      areaChartDatas: [],
      areaChartOptions: {},
      viewBy: 'month',
      showDetail: false,
      selectedLocationObject: {
        cityId: '5e5501caeb80a7245175dddb',
        districtId: '5e5501caeb80a7245175de2d',
        wardId: '5e5501cbeb80a7245175e13b',
        cityName: 'Hà Nội',
        districtName: 'Hai Bà Trưng',
        wardName: 'Phố Huế',
      },
      locationString: '',
      loading:false
    }
  },
  methods:{
    setLocation(type, value) {
      this.selectedLocationObject[type + 'Id'] = value ? value.id : null
      this.selectedLocationObject[type + 'Name'] = value ? value.name : null
    },
    buildLocationString(locationObject) {
      let returnString = ''
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

      this.loading = true
      this.showDetail = false
      this.locationString = this.buildLocationString(this.selectedLocationObject)

      let locationHouse = this.buildUlr('/post/average-price', '&type=HOUSE')
      let locationApartment = this.buildUlr('/post/average-price', '&type=APARTMENT')
      let listTempProjects = this.buildUlr('/project/list-project');
      let listTempPosts = this.buildUlr('/post/list','&page=0&limit=50')

      const requestOne = axios.get(locationHouse)
      const requestTwo = axios.get(locationApartment)
      // const requestThree = axios.get(secondLocationHouse)
      // const requestFour = axios.get(secondLocationApartment)
      const requestThree = axios.get(listTempProjects)
      const requestFour = axios.get(listTempPosts)

      axios
        .all([requestOne, requestTwo , requestThree, requestFour])
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
              this.listProjects = responseThree.data.data
            }
            if (responseFour && responseFour.data) {
              this.listPosts = responseFour.data.data
            }

      
            // rename of the series
            this.setNameForChartDatas(tempBarChartData)
            // set data for barchart -> for reactive of vue
            this.chartData = tempBarChartData
            this.showDetail = true
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
      return baseUrl
    },
    setNameForChartDatas(tempBarChart) {
        tempBarChart[0].name = this.locationString;
    },
  },
}
</script>
