<template>
  <div id="app">
    <h1>A* Pathfinder</h1>
    <div id="settings">
      <div class="form-group">
        <label for="rows">Rows:</label>
        <input v-model="rows" id="rows" type="number">
      </div>
      <div class="form-group">
        <label for="cols">Cols:</label>
        <input v-model="cols" id="cols" type="number">
      </div>
      <div class="form-group">
        <label for="size">Square Size:</label>
        <input v-model="size" id="size" type="number">
      </div>
    </div>
    <div>
      <AStarGrid :squareSize=parseInt(size) :rows=parseInt(rows) :cols=parseInt(cols)></AStarGrid>
    </div>
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
  
  #settings {
    display: inline-block;
    border: lightgrey 1px solid;
    border-radius: 5px;
    box-shadow: 0 2px 3px rgba(0, 0, 0, 0.2);
    padding: 20px;
    margin-bottom: 15px;
  }
  
  .form-group {
    display: flex;
    justify-content: space-between;
    margin-top: 10px;
  }
  
  .form-group label {
    margin-right: 10px;
    padding: 5px 0px;
  }
  
  .form-group input {
    margin-left: 10px;
    padding: 5px 10px;
  }
</style>
