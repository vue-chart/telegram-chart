<template>
  <div class="chart_wrapper">
    <canvas ref="canvas" width="700" height="400"></canvas>
  </div>
</template>

<script>
export default {
  name: 'chart-canvas',
  props: {
    dataJson: {
      type: Array
    }
  },
  mounted () {
    this.prepareData()
    console.log(this.dataJson)
  },
  methods: {
    setCanvasHeight (lines) {
      let maxs = []
      lines.forEach(l => {
        maxs.push(Math.max.apply(null, l.values))
      })
      return Math.max.apply(null, maxs)
    },
    prepareData () {
      let canvas = this.$refs.canvas
      let c = canvas.getContext('2d')
      let data = this.dataJson[0]
      let lines = []
      let dataX = data.columns[0]
      canvas.width = dataX.length * 10
      Object.keys(data.names).forEach(k => {
        let color = data.colors[k]
        let name = data.names[k]
        let type = data.types[k]
        let values = []
        data.columns.forEach(c => {
          if (c[0] === k) {
            c.splice(0, 1)
            values = c
          }
        })
        let stepX = canvas.width / values.length
        lines.push({
          color: color,
          name: name,
          type: type,
          values: values,
          stepX: stepX
        })
      })
      // canvas.height = this.setCanvasHeight(lines)
      lines.forEach(l => {
        this.drawLine(canvas, l.values, l.color, l.stepX)
      })
      this.drawX(canvas, c)
      this.drawY(canvas, c)
    },
    drawLine (canvas, values, color, stepX) {
      let c = canvas.getContext('2d')
      let x = 0
      c.beginPath()
      c.lineWidth = 1
      c.moveTo(0, canvas.height)
      c.strokeStyle = color
      values.forEach(point => {
        // point = point / 100
        c.lineTo(x, point)
        c.moveTo(x, point)
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
