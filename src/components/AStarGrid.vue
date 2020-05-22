<template>
  <canvas id="gridCanvas" :width="cWidth" :height="cHeight"></canvas>
</template>

<script>
  import PriorityQueue from "@/assets/js/PriorityQueue";
  
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
        filled: [],
        open: new PriorityQueue((a, b) => a.fScore < b.fScore),
        cameFrom: {},
        gScore: {},
        end: [9, 9]
      }
    },
    
    methods: {
      reset() {
        console.log("RESETING")
        this.filled = []
        for (let x = 0; x < this.cols; x++) {
          this.filled[x] = []
          for (let y = 0; y < this.rows; y++) {
            this.filled[x][y] = 0
            this.paintSquare(x,y,"#fff")
          }
        }
        
        this.drawGrid()
        
        this.open.clear()
        this.cameFrom = {}
        this.gScore = {}
      },
      drawGrid() {
        const ctx = document.getElementById("gridCanvas").getContext("2d")
        ctx.strokeStyle = 'lightgrey'
        
        console.log("DRAWING GRID")
        
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
        const ctx = document.getElementById("gridCanvas").getContext("2d")
        const sw = this.squareSize
        
        ctx.fillStyle = color
        ctx.fillRect(x * sw, y * sw, sw, sw)
      },
      add(a, b) {
        return [a[0] + b[0], a[1] + b[1]]
      },
      h(coordinate) {
        return Math.sqrt(
          Math.pow(this.end[0] - coordinate[0], 2)
          + Math.pow(this.end[1] - coordinate[1], 2)
        )
      },
      aStar() {
        console.log("ASTAR")
        this.open.clear()
        this.cameFrom = {}
        this.gScore = {}
        
        const directions = [
          [0, 1], [0, -1], [1, 0], [-1, 0], [1, 1], [1, -1], [-1, 1], [-1, -1]
        ]
        const start = [0, 0]
        
        this.open.push({c: start, fScore: this.h(start)})
        this.gScore[start] = 0
        
        while (!this.open.isEmpty()) {
          let current = this.open.pop()
          console.log("looking at" + current.c)
          this.paintSquare(current.c[0], current.c[1], "#90caf9")
          if (current.c[0] === this.end[0] && current.c[1] === this.end[1]) {
            this.drawSolution()
            return
          }
          for (let d of directions) {
            let neighbor = this.add(current.c, d)
            // out of bounds ?
            if (neighbor[0] < 0 || neighbor[1] < 0 || neighbor[0] > this.cols - 1 || neighbor[1] > this.rows - 1) {
              continue
            }
            // blocked ?
            if (this.filled[neighbor[0]][neighbor[1]] === 1) {
              continue
            }
            let nGscore = this.gScore[current.c] + 1
            // diagonal?
            if (d[0] !== 0 && d[1] !== 0) {
              nGscore += Math.sqrt(2) - 1
            }
            // new shortest path?
            if (!(neighbor in this.gScore) || nGscore < this.gScore[neighbor]) {
              this.cameFrom[neighbor] = current.c
              this.gScore[neighbor] = nGscore
              if (!this.open.contains(neighbor)) {
                this.open.push({c: neighbor, fScore: nGscore + this.h(neighbor)})
              }
            }
          }
        }
      },
      drawSolution() {
        let coords = this.end
        this.paintSquare(coords[0], coords[1], "#0f0")
        
        while (coords in this.cameFrom) {
          coords = this.cameFrom[coords]
          this.paintSquare(coords[0], coords[1], "#0000ff")
        }
        
        this.paintSquare(coords[0], coords[1], "#ff0")
        
        this.drawGrid()
      }
    },
    
    mounted() {
      this.reset()
      this.drawGrid()
      
      const canvas = document.getElementById("gridCanvas")
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
  #gridCanvas {
    position: relative !important;
    border: lightgrey 1px solid;
    border-radius: 5px;
    box-shadow: 0 2px 3px rgba(0, 0, 0, 0.2);
    padding: 20px;
  }
</style>