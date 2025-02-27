<script setup lang="ts">
import { onMounted, shallowRef } from "vue";
import maplibregl, {type MapOptions} from 'maplibre-gl';
import 'maplibre-gl/dist/maplibre-gl.css';

const map = shallowRef<maplibregl.Map | null>(null);

const LIGHTMAPTILEURL = "https://tiles.openfreemap.org/styles/bright";

const config = shallowRef<MapOptions>({
  container: "map",
  style: LIGHTMAPTILEURL,
  center: [13.388, 52.517],
  zoom: 12,
  minZoom: 5,
  maxZoom: 18,
});

onMounted(() => {
  navigator.geolocation.getCurrentPosition(
    (position) => {
      const {latitude, longitude} = position.coords;
      config.value.center = [longitude, latitude];

      map.value = new maplibregl.Map(config.value);

      map.value.on("load", () => {
        map.value?.addControl(new maplibregl.NavigationControl(), "bottom-left");
      });
    },
    (error) => {
      console.error("Error getting user location:", error);

      map.value = new maplibregl.Map(config.value);
    }
  );
});
</script>

<template>
  <div class="flex flex-col h-screen">
    <!-- Main content area -->
    <div class="relative flex flex-1">
      <!-- Map container -->
      <div id="map" class="h-full w-full"/>
    </div>
  </div>
</template>
