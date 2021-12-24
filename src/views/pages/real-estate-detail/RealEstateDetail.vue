<template>
  <v-row>
    <v-col cols="12" md="12">
      

          <span>Bạn đang xem khu vực Phố Huế, Hai Bà Trưng, Hà Nội</span>
          <v-spacer></v-spacer>


    </v-col>
    <v-slide-y-transition mode="out-in">
    <v-col cols="12" md="12">
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

      </v-slide-y-transition>
    <v-col cols="12" md="6">
      <v-card>
        <v-card-title>
          <span class="me-3">Các dự án tại khu vực này</span>
        </v-card-title>
        <v-card-text>
          <v-list>
            <v-list-item
              v-for="(data, index) in listProjects"
              :key="data.id"
              :class="`d-flex px-0 ${index > 0 ? 'mt-4' : ''}`"
            >
             
              <div class="d-flex align-center flex-grow-1 flex-wrap">
                <div class="me-auto pe-2">
                  <h4 class="font-weight-semibold">
                    {{ data.title }}
                  </h4>
                  <span class="text-xs">{{ data.address }}</span>
                </div>
              </div>
            </v-list-item>
          </v-list>
        </v-card-text>
      </v-card>
    </v-col>
    <v-col cols="12" md="6"
    >
    <v-card>
        <v-card-title>
          <span class="me-3">Tin rao bán hot tại đây</span>
        </v-card-title>
        <v-card-text>
          <v-list>
            <v-list-item
              v-for="(data, index) in listPosts"
              :key="data.id"
              :class="`d-flex px-0 ${index > 0 ? 'mt-4' : ''}`"
            >
              <div class="d-flex align-center flex-grow-1 flex-wrap">
                <div class="me-auto pe-2">
                  <h4 class="font-weight-semibold">
                    {{ data.title }}
                  </h4>
                  <span class="text-xs">{{ data.areaValue }} - {{ data.textPrice }}. {{data.metaDescription}} </span>
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
        categories: ['HOUSE', 'APARTMENT'],
      },
    }

    const chartData = [
      {
        data: [378520018.4574349,38290441.176470585],
      },
    ]

    return {
      chartOptions,
      chartData,
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
      listProjects:[
    {
      "id": "5e550205eb80a7245176ae53",
      "title": "10 Hoa Lư",
      "address": "Số 10 Hoa Lư, Phường Lê Đại Hành, Hai Bà Trưng, Hà Nội",
      "longitude": "105.8473318",
      "latitude": "21.0096123",
      "wardId": "5e5501cbeb80a7245175e139",
      "districtId": "5e5501caeb80a7245175de2d",
      "cityId": "5e5501caeb80a7245175dddb",
      "city": {
        "id": "5e5501caeb80a7245175dddb",
        "title": "Thành phố Hà Nội",
        "name": "Hà Nội"
      },
      "district": {
        "id": "5e5501caeb80a7245175de2d",
        "title": "Quận Hai Bà Trưng",
        "name": "Hai Bà Trưng"
      },
      "ward": {
        "id": "5e5501cbeb80a7245175e139",
        "title": "Phường Lê Đại Hành",
        "name": "Lê Đại Hành"
      }
    },
    {
      "id": "5e550205eb80a7245176ae54",
      "title": "141 Trương Định",
      "address": "Số 141 Trương Định, Phường Trương Định, Hai Bà Trưng, Hà Nội",
      "longitude": "105.8493026",
      "latitude": "20.991303",
      "wardId": null,
      "districtId": "5e5501caeb80a7245175de2d",
      "cityId": "5e5501caeb80a7245175dddb",
      "city": {
        "id": "5e5501caeb80a7245175dddb",
        "title": "Thành phố Hà Nội",
        "name": "Hà Nội"
      },
      "district": {
        "id": "5e5501caeb80a7245175de2d",
        "title": "Quận Hai Bà Trưng",
        "name": "Hai Bà Trưng"
      },
      "ward": null
    },
    {
      "id": "5e550205eb80a7245176ae55",
      "title": "Chung cư 229 Phố Vọng",
      "address": "Số 229 Phố Vọng, Phường Đồng Tâm, Hai Bà Trưng, Hà Nội",
      "longitude": "105.842917",
      "latitude": "20.99599",
      "wardId": "5e5501cbeb80a7245175e141",
      "districtId": "5e5501caeb80a7245175de2d",
      "cityId": "5e5501caeb80a7245175dddb",
      "city": {
        "id": "5e5501caeb80a7245175dddb",
        "title": "Thành phố Hà Nội",
        "name": "Hà Nội"
      },
      "district": {
        "id": "5e5501caeb80a7245175de2d",
        "title": "Quận Hai Bà Trưng",
        "name": "Hai Bà Trưng"
      },
      "ward": {
        "id": "5e5501cbeb80a7245175e141",
        "title": "Phường Đồng Tâm",
        "name": "Đồng Tâm"
      }
    },
    {
      "id": "5e550205eb80a7245176ae56",
      "title": "25 Lạc Trung",
      "address": "Phố Lạc Trung, Phường Vĩnh Tuy, Hai Bà Trưng, Hà Nội",
      "longitude": "105.8649652",
      "latitude": "21.0022545",
      "wardId": null,
      "districtId": "5e5501caeb80a7245175de2d",
      "cityId": "5e5501caeb80a7245175dddb",
      "city": {
        "id": "5e5501caeb80a7245175dddb",
        "title": "Thành phố Hà Nội",
        "name": "Hà Nội"
      },
      "district": {
        "id": "5e5501caeb80a7245175de2d",
        "title": "Quận Hai Bà Trưng",
        "name": "Hai Bà Trưng"
      },
      "ward": null
    },
    {
      "id": "5e550205eb80a7245176ae57",
      "title": "310 Minh Khai",
      "address": "Đường Tam Trinh, Phường Minh Khai, Hai Bà Trưng, Hà Nội",
      "longitude": "105.861181",
      "latitude": "20.995837",
      "wardId": "5e5501cbeb80a7245175e146",
      "districtId": "5e5501caeb80a7245175de2d",
      "cityId": "5e5501caeb80a7245175dddb",
      "city": {
        "id": "5e5501caeb80a7245175dddb",
        "title": "Thành phố Hà Nội",
        "name": "Hà Nội"
      },
      "district": {
        "id": "5e5501caeb80a7245175de2d",
        "title": "Quận Hai Bà Trưng",
        "name": "Hai Bà Trưng"
      },
      "ward": {
        "id": "5e5501cbeb80a7245175e146",
        "title": "Phường Minh Khai",
        "name": "Minh Khai"
      }
    },
    {
      "id": "5e550205eb80a7245176ae58",
      "title": "93 Lò Đúc - Kinh Đô Tower",
      "address": "Số 93 Lò Đúc, Phường Phạm Đình Hổ, Hai Bà Trưng, Hà Nội",
      "longitude": "105.8570133",
      "latitude": "21.015279",
      "wardId": "5e5501cbeb80a7245175e136",
      "districtId": "5e5501caeb80a7245175de2d",
      "cityId": "5e5501caeb80a7245175dddb",
      "city": {
        "id": "5e5501caeb80a7245175dddb",
        "title": "Thành phố Hà Nội",
        "name": "Hà Nội"
      },
      "district": {
        "id": "5e5501caeb80a7245175de2d",
        "title": "Quận Hai Bà Trưng",
        "name": "Hai Bà Trưng"
      },
      "ward": {
        "id": "5e5501cbeb80a7245175e136",
        "title": "Phường Phạm Đình Hổ",
        "name": "Phạm Đình Hổ"
      }
    },
    {
      "id": "5e550205eb80a7245176ae59",
      "title": "CDC Building",
      "address": "Số 25 - 27 Lê Đại Hành, Phường Lê Đại Hành, Hai Bà Trưng, Hà Nội",
      "longitude": "105.8480912",
      "latitude": "21.0090858",
      "wardId": null,
      "districtId": "5e5501caeb80a7245175de2d",
      "cityId": "5e5501caeb80a7245175dddb",
      "city": {
        "id": "5e5501caeb80a7245175dddb",
        "title": "Thành phố Hà Nội",
        "name": "Hà Nội"
      },
      "district": {
        "id": "5e5501caeb80a7245175de2d",
        "title": "Quận Hai Bà Trưng",
        "name": "Hai Bà Trưng"
      },
      "ward": null
    },
    {
      "id": "5e550205eb80a7245176ae5a",
      "title": "Chung cư 122 Vĩnh Tuy",
      "address": "Số 122 đường Vĩnh Tuy, Phường Vĩnh Tuy, Hai Bà Trưng, Hà Nội",
      "longitude": "105.8754981",
      "latitude": "21.0012375",
      "wardId": null,
      "districtId": "5e5501caeb80a7245175de2d",
      "cityId": "5e5501caeb80a7245175dddb",
      "city": {
        "id": "5e5501caeb80a7245175dddb",
        "title": "Thành phố Hà Nội",
        "name": "Hà Nội"
      },
      "district": {
        "id": "5e5501caeb80a7245175de2d",
        "title": "Quận Hai Bà Trưng",
        "name": "Hai Bà Trưng"
      },
      "ward": null
    },
    {
      "id": "5e550205eb80a7245176ae5b",
      "title": "Coma Building",
      "address": "Số 125D Minh Khai, Phường Minh Khai, Hai Bà Trưng, Hà Nội",
      "longitude": "105.8545311",
      "latitude": "20.9963336",
      "wardId": "5e5501cbeb80a7245175e146",
      "districtId": "5e5501caeb80a7245175de2d",
      "cityId": "5e5501caeb80a7245175dddb",
      "city": {
        "id": "5e5501caeb80a7245175dddb",
        "title": "Thành phố Hà Nội",
        "name": "Hà Nội"
      },
      "district": {
        "id": "5e5501caeb80a7245175de2d",
        "title": "Quận Hai Bà Trưng",
        "name": "Hai Bà Trưng"
      },
      "ward": {
        "id": "5e5501cbeb80a7245175e146",
        "title": "Phường Minh Khai",
        "name": "Minh Khai"
      }
    },
    {
      "id": "5e550205eb80a7245176ae5c",
      "title": "Crystal Tower",
      "address": "Phố Mai Hắc Đế, Phường Lê Đại Hành, Hai Bà Trưng, Hà Nội",
      "longitude": null,
      "latitude": null,
      "wardId": null,
      "districtId": "5e5501caeb80a7245175de2d",
      "cityId": "5e5501caeb80a7245175dddb",
      "city": {
        "id": "5e5501caeb80a7245175dddb",
        "title": "Thành phố Hà Nội",
        "name": "Hà Nội"
      },
      "district": {
        "id": "5e5501caeb80a7245175de2d",
        "title": "Quận Hai Bà Trưng",
        "name": "Hai Bà Trưng"
      },
      "ward": null
    }
  ],
      listPosts :[
    {
      "id": "5f78af9ec6a55a10608c7367",
      "title": "Vị trí hấp dẫn Quận Hai Bà Trưng, Hà Nội, bán nhà dt 133 m2 liên hệ trực tiếp để được tư vấn",
      "metaDescription": "Xem nhanh tin rao hay nhất về Mua/ Bán Nhà Đất tại Quận Hai Bà Trưng Thành phố Hà Nội Vị trí hấp dẫn Quận Hai Bà Trưng, Hà Nội, bán nhà dt 133 m2 liên hệ trực tiếp để được tư vấn",
      "longitude": "105.8498873",
      "latitude": "21.0134181",
      "type": 1,
      "projectId": null,
      "streetId": "5e958eea208d0d6d7648fdd9",
      "wardId": "5e5501cbeb80a7245175e13b",
      "districtId": "5e5501caeb80a7245175de2d",
      "cityId": "5e5501caeb80a7245175dddb",
      "createdDate": "2020-12-06T19:10:19.000Z",
      "publishedDate": "2020-10-03T17:06:38.000Z",
      "priceTotal": null,
      "areaValue": 133,
      "textPrice": "Thương lượng",
      "deletedAt": null
    },
    {
      "id": "5f7ae774c6a55a01c4746154",
      "title": "Ở Đường Đê Trần Khát Chân, Quận Hai Bà Trưng, bán nhà, bán ngay với giá tốt bất ngờ 3.5 tỷ có dt 45 m2 ,căn này bao gồm 3 PN hỗ trợ mọi thủ tục miễn phí",
      "metaDescription": "Tìm hiểu ngay tin rao mới nhất về Mua/ Bán Nhà Đất tại Quận Hai Bà Trưng Thành phố Hà Nội Ở Đường Đê Trần Khát Chân, Quận Hai Bà Trưng, bán nhà, bán ngay với giá tốt bất ngờ 3.5 tỷ có dt 45 m2 ,căn này bao gồm 3 PN hỗ trợ mọi thủ tục miễn phí",
      "longitude": "105.8656789",
      "latitude": "21.0069151",
      "type": 1,
      "projectId": null,
      "streetId": "5e958eea208d0d6d7648fde3",
      "wardId": "5e5501cbeb80a7245175e13b",
      "districtId": "5e5501caeb80a7245175de2d",
      "cityId": "5e5501caeb80a7245175dddb",
      "createdDate": "2020-12-31T15:25:19.000Z",
      "publishedDate": "2020-10-05T09:29:24.000Z",
      "priceTotal": 3500100000,
      "areaValue": 45,
      "textPrice": "3.5 Tỷ - 77.78 Triệu/m²",
      "deletedAt": null
    },
    {
      "id": "5f7b0099c6a55a01c4746c49",
      "title": "Nằm tại Phường Phố Huế, Hà Nội, bán nhà, vào ở ngay giá phải chăng chỉ 3.5 tỷ có dt chung là 50 m2 ,nhà này có tổng 3 phòng ngủ liên hệ chính chủ",
      "metaDescription": "Xem đầy đủ loạt tin đăng giá trị về Mua/ Bán Nhà Đất tại Quận Hai Bà Trưng Thành phố Hà Nội Nằm tại Phường Phố Huế, Hà Nội, bán nhà, vào ở ngay giá phải chăng chỉ 3.5 tỷ có dt chung là 50 m2 ,nhà này có tổng 3 phòng ngủ liên hệ chính chủ",
      "longitude": "105.8656789",
      "latitude": "21.0069151",
      "type": 1,
      "projectId": null,
      "streetId": "5e958eea208d0d6d7648fde3",
      "wardId": "5e5501cbeb80a7245175e13b",
      "districtId": "5e5501caeb80a7245175de2d",
      "cityId": "5e5501caeb80a7245175dddb",
      "createdDate": "2020-12-04T15:30:19.000Z",
      "publishedDate": "2020-10-05T11:16:41.000Z",
      "priceTotal": 3500000000,
      "areaValue": 50,
      "textPrice": "3.5 Tỷ - 70 Triệu/m²",
      "deletedAt": null
    },
    {
      "id": "5f7b9ed6c6a55a0f649e95dc",
      "title": "Bán nhà diện tích rộng 42 m2 vị trí đặt tọa lạc trên Phường Phố Huế, Hà Nội vào ở luôn giá cực mềm chỉ 3.6 tỷ ,tổng quan nhà này gồm 3 PN ,đường đi 3 m",
      "metaDescription": "Xem ngay thông tin đỉnh cao nhất về Mua/ Bán Nhà Đất tại Quận Hai Bà Trưng Thành phố Hà Nội Bán nhà diện tích rộng 42 m2 vị trí đặt tọa lạc trên Phường Phố Huế, Hà Nội vào ở luôn giá cực mềm chỉ 3.6 tỷ ,tổng quan nhà này gồm 3 PN ,đường đi 3 m",
      "longitude": "105.8538723",
      "latitude": "21.0087159",
      "type": 1,
      "projectId": null,
      "streetId": "5e958eea208d0d6d7648fe1f",
      "wardId": "5e5501cbeb80a7245175e13b",
      "districtId": "5e5501caeb80a7245175de2d",
      "cityId": "5e5501caeb80a7245175dddb",
      "createdDate": "2020-12-14T02:05:01.000Z",
      "publishedDate": "2020-10-05T22:31:50.000Z",
      "priceTotal": 3599820000,
      "areaValue": 42,
      "textPrice": "3.6 Tỷ - 85.71 Triệu/m²",
      "deletedAt": null
    },
    {
      "id": "5f7c7327c6a55a167cbf1d08",
      "title": "Tổng quan ngôi nhà này 4 PN, bán nhà giá cực tốt từ 35 tỷ dt 140 m2 vị trí mặt tiền tọa lạc ở Phố Phố Huế, Quận Hai Bà Trưng",
      "metaDescription": "HOT!!! Xem ngay Mua/ Bán Nhà Đất tại Quận Hai Bà Trưng Thành phố Hà Nội Tổng quan ngôi nhà này 4 PN, bán nhà giá cực tốt từ 35 tỷ dt 140 m2 vị trí mặt tiền tọa lạc ở Phố Phố Huế, Quận Hai Bà Trưng",
      "longitude": "105.8533481",
      "latitude": "21.0115168",
      "type": 1,
      "projectId": null,
      "streetId": "5e958eea208d0d6d7648fe4d",
      "wardId": "5e5501cbeb80a7245175e13b",
      "districtId": "5e5501caeb80a7245175de2d",
      "cityId": "5e5501caeb80a7245175dddb",
      "createdDate": "2020-12-06T20:15:41.000Z",
      "publishedDate": "2020-10-06T13:37:43.000Z",
      "priceTotal": 35000000000,
      "areaValue": 140,
      "textPrice": "35 Tỷ - 250 Triệu/m²",
      "deletedAt": null
    }
  ]
,
    }
  },
  methods: {
  
  },
  watch: {
  },
}
</script>
