<template>
  <v-app class="weather-app">
    <v-navigation-drawer permanent color="dark" width="80">
      <v-list nav dense>
        <v-list-item>
          <v-list-item-icon>
            <v-icon>mdi-weather-cloudy</v-icon>
          </v-list-item-icon>
        </v-list-item>
        <v-list-item>
          <v-list-item-icon>
            <v-icon>mdi-view-list</v-icon>
          </v-list-item-icon>
        </v-list-item>
        <v-list-item>
          <v-list-item-icon>
            <v-icon>mdi-map</v-icon>
          </v-list-item-icon>
        </v-list-item>
        <v-list-item>
          <v-list-item-icon>
            <v-icon>mdi-cog</v-icon>
          </v-list-item-icon>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>

    <v-main class="bg-dark">
      <v-container fluid class="pa-6">
        <v-row>
          <v-col cols="12" md="8">
            <!-- Search Bar -->
            <v-text-field
              v-model="city"
              label="Search for cities"
              dark
              filled
              rounded
              dense
              prepend-inner-icon="mdi-magnify"
              @keyup.enter="fetchWeather"
              class="mb-6"
            ></v-text-field>

            <!-- Current Weather -->
            <v-card v-if="weather" dark class="mb-6 transparent">
              <v-card-text>
                <div class="text-h3 mb-2">{{ weather.name }}</div>
                <div class="text-subtitle-1">Chance of rain: {{ weather.rain ? weather.rain['1h'] : '0' }}%</div>
                <div class="d-flex align-center justify-space-between mt-6">
                  <div class="text-h1">{{ Math.round(weather.main.temp) }}°</div>
                  <v-icon size="120" color="amber">{{ getWeatherIcon(weather.weather[0].id) }}</v-icon>
                </div>
              </v-card-text>
            </v-card>

            <!-- Today's Forecast -->
            <v-card dark class="mb-6">
              <v-card-title>TODAY'S FORECAST</v-card-title>
              <v-card-text>
                <v-row>
                  <v-col v-for="hour in hourlyForecast" :key="hour.time" cols="2">
                    <div class="text-center">
                      <div class="text-caption">{{ hour.time }}</div>
                      <v-icon color="amber" class="my-2">{{ hour.icon }}</v-icon>
                      <div class="text-h6">{{ hour.temp }}°</div>
                    </div>
                  </v-col>
                </v-row>
              </v-card-text>
            </v-card>

            <!-- Air Conditions -->
            <v-card dark>
              <v-card-title class="d-flex justify-space-between">
                AIR CONDITIONS
                <v-btn text color="primary">See more</v-btn>
              </v-card-title>
              <v-card-text>
                <v-row>
                  <v-col cols="6">
                    <div class="d-flex align-center mb-4">
                      <v-icon class="mr-2">mdi-thermometer</v-icon>
                      <div>
                        <div class="text-caption">Real Feel</div>
                        <div class="text-h6">{{ Math.round(weather?.main?.feels_like || 0) }}°</div>
                      </div>
                    </div>
                    <div class="d-flex align-center">
                      <v-icon class="mr-2">mdi-water-percent</v-icon>
                      <div>
                        <div class="text-caption">Chance of rain</div>
                        <div class="text-h6">{{ weather?.rain ? weather.rain['1h'] : '0' }}%</div>
                      </div>
                    </div>
                  </v-col>
                  <v-col cols="6">
                    <div class="d-flex align-center mb-4">
                      <v-icon class="mr-2">mdi-weather-windy</v-icon>
                      <div>
                        <div class="text-caption">Wind</div>
                        <div class="text-h6">{{ weather?.wind?.speed || 0 }} km/h</div>
                      </div>
                    </div>
                    <div class="d-flex align-center">
                      <v-icon class="mr-2">mdi-sun-wireless</v-icon>
                      <div>
                        <div class="text-caption">UV Index</div>
                        <div class="text-h6">3</div>
                      </div>
                    </div>
                  </v-col>
                </v-row>
              </v-card-text>
            </v-card>
          </v-col>

          <!-- 7-Day Forecast -->
          <v-col cols="12" md="4">
            <v-card dark class="forecast-card">
              <v-card-title>7-DAY FORECAST</v-card-title>
              <v-list dark>
                <v-list-item v-for="day in weekForecast" :key="day.day">
                  <v-list-item-content>
                    <div class="d-flex justify-space-between align-center">
                      <div class="text-body-1">{{ day.day }}</div>
                      <v-icon color="amber">{{ day.icon }}</v-icon>
                      <div class="text-body-1">{{ day.condition }}</div>
                      <div class="text-body-1">{{ day.temp }}°/{{ day.temp_min }}°</div>
                    </div>
                  </v-list-item-content>
                </v-list-item>
              </v-list>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
export default {
  data() {
    return {
      city: '',
      weather: null,
      error: '',
      hourlyForecast: [
        { time: '6:00 AM', temp: 25, icon: 'mdi-weather-cloudy' },
        { time: '9:00 AM', temp: 28, icon: 'mdi-weather-partly-cloudy' },
        { time: '12:00 PM', temp: 33, icon: 'mdi-weather-sunny' },
        { time: '3:00 PM', temp: 34, icon: 'mdi-weather-sunny' },
        { time: '6:00 PM', temp: 32, icon: 'mdi-weather-sunny' },
        { time: '9:00 PM', temp: 30, icon: 'mdi-weather-night-partly-cloudy' },
      ],
      weekForecast: [
        { day: 'Today', temp: 36, temp_min: 22, condition: 'Sunny', icon: 'mdi-weather-sunny' },
        { day: 'Tue', temp: 37, temp_min: 21, condition: 'Sunny', icon: 'mdi-weather-sunny' },
        { day: 'Wed', temp: 37, temp_min: 21, condition: 'Sunny', icon: 'mdi-weather-sunny' },
        { day: 'Thu', temp: 37, temp_min: 21, condition: 'Cloudy', icon: 'mdi-weather-cloudy' },
        { day: 'Fri', temp: 37, temp_min: 21, condition: 'Cloudy', icon: 'mdi-weather-cloudy' },
        { day: 'Sat', temp: 37, temp_min: 21, condition: 'Rainy', icon: 'mdi-weather-rainy' },
        { day: 'Sun', temp: 37, temp_min: 21, condition: 'Storm', icon: 'mdi-weather-lightning' },
      ],
    }
  },
  methods: {
    async fetchWeather() {
      if (this.city.trim() === '') {
        this.error = 'Please enter a city name.'
        return
      }
      try {
        const apiKey = '1b0ab424ff99cf738761502d3b912761'
        const response = await fetch(
          `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=metric&appid=${apiKey}`
        )
        const data = await response.json()
        if (data.cod === 200) {
          this.weather = data
          this.error = ''
        } else {
          this.error = 'City not found.'
          this.weather = null
        }
      } catch (error) {
        this.error = 'Unable to fetch weather data.'
        this.weather = null
      }
    },
    getWeatherIcon(id) {
      if (id >= 200 && id < 300) return 'mdi-weather-lightning'
      if (id >= 300 && id < 500) return 'mdi-weather-pouring'
      if (id >= 500 && id < 600) return 'mdi-weather-rainy'
      if (id >= 600 && id < 700) return 'mdi-weather-snowy'
      if (id >= 700 && id < 800) return 'mdi-weather-fog'
      if (id === 800) return 'mdi-weather-sunny'
      if (id > 800) return 'mdi-weather-cloudy'
      return 'mdi-help-circle-outline'
    },
  },
}
</script>

<style scoped>
.weather-app {
  background-color: #000000;
}
.bg-dark {
  background-color: #ffffff !important;
}
.transparent {
  background-color: transparent !important;
  box-shadow: none !important;
}
.forecast-card {
  height: 100%;
}
</style>