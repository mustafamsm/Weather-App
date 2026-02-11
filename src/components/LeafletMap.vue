<template>
  <div ref="mapEl" class="map"></div>
</template>

<script setup>
import { onMounted, onBeforeUnmount, ref } from 'vue';
import L from 'leaflet';
import 'leaflet/dist/leaflet.css';

import { GeoSearchControl, OpenStreetMapProvider } from 'leaflet-geosearch';
import 'leaflet-geosearch/dist/geosearch.css';

const mapEl = ref(null);
let map;
let searchControl;

onMounted(() => {
  map = L.map(mapEl.value).setView([40.73, -73.93], 12);

  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '&copy; OpenStreetMap contributors',
  }).addTo(map);

  const provider = new OpenStreetMapProvider();

  searchControl = new GeoSearchControl({
    provider,
    style: 'bar',
    showMarker: true,
    showPopup: false,
    autoClose: true,
    retainZoomLevel: false,
    animateZoom: true,
    keepResult: true
  });

  map.addControl(searchControl);
});

onBeforeUnmount(() => {
  map?.remove();
});
</script>

<style scoped>
.map {
  height: 100vh;
}

.leaflet-control-geosearch {
  max-width: 400px;
}
</style>
