<template>
    <div v-for="city in savedCities" :key="city.id">
        <CityCard :city="city" @click="goToCityView(city)" />
    </div>
    <p v-if="savedCities.length===0">
        No Location added. to start tracking a location , serach in 
        the filed above
    </p>
</template>

<script setup>
import axios from 'axios';
import { ref } from 'vue';
import CityCard from './CityCard.vue';
import { useRouter } from 'vue-router';


const savedCities = ref([]);
const getCities = async () => {
    if (localStorage.getItem('savedCities')) {
        savedCities.value = JSON.parse(
            localStorage.getItem('savedCities')
        );
        const requests = [];
        savedCities.value.forEach((city) => {
            requests.push(
                axios.get(
                    "https://api.open-meteo.com/v1/forecast",
                    {
                        params: {
                            latitude: city.coords.lat,
                            longitude: city.coords.lon,
                            current_weather: true,
                            timezone: "auto"
                        }
                    }
                )
            );
        });

        const weatherData = await Promise.all(requests);

        weatherData.forEach((value, index) => {
            savedCities.value[index].weather = value.data;
        });
    }
};

await getCities();
const router = useRouter();

const goToCityView = (city) => {
    router.push({
        name: "cityView",
        params: {
            state: city.state,
            city: city.city,

        },
        query: {
            lat: city.coords.lat,
            lon: city.coords.lon
        }
    })
}
</script>
