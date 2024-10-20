<template>
  <div>
    <h1>Weather Information</h1>
    <input v-model="city" placeholder="Enter city name" />
    <button @click="fetchWeather">Get Weather</button>

    <div v-if="weather">
      <h2>{{ weather.name }}</h2>
      <p>Temperature: {{ weather.main.temp }} °C</p>
      <p>Weather: {{ weather.weather[0].description }}</p>
      <pre>{{ weather }}</pre>
    </div>
    <div v-if="error">{{ error }}</div>
  </div>
</template>

<script setup lang="ts">
// Define Types for Weather Data
interface WeatherData {
  name: string
  main: {
    temp: number
  }
  weather: Array<{
    description: string
  }>
}

import { ref } from 'vue'

// Reactive data
const city = ref<string>('')
const weather = ref<WeatherData | null>(null)
const error = ref<string | null>(null)

const fetchWeather = async () => {
  error.value = null
  weather.value = null

  if (!city.value) {
    error.value = 'Please enter a city name'
    return
  }

  try {
    const response = await fetch(`https://app1.bartechlabs.com/api/weather/${city.value}`)
    if (!response.ok) {
      throw new Error('City not found')
    }

    const data: WeatherData = await response.json()
    weather.value = data
  } catch (err: unknown) {
    if (err instanceof Error) {
      error.value = err.message
    } else {
      error.value = 'An unknown error occurred'
    }
  }
}
</script>
