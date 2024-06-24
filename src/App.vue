<template>
  <!-- <div id="app" class="min-h-screen bg-gray-100 flex items-center justify-center">
    <Game />
  </div> -->
  <div id="app" class="absolute top-0 z-[-2] h-screen w-screen bg-[#000000] bg-[radial-gradient(#ffffff33_1px,#000000_1px)] bg-[size:20px_20px] flex items-center justify-center">
    <Game />
  </div>
</template>

<script>
import Game from './components/GameApp.vue';

export default {
  name: 'App',
  components: {
    Game
  }
};
</script>

<style scoped>
</style>
