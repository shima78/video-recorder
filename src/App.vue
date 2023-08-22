<script setup lang="ts">
import VideoRecorder from "./components/VideoRecorder.vue";
</script>

<template>
  <img class="logo" src="./assets/logo.svg" alt="Logo" />
  <VideoRecorder :timeout="30000" />
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em rgba(105, 100, 255, 0.67));
}

</style>
