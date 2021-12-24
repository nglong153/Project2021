<template>
  <component :is="resolveLayout" >
     <v-scroll-y-transition>
      <router-view></router-view>
     </v-scroll-y-transition>
    
    
  </component>
</template>

<script>
import { computed } from '@vue/composition-api'
import { useRouter } from '@/utils'
import LayoutBlank from '@/layouts/Blank.vue'
import LayoutContent from '@/layouts/Content.vue'

export default {
  components: {
    LayoutBlank,
    LayoutContent
  },
  setup() {
    const { route } = useRouter()

    const resolveLayout = computed(() => {
      // Handles initial route
      if (route.value.name === null) return null

      if (route.value.meta.layout === 'blank') return 'layout-blank'

      return 'layout-content'
    })

    return {
      resolveLayout,
    }
  },
}
</script>

<style scoped>

</style>
