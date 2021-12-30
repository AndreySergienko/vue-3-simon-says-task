<template>
  <SimonGames />
</template>

<script>
import SimonGames from "./components/SimonGames"
export default {
  components: {
    SimonGames
  }
}
</script>

<style>
*,
*::before,
*::after {
  box-sizing: border-box;
}

body {
  margin: 0;
}

h1, h2, h3, h4, h5, h6, p {
  padding: 0;
  margin: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

</style>
