<template>
  <div class="chart_wrapper">
    <canvas ref="canvas" width="700" height="400"></canvas>
  </div>
</template>

<script>
const den = [1, 1, 1, 10, 100, 1000, 10000, 100000]
export default {
  name: 'chart-canvas',
  props: {
    dataJson: {
      type: Array
    },
    width: {
      type: Number
    }
  },
  data () {
    return {
      denomList: den,
      canvas: ''
    }
  },
  watch: {
    width (to, fr) {
      this.setWidth(to)
    }
  },
  mounted () {
    this.prepareData()
    console.log(this.dataJson)
  },
  methods: {
    prepareData () {
      this.canvas = this.$refs.canvas
      let c = this.canvas.getContext('2d')
      let data = this.dataJson[4]
      let lines = []
      let dataX = data.columns[0]
      this.setWidth(dataX.length * 10)
      Object.keys(data.names).forEach(k => {
        let color = data.colors[k]
        let name = data.names[k]
        let type = data.types[k]
        let values = []
        let denom = 1
        data.columns.forEach(c => {
          if (c[0] === k) {
            c.splice(0, 1)
            values = c
            denom = this.denomList[c[0].toString().length] || 1
          }
        })
        let stepX = this.canvas.width / values.length
        lines.push({
          color: color,
          name: name,
          type: type,
          values: values,
          denom: denom,
          stepX: stepX
        })
      })
      this.setHeight(this.getMaxHeight(lines))
      lines.forEach(l => {
        this.drawLine(this.canvas, l.values, l.color, l.stepX, l.denom)
      })
      this.drawX(this.canvas, c)
      this.drawY(this.canvas, c)
    },
    drawLine (canvas, values, color, stepX, denom) {
      let c = canvas.getContext('2d')
      let x = 0
      c.beginPath()
      c.lineWidth = 1
      c.moveTo(0, canvas.height)
      c.strokeStyle = color
      values.forEach(point => {
        point /= denom
        c.lineTo(x, canvas.height - point)
        c.moveTo(x, canvas.height - point)
        x += stepX
      })
      c.stroke()
    },
    drawX (canvas, c) {
      c.beginPath()
      c.lineWidth = 4
      c.moveTo(0, canvas.height)
      c.lineTo(canvas.width, canvas.height)
      c.strokeStyle = '#8ec04f'
      c.stroke()
    },
    drawY (canvas, c) {
      c.beginPath()
      c.lineWidth = 4
      c.moveTo(0, 0)
      c.lineTo(0, canvas.height)
      c.strokeStyle = '#8ec04f'
      c.stroke()
    },
    setWidth (w) {
      if (w > 1000) {
        w = 1000
      }
      if (w < 300) {
        w = 300
      }
      if (this.canvas) {
        this.canvas.width = w
      }
    },
    setHeight (h) {
      if (h > 700) {
        h = 700
      }
      if (h < 300) {
        h = 300
      }
      if (this.canvas) {
        this.canvas.height = h
      }
    },
    getMaxHeight (lines) {
      let maxs = []
      lines.forEach(l => {
        let m = Math.max.apply(null, l.values)
        maxs.push(m / l.denom + 20)
      })
      return Math.max.apply(null, maxs)
    }
  }
}
</script>

<style scoped lang="stylus">
.chart_wrapper
  width inherit
  height inherit
  display flex
  justify-content center
  align-items center
</style>
