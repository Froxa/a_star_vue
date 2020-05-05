<template>
  <div id="app">
    <h1>A* Pathfinder</h1>
    <AStarGrid :squareWidth=50 :height=10 :width=10></AStarGrid>
  </div>
</template>

<script>
  import AStarGrid from "@/components/AStarGrid";

  export default {
    name: 'App',
    components: {
      AStarGrid
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
