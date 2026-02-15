<template>
    <div>
        <Suspense>
            <!-- async component -->
            <AsyncCityView />

            <template #fallback>
 
                    <h1 class="text-center">Loading...</h1>
                
                 
            </template>
        </Suspense>
    </div>
</template>

<script setup>
import AsyncCityView from '@/components/AsyncCityView.vue';
</script>