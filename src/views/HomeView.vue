<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input type="text" v-model="searchQuery" @input="getSearchResults" placeholder="Search for a city or state"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none" />

      <ul class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-16.5" v-if="searchResults">
        <p v-if="searchError">
          Sorry,somthing went wrong please try again
        </p>

        <p v-if="!searchError && searchResults.length === 0">
          No Results match query, try a diffent term.
        </p>
        <template v-else>
          <li v-for="result in searchResults" :key="result.place_id" class="py-2 cursor-pointer"
            @click="previewCity(result)">
            {{ result.display_name }}
          </li>
        </template>
      </ul>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
        <CityList />
        <template #fallback>
          <p>Loading...</p>
        </template>
      </Suspense>
    </div>
  </main>
</template>

<script setup>
import { ref, Suspense } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";
import CityList from "@/components/CityList.vue";

const router = useRouter()
const searchQuery = ref("");
const queryTimeout = ref(null);
const searchResults = ref(null);
const searchError = ref(null)
const getSearchResults = () => {
  clearTimeout(queryTimeout.value);

  queryTimeout.value = setTimeout(async () => {
    if (!searchQuery.value) {
      searchResults.value = null;
      return;
    }

    try {
      const result = await axios.get(
        "https://nominatim.openstreetmap.org/search",
        {
          params: {
            q: searchQuery.value,
            format: "json",
            addressdetails: 1,
            limit: 5,
          },
          headers: {
            "Accept-Language": "en",
          },
        }
      );

      searchResults.value = result.data;
    } catch (error) {
      console.error("Geocoding error:", error);
      searchError.value = true
    }
  }, 300);
};

const previewCity = (result) => {
  console.log("Selected:", result);
  const [city, state] = result.display_name.split(",");
  router.push({
    name: "cityView",
    params: {
      state: state.replaceAll(" ", ""),
      city: city
    },
    query: {
      lat: result.lat,
      lon: result.lon,
      preview: true
    }

  });
  const lat = result.lat;
  const lon = result.lon;

  // Example: emit to parent or move map
  // map.setView([lat, lon], 12)

  searchResults.value = null;
};
</script>

<style scoped></style>
