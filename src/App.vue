<template>
  <div id="app">
    <Board />
  </div>
</template>

<script>
import Board from "./components/Board";

export default {
  name: "App",
  components: {
    Board,
  },
};
</script>

<style>
*{
  padding: 0;
  margin: 0;
  border: 0;
  box-sizing: border-box;
}
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: #c9c9c9;
  width: 100vw;
  height: 100vh;
  min-height: 0;
}
</style>
