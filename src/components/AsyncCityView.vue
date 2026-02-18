<template>
    <div class="flex flex-col flex-1 items-center">
        <!-- banner -->
        <div v-if="route.query.preview" class="text-white p-4 bg-weather-secondary w-full text-center">
            <p>
                You are currently previewing this city, click the "+"
                icon to start tracking this city.
            </p>
        </div>

        <!-- weather overview -->
        <div class="flex flex-col items-center text-white py-12">
            <h1 class="text-4xl mb-12">{{ route.params.city }}</h1>

            <p class="text-sm mb-12" v-if="weatherData">
                {{
                    new Date(weatherData.currentTime).toLocaleDateString("en-US", {
                        weekday: "short",
                        day: "2-digit",
                        month: "long",
                    })
                }}
                {{
                    new Date(weatherData.currentTime).toLocaleTimeString("en-US", {
                        timeStyle: "short",
                    })
                }}
            </p>

            <p class="text-8xl mb-8" v-if="weatherData?.current_weather">
                {{ Math.round(weatherData.current_weather.temperature) }}°
            </p>
            <p class="text-6xl">
                {{ weatherIcons[weatherData.current_weather.weathercode] }}
            </p>
        </div>
   

    <hr class="border-white border-opacity-10 border w-full" />

    <!-- Hourly Weather -->
    <div class="max-w-3xl w-full py-12" v-if="weatherData">
        <div class="mx-8 text-white">
            <h2 class="mb-4">Hourly Weather</h2>

            <div class="flex gap-10 overflow-x-scroll">
                <div v-for="hourData in weatherData.hourly" :key="hourData.dt" class="flex flex-col gap-4 items-center">
                    <p class="text-md">
                        {{
                            new Date(hourData.currentTime).toLocaleTimeString(
                                "en-US",
                                { hour: "numeric" }
                            )
                        }}
                    </p>

                    <p class="text-xl">
                        {{ Math.round(hourData.temp) }}°
                    </p>

                </div>
            </div>
        </div>
    </div>
    <!-- Weekly Weather -->
    <div class="max-w-3xl w-full py-12" v-if="weatherData?.daily">
        <div class="mx-8 text-white">
            <h2 class="mb-4">7 Day Forecast</h2>

            <div v-for="day in weatherData.daily" :key="day.date"
                class="flex items-center py-2 border-b border-white/10">
                <p class="flex-1">
                    {{
                        new Date(day.date).toLocaleDateString("en-US", {
                            weekday: "long",
                        })
                    }}
                </p>

                <p class="text-2xl mr-6">
                    {{ weatherIcons[day.weathercode] }}
                </p>

                <p>
                    {{ Math.round(day.max) }}°
                    /
                    {{ Math.round(day.min) }}°
                </p>
            </div>
        </div>
    </div>
    <div class="flex items-center gap-2 py-12 text-white cursor-pointer
    duration-150 hover:text-red-500" @click="removeCity">
        <i class="fa-solid fa-trash"></i>
        <p>Remove City</p>
    </div>
 </div>
</template>

<script setup>
import axios from "axios";
import { ref } from "vue";
import { useRoute, useRouter } from "vue-router";

const route = useRoute();
const weatherData = ref(null);

const weatherIcons = {
    0: "☀️",   // Clear sky
    1: "🌤️",
    2: "⛅",
    3: "☁️",
    45: "🌫️",
    48: "🌫️",
    51: "🌦️",
    61: "🌧️",
    71: "❄️",
    80: "🌦️",
    95: "⛈️",
};


const getWeatherData = async () => {
    try {
        const response = await axios.get(
            "https://api.open-meteo.com/v1/forecast",
            {
                params: {
                    latitude: route.query.lat,
                    longitude: route.query.lon,
                    current_weather: true,
                    hourly: "temperature_2m,weathercode",
                    timezone: "auto",
                    daily: "temperature_2m_max,temperature_2m_min,weathercode",
                },
            }
        );

        const data = response.data;

        // Current weather time
        data.currentTime = new Date(
            data.current_weather.time
        ).getTime();

        // Hourly transform
        const raw = data.hourly;

        data.hourly = raw.time.map((time, index) => ({
            dt: time,
            currentTime: new Date(time).getTime(),
            temp: raw.temperature_2m[index],
            weathercode: raw.weathercode[index],
        }));


        // Weekly transform (FIXED)
        const rawDaily = data.daily;

        data.daily = rawDaily.time.map((date, index) => ({
            date, // already ISO string → NO * 1000
            max: rawDaily.temperature_2m_max[index],
            min: rawDaily.temperature_2m_min[index],
            weathercode: rawDaily.weathercode[index],
        }));


        weatherData.value = data;
    } catch (error) {
        console.error(error);
    }
};

await getWeatherData();
const router = useRouter();
const removeCity = () => {
    const cities = JSON.parse(localStorage.getItem('savedCities'));
    const updatedCities = cities.filter((city) => city.id !== route.query.id);
    localStorage.setItem('savedCities', JSON.stringify(updatedCities));
    router.push({name:"home"})
}
</script>
