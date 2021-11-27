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
      firstSelectedLocation : "Yen So, Hoang Mai, Hanoi",
      secondSelectedLocation : "Tran Phu, Quan Hoang Kiem, Hanoi",
      series: [
        {
          data: [44, 55, 41, 64, 22, 43, 21],
        },
        {
          data: [53, 32, 33, 52, 13, 44, 32],
        },
      ],
    }
  },
  methods:{
      compare(typeOfSelect,cityId){

      },
  computed: {
    compareString: function () {
      return this.firstSelectedLocation + ' vs ' + this.secondSelectedLocation
    },
  },
  computed:{
      compareString : function(){
          return this.firstSelectedLocation + " vs " + this.secondSelectedLocation
      }
  }
}
}
</script>