<template>
  <div class="page_wrapper">
    <chart-canvas :dataJson="dataJson"/>
  </div>
</template>

<script>
import ChartCanvas from '@/components/ChartCanvas.vue'
export default {
  name: 'Chart',
  props: {
    dataJson: {
      type: Array
    }
  },
  created () {
    // console.log(this.dataJson)
  },
  components: {
    ChartCanvas
  }
}
</script>

<style scoped lang="stylus">
.page_wrapper
  width inherit
  height inherit
</style>
