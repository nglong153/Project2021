<template>
  <v-card>
    <v-card-title>City {{cityId}}</v-card-title>
    <v-card-actions>
      <v-col cols="4" class="mb-6">
        <v-combobox @blur="chooseCity" v-model="selectedCity" :items="cities" label="city"> </v-combobox>
      </v-col>
      <v-col cols="4" class="mb-6">
        <v-combobox :items="districts" v-model="selectedDistrict" label="District"> </v-combobox>
      </v-col>
      <v-col cols="4" class="mb-6">
        <v-combobox :items="wards" v-model="selectedWard"  label="Ward"> </v-combobox>
      </v-col>
    </v-card-actions>
  </v-card>
</template>

<script>
export default {
  components: {},
  data() {
    return {
      selectedCity : 'Hanoi',
      selectedDistrict :'Quan Hoang Mai',
      selectedWard:'Yen So',
      cities: ['Hanoi', 'HCM', 'Da Nang', 'Bac Giang'],
      districts: ['Quan Hoang Mai', 'Quan Hoan Kiem', 'Quan Cau Giay'],
      wards: ['Yen So', 'Tran Phu', 'Linh Nam', 'Hai Ba Trung'],
    }
  },
  props:{
      cityId  :{
          type : Number,
          required:true
      }
  },
  methods:{
      chooseCity:function(){
         this.$emit('choosed','city',this.cityId,this.selectedCity)
      },
  }
}
</script>
