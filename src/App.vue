<template>
  <div class="flex flex-col min-h-screen font-Roboto bg-weather-primary">
    <SiteNavigation/>
    <!-- <<LeafletMap /> -->
    <RouterView />
  </div>
</template>

<script setup>
import { RouterView } from 'vue-router'
import SiteNavigation from './components/SiteNavigation.vue';
 import LeafletMap from './components/LeafletMap.vue';
</script>

<style lang="scss" scoped>

</style>