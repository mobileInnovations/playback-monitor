<template>
  <v-app>
    <v-main>
      <Monitor :video-type="videoType" :payload="payload" />
    </v-main>
  </v-app>
</template>

<script setup lang="ts">
import { ref } from "vue";
// @ts-ignore: Allow importing Vue SFC without a declaration file
import Monitor from "./components/Monitor.vue";

const url = new URL(window.location.href);
const params = new URLSearchParams(window.location.search);
const urlParams = Object.fromEntries(params.entries());
const payload = urlParams;
const videoType = ref("");

if (url.pathname.includes("RealVideo")) {
  videoType.value = "RealVideo";
} else if (url.pathname.includes("PlayBack")) {
  videoType.value = "Playback";
}
</script>
