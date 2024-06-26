<script setup lang="ts">
import { onMounted, ref } from "vue";
import { invoke } from "@tauri-apps/api/tauri";

import DiskUsage from './DiskUsage.vue';
import EspIdfList from './EspIdfList.vue';
import ConnectedDevicesList from "./ConnectedDevicesList.vue";
import RustDashboardTile from "./RustDashboardTile.vue";
import BooksTile from "./BooksTile.vue";

let versions = ref<string[]>([]);

function extractVersion(path: string): string {
  const parts = path.split('/');
  const lastPart = parts[parts.length - 1];
  const version = lastPart.replace('esp-idf-', '');
  return version;
}

function extractVersions(paths: string[]): string[] {
  return paths.map(extractVersion);
}

onMounted(() => {
  invoke("get_esp_idf_list").then((espIdfList) => {
    console.log("ESP-IDF List:" + espIdfList);
    versions.value = extractVersions((espIdfList as string[]));
  }).catch((error) => {
    console.error(error);
  });
});


</script>

<template>
  <div class="grid-container">
    <div class="tile">
      <esp-idf-list class="esp-idf-list" :versions="versions" />
    </div>
    <div class="tile">
      <disk-usage />
    </div>
    <div class="tile">
      <connected-devices-list />
    </div>
    <div class="tile">
      <rust-dashboard-tile />
    </div>
    <div class="tile">
      <books-tile />
    </div>
  </div>
</template>

<style>
.add-button {
  display: inline-block;
  padding: 10px 20px;
  color: #fff;
  background-color: #007bff;
  border-radius: 5px;
  text-decoration: none;
  transition: background-color 0.2s ease;
}

.add-button:hover {
  background-color: #0056b3;
}
</style>

<style scoped>


.grid-container {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 20px;
  padding: 20px;
}

.tile {
  border: 1px solid #ccc;
  padding: 20px;
  border-radius: 8px;
  background-color: #f8f8f8;
}

.tile :deep(h2)::after {
  content: '';
  display: block;
  height: 1px;
  background-color: lightgray;
  margin-top: 10px;
  margin-bottom: 10px;
}

@media (prefers-color-scheme: dark) {
  .tile {
    background-color: #989898;
  }
}
</style>