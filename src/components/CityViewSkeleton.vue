<template>
  <div class="flex flex-col flex-1">
    <!-- Overview -->
    <div class="flex flex-col py-12 items-center">
      <AnimatedPlaceholder class="max-w-75 w-full mb-2" />
      <AnimatedPlaceholder class="max-w-75 w-full mb-12" />
      <AnimatedPlaceholder
        class="max-w-75 h-25 w-full mb-12"
      />
      <AnimatedPlaceholder class="max-w-75 w-full mb-8" />
      <AnimatedPlaceholder
        class="max-w-75 h-18.75 w-full"
      />
    </div>
    <!-- Hourly -->
    <div class="flex flex-col py-12 px-8 items-center">
      <AnimatedPlaceholder
        class="max-w-3xl h-25 w-full mb-12 "
      />
    </div>
    <!-- Weekly -->
    <div class="flex flex-col py-12 px-8 items-center">
      <AnimatedPlaceholder
        class="max-w-3xl h-25 w-full mb-12"
      />
    </div>
  </div>
</template>

<script setup>
import AnimatedPlaceholder from "./AnimatedPlaceholder.vue";
</script>