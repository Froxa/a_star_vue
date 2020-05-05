<template>
  <div id="app">
    <h1>A* Pathfinder</h1>
    <div>
      <div>
        <label for="rows">Rows:</label>
        <input v-model="rows" id="rows" type="number">
      </div>
      <div>
        <label for="cols">Cols:</label>
        <input v-model="cols" id="cols" type="number">
      </div>
      <div>
        <label for="size">Square Size:</label>
        <input v-model="size" id="size" type="number">
      </div>
    </div>
    <AStarGrid :squareSize=parseInt(size) :rows=rows :cols=cols></AStarGrid>
  </div>
</template>

<script>
  import AStarGrid from "@/components/AStarGrid";
  
  export default {
    name: 'App',
    components: {
      AStarGrid
    },
    data() {
      return {
        rows: 10,
        cols: 10,
        size: 50
      }
    }
  }
</script>

<style>
  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }
</style>
