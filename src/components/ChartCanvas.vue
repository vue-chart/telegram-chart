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
    }
  },
  data () {
    return {
      denomList: den
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
        let m = Math.max.apply(null, l.values)
        maxs.push(m / l.denom + 20)
      })
      return Math.max.apply(null, maxs)
    },
    prepareData () {
      let canvas = this.$refs.canvas
      let c = canvas.getContext('2d')
      let data = this.dataJson[1]
      let lines = []
      let dataX = data.columns[0]
      let wt =  dataX.length * 10
      canvas.width = wt > 1000 ? 1000 : wt
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
        let stepX = canvas.width / values.length
        lines.push({
          color: color,
          name: name,
          type: type,
          values: values,
          denom: denom,
          stepX: stepX
        })
      })
      canvas.height = this.setCanvasHeight(lines)
      lines.forEach(l => {
        this.drawLine(canvas, l.values, l.color, l.stepX, l.denom)
      })
      this.drawX(canvas, c)
      this.drawY(canvas, c)
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
