<template>
  <main class="container">
    <div class="query-wrapper">
      <input
        type="text"
        v-model="searchQuery"
        @input="getSearchResults"
        placeholder="Search for a city :)"
      />
      <ul v-if="mapboxSearchResults">
        <p v-if="searchError">Error, try again.</p>
        <p v-if="!searchError && mapboxSearchResults.length === 0">
          This search does not exist, dummy.
        </p>
        <template v-else>
          <li
            v-for="searchResult in mapboxSearchResults"
            :key="searchResult.id"
            @click="previewCity(searchResult)"
          >
            {{ searchResult.place_name }}
          </li>
        </template>
      </ul>
    </div>
    <div>
      <Suspense>
        <CityList />
      </Suspense>
    </div>
  </main>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'
import { useRouter } from 'vue-router'
import CityList from '../components/CityList.vue'

const router = useRouter()
const previewCity = (searchResult) => {
  console.log(searchResult)
  const [city, state] = searchResult.place_name.split(',')
  router.push({
    name: 'cityView',
    params: { state: state.replaceAll(' ', ''), city: city },
    query: {
      lat: searchResult.geometry.coordinates[1],
      lng: searchResult.geometry.coordinates[0],
      preview: true,
    },
  })
}

const mapboxAPIKey =
  'pk.eyJ1IjoibGVvamFxdWVzIiwiYSI6ImNsaTlhMGoyNjFyb2szZXBjeHNpZm5sdm4ifQ.wxLXRMHNzj28d4JwiEZKmA'
const searchQuery = ref('')
const queryTimeout = ref(null)
const mapboxSearchResults = ref(null)
const searchError = ref(null)

const getSearchResults = () => {
  clearTimeout(queryTimeout.value) //lazy search, when user stops typing the code bellow runs
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== '') {
      try {
        const result = await axios.get(
          `https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}`
        )
        mapboxSearchResults.value = result.data.features
      } catch {
        searchError.value = true
      }

      return //returns out of function so that if this is true, code bellow never runs
    }
    mapboxSearchResults.value = null //if above not true, runs
  }, 300)
}
</script>

<style scoped>
.query-wrapper {
  position: relative;
  padding-top: 1rem;
  margin-bottom: 2rem;
}

.query-wrapper input {
  width: 100%;
  padding: 0.5rem 0.25rem 0.5rem 0.25rem;
  border: 1px solid var(--text-light);
  background-color: transparent;
  border-width: 2px;
  border-radius: 0.25rem;
  color: var(--text-light);
}

.query-wrapper input::placeholder {
  color: var(--text-light);
}

.query-wrapper input:focus {
  border-color: rgb(59, 252, 0);
  outline: 2px solid transparent;
  outline-offset: 2px;
}

.query-wrapper ul {
  position: absolute;
  width: 100%;
  padding: 0.5rem 0.25rem 0.5rem 0.25rem;
  background-color: var(--color-background-secondary);
  box-shadow: rgb(0, 0, 0, 0.2);
  top: 66px;
  border-radius: 0.25rem;
}

.query-wrapper ul li {
  padding: 0.5rem 0 0.5rem 0;
  cursor: pointer;
  list-style-type: none;
  color: var(--text-dark);
}

.query-wrapper ul p {
  color: var(--text-dark);
}
</style>
