<template>
  <h2>种子下载</h2>
  <div class="torrent-link">
    <input class="input" type="text" v-model="input">
    <button class="button" @click="download">下载</button>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';

const input = ref('')

function download() {

}
</script>