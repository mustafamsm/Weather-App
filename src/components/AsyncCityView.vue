<template>
    <div>

    </div>
</template>

<script setup>
import axios from "axios";
import { useRoute } from "vue-router";

const route = useRoute();

const getWeatherData = async () => {
    try {
        const weatherData = await axios.get("https://api.open-meteo.com/v1/forecast",
            {
                params: {
                    latitude: route.query.lat,
                    longitude: route.query.lon,
                    current_weather: true,
                    hourly: "temperature_2m",
                    timezone: "auto"   // IMPORTANT
                }
            }
        );
        
        // Current weather time
        weatherData.data.currentTime = new Date(
            weatherData.data.current_weather.time
        ).getTime();

        // Hourly times
        weatherData.data.hourlyWithTime =
            weatherData.data.hourly.time.map((time, index) => {
                return {
                    time: new Date(time).getTime(),
                    temperature:
                        weatherData.data.hourly.temperature_2m[index]
                };
            });


        return weatherData.data;
    } catch (error) {
        console.error(error)
    }
}

const weatherData = await getWeatherData();
console.log(weatherData)
</script>
