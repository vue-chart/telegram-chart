<template>
  <div class="page_wrapper">
    <select v-model="chartNumber">
      <option v-for="(item, index) in dataJson" :key="index" :value="index">График {{index + 1}}</option>
    </select>
    <chart-canvas :dataJson="dataJson"
                  :width="width"
                  :chartNumber="chartNumber"
                  @backgroundUrl="setImageUrl">
      <div slot="slider" class="slider" ref="slider">
        <div class="size_wrap" ref="slider_resize">
          <!-- Сейчас доступно изменение ширины одним ползунком -->
          <!-- <span class="left" @click="resize($event.target)"></span> -->
          <span class="right" @mousedown="resize()"></span>
        </div>
      </div>
    </chart-canvas>
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
  data () {
    return {
      width: 1000,
      chartNumber: 0,
      targetResize: ''
    }
  },
  methods: {
    setImageUrl (dataUrl) {
      let el = this.$refs.slider
      el.style.backgroundImage = 'url(' + dataUrl + ')'
    },
    // Изменение ширины
    resize () {
      document.addEventListener('mousemove', this.moveSlider)
      document.addEventListener('mouseup', this.stopListen)
    },
    moveSlider (e) {
      let el = this.targetResize
      let elparent = el.parentElement
      let mx = e.movementX
      let w = el.clientWidth + mx
      if (w > elparent.clientWidth) return
      this.width = w
      el.style.width = w + 'px'
    },
    stopListen () {
      document.removeEventListener('mousemove', this.moveSlider)
      document.removeEventListener('mouseup', this.stopListen)
    }
  },
  mounted () {
    this.targetResize = this.$refs.slider_resize
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
.slider
  width 50%
  height 50px
  margin-top 20px
  background-size 100% 100%
  background-repeat no-repeat
  .size_wrap
    height 100%
    position relative
    background-color #c0c0bc36
    span
      display block
      position absolute
      width 5px
      height inherit
      background-color #ade5ae
      cursor e-resize
      &.right
        right 0
      &.left
        left 0
</style>
