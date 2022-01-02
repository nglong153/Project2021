<template>
  <v-card>
    <v-card-title>Khu vực {{ displayLocationText }}</v-card-title>
    <v-card-actions>
      <v-col :cols="detailView ? 6 : 4" class="mb-6">
        <v-combobox :return-object="false" v-model="selectedCity" :items="cities" label="Tỉnh/Thành phố"> </v-combobox>
      </v-col>
      <v-col :cols="detailView ? 6 : 4" class="mb-6">
        <v-combobox
          :items="districts"
          :auto-select-first="true"
          :ref="'districtCombobox' + cityId"
          @input="handleChooseDistrict"
          v-model="selectedDistrictObject"
          label="Quận/Huyện"
        >
        </v-combobox>
      </v-col>
      <v-col :cols="detailView ? 6 : 4" class="mb-6" v-if="!detailView">
        <v-combobox
          :auto-select-first="true"
          :items="wards"
          :ref="'wardCombobox' + cityId"
          v-model="selectedWardObject"
          label="Phường/Xã"
          @input="handleChooseWard"
        >
        </v-combobox>
      </v-col>
    </v-card-actions>
    <v-card-actions v-if="!hidden">
      <v-col cols="4" class="mb-6">
        <v-range-slider
          v-model="priceRange"
          label="Khoảng giá"
          step="0.1"
          min="0"
          max="100"
          :thumb-label="true"
          hint="tỷ"
          @input="handleChoosePriceRange"
        ></v-range-slider>
      </v-col>
      <v-col cols="4" class="mb-6">
        <v-range-slider
          v-model="areaRange"
          label="Khoảng diện tích"
          step="1"
          min="10"
          max="500"
          :thumb-label="true"
          hint="m2"
          @input="handleChooseAreaRange"
        ></v-range-slider>
      </v-col>
    </v-card-actions>
  </v-card>
</template>

<script>
import axios from 'axios'

export default {
  components: {},
  data() {
    return {
      selectedCity: {
        id: '5e5501caeb80a7245175dddb',
        title: 'Thành phố Hà Nội',
        englishName: 'Ha Noi',
        name: 'Hà Nội',
        longitude: '105.8341598',
        latitude: '21.0277644',
        text: 'Hà Nội',
      },
      selectedDistrictObject: {
        id: '5e5501caeb80a7245175de2d',
        title: 'Quận Hai Bà Trưng',
        name: 'Hai Bà Trưng',
        englishName: 'Hai Ba Trung',
        longitude: '105.8607507',
        latitude: '21.0090571',
        cityId: '5e5501caeb80a7245175dddb',
        city: {
          id: '5e5501caeb80a7245175dddb',
          title: 'Thành phố Hà Nội',
          name: 'Hà Nội',
        },
        text: 'Hai Bà Trưng',
        value: '5e5501caeb80a7245175de2d',
      },
      selectedWardObject: {
        id: '5e5501cbeb80a7245175e13b',
        title: 'Phường Phố Huế',
        name: 'Phố Huế',
        englishName: 'Pho Hue',
        longitude: '105.5474373',
        latitude: '21.3608805',
        districtId: '5e5501caeb80a7245175de2d',
        district: {
          id: '5e5501caeb80a7245175de2d',
          title: 'Quận Hai Bà Trưng',
          name: 'Hai Bà Trưng',
          city: {
            id: '5e5501caeb80a7245175dddb',
            title: 'Thành phố Hà Nội',
            name: 'Hà Nội',
          },
        },
        text: 'Phố Huế',
        value: '5e5501cbeb80a7245175e13b',
      },
      cities: [
        {
          id: '5e5501caeb80a7245175dddb',
          title: 'Thành phố Hà Nội',
          englishName: 'Ha Noi',
          name: 'Hà Nội',
          longitude: '105.8341598',
          latitude: '21.0277644',
          text: 'Hà Nội',
        },
      ],
      districts: [],
      wards: [],
      priceRange: [0, 10],
      areaRange: [0, 50],
    }
  },
  props: {
    cityId: {
      type: Number,
      required: false,
    },
    hidden: {
      type: Boolean,
      required: false,
      default: true,
    },
    detailView: {
      type: Boolean,
      required: false,
      default: false,
    },
  },
  methods: {
    chooseCity: function () {
      this.$emit('choosed', 'city', this.selectedCity)
    },
    getListDistrict: function () {
      VueAxios.get('http://20.89.155.32/api/address/list-district?idTinhThanhPho=hn').then(response =>
        console.log(response),
      )
    },
    handleChooseDistrict: function () {
      this.$emit('choosed', 'district', this.selectedDistrictObject)

      this.selectedWardObject = null
      this.$emit('choosed', 'ward', this.selectedWardObject)
      if (this.selectedDistrictObject.id) {
        let districtId = this.selectedDistrictObject.id
        axios
          .get('http://20.89.155.32/api/address/list-ward?cityId=5e5501caeb80a7245175dddb&districtId=' + districtId)
          .then(result => {
            let tempArr = result.data.data
            tempArr.forEach(element => {
              element.text = element.name
              element.value = element.id
            })
            this.wards = tempArr
            if (this.cityId) {
              let wardCombobox = 'wardCombobox' + this.cityId
              this.$refs[wardCombobox].focus()
            }
          })
      }
    },
    handleChooseWard: function () {
      this.$emit('choosed', 'ward', this.selectedWardObject)
    },
    handleChoosePriceRange :function(){
        this.$emit('choosePriceRange', this.priceRange)
    },
    handleChooseAreaRange :function(){
      this.$emit('chooseAreaRange', this.areaRange)
    },
    parseResponse(data) {
      let tempArr = data
      tempArr.forEach(element => {
        element.text = element.name
        element.value = element.id
      })
      return tempArr
    },
  },
  created() {
    let one = 'http://20.89.155.32/api/address/list-district?cityId=5e5501caeb80a7245175dddb'
    let two =
      'http://20.89.155.32/api/address/list-ward?cityId=5e5501caeb80a7245175dddb&districtId=5e5501caeb80a7245175de2d'
    const requestOne = axios.get(one)
    const requestTwo = axios.get(two)

    axios
      .all([requestOne, requestTwo])
      .then(
        axios.spread((...responses) => {
          const responseOne = responses[0]
          const responseTwo = responses[1]
          if (responseOne && responseOne.data) {
            let tempDistrictArr = this.parseResponse(responseOne.data.data)
            this.districts = tempDistrictArr
          }
          if (responseTwo && responseTwo.data) {
            let tempDistrictArr = this.parseResponse(responseTwo.data.data)
            this.wards = tempDistrictArr
          }
        }),
      )
      .catch(errors => {})
  },
  computed: {
    displayLocationText: function () {
      if (this.cityId == 1) {
        return 'thứ nhất'
      } else if (this.cityId == 2) {
        return 'thứ hai'
      } else return 'muốn xem'
    },
  },
}
</script>
