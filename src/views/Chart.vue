<template>
  <div class="view_wrapper">
  </div>
</template>

<script>
export default {
  name: 'Chart',
  props: {
    dataJson: {
      type: Array
    }
  },
  created () {
    console.log(this.dataJson)
  }
}
</script>

<style scoped lang="stylus">

</style>
