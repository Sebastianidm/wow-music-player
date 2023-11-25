<template>
  <div class="wow-theme">
    <MusicPlayer msg="Welcome to Your Vue.js App" />
  </div>
</template>

<script>
import MusicPlayer from "./components/MusicPlayer.vue";

export default {
  name: "App",
  components: {
    MusicPlayer,
  },
};
</script>

<style>
body {
  margin: 0;
  padding: 0;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100vh;
  margin: 0; /* Elimina los márgenes predeterminados del body */
  background: url("/public/img/wowbackground.jpg") center center fixed;
  background-size: cover;
}
</style>
