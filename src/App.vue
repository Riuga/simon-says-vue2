<template>
  <div id="app">
    <SimonGame />
  </div>
</template>

<script>
import SimonGame from './components/SimonGame.vue';

export default {
  name: 'App',
  components: {
    SimonGame
  }
}
</script>

<style>
  *{
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    user-select: none;
    font-family: Impact, Haettenschweiler, 'Arial Narrow Bold', sans-serif;
  }
</style>
