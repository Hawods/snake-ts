<template>
  <GameContainer />
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import GameContainer from './components/GameContainer.vue'

export default defineComponent({
  name: 'App',
  components: {
    GameContainer
  }
})
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
