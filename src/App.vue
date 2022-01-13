<script setup lang="ts">
import Floor from "./components/Floor.vue";
</script>

<template>
  <div :style="{ width: 200, height: 200, margin: '10px auto' }">
    <Floor></Floor>
  </div>
</template>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
