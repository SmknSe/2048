<template>
  <game></game>
</template>

<script>
import Game from './components/Game.vue';

export default {
  name: 'App',
  components: {
    Game
  }
}
</script>

<style>
*{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Clear Sans", "Helvetica Neue", Arial, sans-serif;
  font-size: 20px;
  font-weight: 700;
}
#app {
  position: relative;
  min-height: 100vh;
  min-width: 100vw;
  background-color: rgb(26, 37, 46);
}
</style>
