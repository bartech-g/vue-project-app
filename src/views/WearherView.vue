<template>
  <div>
    <h1>Weather Information</h1>
    <div class="modern-form">
      <input class="modern-input" type="text" v-model="city" placeholder="Enter city name" />
      <button @click="fetchWeather" class="modern-button">Get Weather</button>
    </div>
    <div v-if="weather">
      <h2>{{ weather.name }}</h2>
      <p>Temperature: {{ weather.main.temp }} °C</p>
      <p>Weather: {{ weather.weather[0].description }}</p>

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
    const url = import.meta.env.VITE_FETCH_URL;
    
    const response = await fetch(`${url}/api/weather/${city.value}`)
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
<style scoped>
.modern-form {
  display: grid;
  grid-template-columns: 1fr;
  width: 100%;

}

/* Dark Green Button */
.modern-button {
  margin: .5rem 0;
  background: linear-gradient(135deg, #1b5e20 0%, #66bb6a 100%);
  /* Dark green to light green */
  color: white;
  /* Text color */
  padding: 12px 24px;
  /* Padding for the button */
  font-size: 16px;
  /* Font size */
  font-weight: bold;
  /* Bold text */
  border: none;
  /* No border */
  border-radius: 8px;
  /* Rounded corners */
  cursor: pointer;
  /* Pointer cursor on hover */
  box-shadow: 0px 8px 15px rgba(0, 0, 0, 0.1);
  max-width: fit-content;
}

/* Hover Effects */
.modern-button:hover {
  background: linear-gradient(120deg, #1b5e20 0%, #66bb6a 100%);
  box-shadow: 0px 15px 20px rgba(0, 0, 0, 0.2);
  /* Stronger shadow on hover */

}

/* Active State */
.modern-button:active {
  transform: translateY(1px);
  /* Small downward movement on click */
  box-shadow: 0px 5px 10px rgba(0, 0, 0, 0.2);
  /* Lighter shadow on click */
}

/* Input Field */
.modern-input {
  max-width: 250px;
  padding: 10px 15px;
  /* Padding inside the input */
  font-size: 16px;
  /* Text size */
  background-color: #f1f8e9;
  /* Light greenish background */
  border: 2px solid #1b5e20;
  /* Dark green border */
  border-radius: 6px;
  /* Rounded corners */
  outline: none;
  /* Remove default outline */
  transition: all 0.3s ease;
  /* Smooth transition */
  box-sizing: border-box;
  /* Ensure padding is included in the width */
}

/* Focus State */
.modern-input:focus {
  border-color: #66bb6a;
  /* Light green border on focus */
  box-shadow: 0 0 5px rgba(102, 187, 106, 0.5);
  /* Green glow effect */
}
</style>
