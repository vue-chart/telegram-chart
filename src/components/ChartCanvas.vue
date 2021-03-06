<template>
  <div class="chart_wrapper">
    <canvas ref="canvas" width="700" height="400"></canvas>
    <div>
      <label v-for="line in lines" :key="line.name.name">
      <input type="checkbox" v-model="line.name.show" @change="toggleLines(line)"/> {{line.name.name}}</label>
    </div>
    <slot name="slider"></slot>
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
    },
    chartNumber: {
      type: Number
    }
  },
  data () {
    return {
      denomList: den,
      canvas: '',
      lines: [],
      data: this.dataJson[this.chartNumber]
    }
  },
  watch: {
    width (to, fr) {
      this.setWidth(to)
      this.prepareData(this.canvas, this.data)
    },
    chartNumber (to, fr) {
      this.data = this.dataJson[to]
      this.prepareData(this.canvas, this.data)
    }
  },
  mounted () {
    this.canvas = this.$refs.canvas
    this.prepareData(this.canvas, this.data)
  },
  methods: {
    toggleLines (line) {
      this.setHeight(this.getMaxHeight(this.lines))
      this.lines.forEach(l => {
        if (!l.name.show) return
        this.drawLine(this.canvas, l.values, l.color, l.stepX, l.denom)
      })
      let c = this.canvas.getContext('2d')
      this.drawX(this.canvas, c)
      this.drawY(this.canvas, c)
      this.$emit('backgroundUrl', this.canvas.toDataURL())
    },
    // Готовим данные
    prepareData (canvas, data) {
      let c = canvas.getContext('2d')
      this.lines = []
      Object.keys(data.names).forEach(k => {
        let color = data.colors[k]
        let name = data.names[k]
        let type = data.types[k]
        let values = []
        let denom = 1
        data.columns.forEach(c => {
          if (c[0] === k) {
            values = c.slice(1, c.lenght)
            denom = this.denomList[values[0].toString().length] || 1
          }
        })
        let stepX = canvas.width / values.length
        this.lines.push({
          color: color,
          name: {
            name: name,
            show: true
          },
          type: type,
          values: values,
          denom: denom,
          stepX: stepX
        })
      })
      this.setHeight(this.getMaxHeight(this.lines))
      this.lines.forEach(l => {
        this.drawLine(canvas, l.values, l.color, l.stepX, l.denom)
      })
      this.drawX(canvas, c)
      this.drawY(canvas, c)
      this.$emit('backgroundUrl', canvas.toDataURL())
    },
    // Рисуем линии по координатам
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
    // Рисуем ось X
    drawX (canvas, c) {
      c.beginPath()
      c.lineWidth = 4
      c.moveTo(0, canvas.height)
      c.lineTo(canvas.width, canvas.height)
      c.strokeStyle = '#8ec04f'
      c.stroke()
    },
    // Рисуем ось Y
    drawY (canvas, c) {
      c.beginPath()
      c.lineWidth = 4
      c.moveTo(0, 0)
      c.lineTo(0, canvas.height)
      c.strokeStyle = '#8ec04f'
      c.stroke()
    },
    // Устанавливаем ширину для canvas
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
    // Устанавливаем высоту для canvas
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
    // Получаем максимальную координату по оси Y
    getMaxHeight (lines) {
      let maxs = []
      lines.forEach(l => {
        if (!l.name.show) return
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
  width 100%
  max-width 1000px
  height inherit
  display flex
  justify-content center
  flex-direction column
  align-items center
</style>
