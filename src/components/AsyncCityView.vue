<template>
  <div class="wrapper-outer">
    <div v-if="route.query.preview" class="wrapper-inner">
      <p>Click on the "+" icon to save a city.</p>
    </div>

    <!-- weather info -->
    <div class="weather-wrapper">
      <h1 class="city">{{ route.params.city }}</h1>
      <p class="temperature">{{ Math.round(weatherData.current.temp) }}&deg;</p>
      <p>
        Feeling like {{ Math.round(weatherData.current.feels_like) }}&deg; 👍🏼
      </p>
      <p class="description">
        {{ weatherData.current.weather[0].description }}
      </p>
      <img
        :src="`http://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png`"
        alt="weather information"
      />
    </div>

    <hr />

    <!-- hourly information -->
    <div class="hourly-wrapper">
      <div>
        <h2>Hourly</h2>
        <div class="flex-container">
          <div v-for="hourData in weatherData.hourly" :key="hourData.dt">
            <p>
              {{
                new Date(hourData.currentTime).toLocaleTimeString('en-us', {
                  hourly: 'numeric',
                })
              }}
            </p>
            <img
              :src="`http://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`"
              alt="hourly data information"
            />
            <p>{{ Math.round(hourData.temp) }}&deg;</p>
          </div>
        </div>
      </div>
    </div>

    <hr />

    <!-- weekly information -->
    <div class="weekly-wrapper">
      <div>
        <h2>Forecast</h2>
        <div
          v-for="day in weatherData.daily"
          :key="day.dt"
          class="weekly-wrapper-inner"
        >
          <p>
            {{
              new Date(day.dt * 1000).toLocaleDateString('en-us', {
                weekday: 'long',
              })
            }}
          </p>
          <img
            :src="`http://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`"
            alt="weekly data information"
          />
          <!-- min/max temp -->
          <div class="min-max">
            <p>Min {{ Math.round(day.temp.min) }}&deg;</p>
            <p>Max {{ Math.round(day.temp.max) }}&deg;</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import axios from 'axios'
import { useRoute } from 'vue-router'

const route = useRoute()
const getWeatherData = async () => {
  try {
    const weatherData = await axios.get(
      `https://api.openweathermap.org/data/2.5/onecall?lat=${route.query.lat}&lon=${route.query.lng}&exclude={part}&appid=7efa332cf48aeb9d2d391a51027f1a71&units=metric`
    )
    return weatherData.data
  } catch (err) {
    console.log(err)
  }
}

const weatherData = await getWeatherData()
</script>

<style scoped>
.wrapper-outer {
  display: flex;
  flex-direction: column;
  flex: 1 1 0%;
  align-items: center;
}

hr {
  border: 1px solid var(--text-light);
  width: 100%;
  opacity: 0.8;
}
.wrapper-inner {
  padding: 1rem;
  width: 100%;
  text-align: center;
}

/* weather */
.weather-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  color: var(--text-light);
  padding-top: 0.5rem;
}

.weather-wrapper .city {
  font-size: 3rem;
  margin-bottom: 0.5rem;
}

.weather-wrapper .temperature {
  font-size: 5rem;
}

.weather-wrapper .description {
  text-transform: capitalize;
}
.weather-wrapper img {
  width: 150px;
  height: auto;
}

/* hourly */
.hourly-wrapper {
  max-width: 768px;
  width: 100%;
  padding: 3rem 0 3rem 0;
}

.hourly-wrapper h2 {
  margin-bottom: 0.5rem;
}

.flex-container {
  display: flex;
  gap: 2.5rem;
  overflow-x: scroll;
}

.flex-container div {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  align-items: center;
}

.flex-container div p {
  white-space: nowrap;
  font-size: 1rem;
}

.flex-container div img {
  width: auto;
  height: 50px;
  object-fit: cover;
}

/* weekly */
.weekly-wrapper {
  max-width: 768px;
  width: 100%;
  padding: 3rem 0 3rem 0;
}

.weekly-wrapper-inner {
  display: flex;
  align-items: center;
}
.weekly-wrapper-inner p {
  flex: 1 1 0%;
}

.weekly-wrapper-inner img {
  width: 50px;
  height: 50px;
  object-fit: cover;
}

.weekly-wrapper-inner .min-max {
  display: flex;
  flex: 1 1 0%;
  justify-content: end;
}
</style>
