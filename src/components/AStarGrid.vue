<template>
  <canvas class="gridCanvas" :width="cWidth" :height="cHeight"></canvas>
</template>

<script>
  export default {
    props: {
      cols: Number,
      rows: Number,
      squareSize: Number
    },
    
    computed: {
      cWidth() {
        return 1 + this.cols * this.squareSize
      },
      cHeight() {
        return 1 + this.rows * this.squareSize
      }
    },
    
    data() {
      return {
        filled: []
      }
    },
    
    methods: {
      reset() {
        this.filled = []
        for (let x = 0; x < this.cols; x++) {
          this.filled[x] = []
          for (let y = 0; y < this.rows; y++) {
            this.filled[x][y] = 0
          }
        }
      },
      drawGrid() {
        const ctx = this.$el.getContext("2d")
        ctx.strokeStyle = 'lightgrey'
        
        console.log("Refreshing")
        
        // vertical
        for (let x = 0.5; x < this.cWidth; x += this.squareSize) {
          ctx.moveTo(x, 0)
          ctx.lineTo(x, this.cHeight)
        }
        
        // horizontal
        for (let y = 0.5; y < this.cHeight; y += this.squareSize) {
          ctx.moveTo(0, y)
          ctx.lineTo(this.cWidth, y)
        }
        
        ctx.stroke()
      },
      cursorPositionInGrid(canvas, event) {
        const rect = canvas.getBoundingClientRect()
        const x = event.clientX - rect.left - 20 //padding
        const y = event.clientY - rect.top - 20 //padding
        return {
          x: Math.floor(x / this.squareSize),
          y: Math.floor(y / this.squareSize)
        }
      },
      squareClicked(canvas, event) {
        const {x, y} = this.cursorPositionInGrid(canvas, event)
        
        // out of bounds?
        if (x < 0 || x >= this.cols || y < 0 || y >= this.rows) {
          return
        }
        
        const squareState = this.filled[x][y]
        
        if (squareState === 0) {
          // add obstacle
          this.paintSquare(x, y, "#FF0000")
          this.filled[x][y] = 1 - this.filled[x][y];
        } else if (squareState === 1) {
          // remove obstacle
          this.paintSquare(x, y, "#FFFFFF")
          this.filled[x][y] = 1 - this.filled[x][y];
        }
        
        this.drawGrid()
      },
      paintSquare(x, y, color) {
        const ctx = this.$el.getContext("2d")
        const sw = this.squareSize
        
        ctx.fillStyle = color
        ctx.fillRect(x * sw, y * sw, sw, sw)
      }
    },
    
    mounted() {
      this.reset()
      this.drawGrid()
      
      const canvas = this.$el
      canvas.addEventListener("mousedown", (e) => this.squareClicked(canvas, e))
    },
    
    updated() {
      // data changes
      this.drawGrid()
    },
    
    // react to size change
    watch: {
      rows: function () {
        this.reset()
      },
      cols: function () {
        this.reset()
      },
    }
  }
</script>

<style scoped>
  .gridCanvas {
    position: relative !important;
    border: lightgrey 1px solid;
    border-radius: 5px;
    box-shadow: 0 2px 3px rgba(0, 0, 0, 0.2);
    padding: 20px;
  }
</style>