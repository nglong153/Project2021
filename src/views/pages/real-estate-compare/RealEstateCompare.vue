<template>
  <v-container>
    <v-row>
      <v-col>
        <choose-location-card @choosed ="compare" :cityId="1"></choose-location-card>
      </v-col>
      <v-col>
        <choose-location-card :cityId="2"></choose-location-card>
      </v-col>
    </v-row>
    <v-row>
      <v-col>
        <v-btn color="primary" >Compare </v-btn>
      </v-col>
    </v-row>
    <v-row>
        <div>
            <span>{{compareString}}</span>
        </div>
         <v-progress-linear value="500000"></v-progress-linear>
    </v-row>
  </v-container>
</template>

<script>
import ChooseLocationCard from '@/components/choose-location-card/ChooseLocationCard.vue'

export default {
  components: {
    ChooseLocationCard,
  },
  data() {
    return {
      select: ['Hanoi', 'HCM'],
      cities: ['Hanoi', 'HCM', 'Da Nang', 'Bac Giang'],
      districts: ['Quan Hoang Mai', 'Quan Hoan Kiem', 'Quan Cau Giay'],
      wards: ['Yen So', 'Tran Phu', 'Linh Nam', 'Hai Ba Trung'],
      firstSelectedLocation : "Yen So, Hoang Mai, Hanoi",
      secondSelectedLocation : "Tran Phu, Quan Hoang Kiem, Hanoi"
    }
  },
  methods:{
      compare(typeOfSelect,cityId){

      }
  },
  computed:{
      compareString : function(){
          return this.firstSelectedLocation + " vs " + this.secondSelectedLocation
      }
  }
}
</script>
