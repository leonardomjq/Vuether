<template>
  <header>
    <nav class="container">
      <RouterLink :to="{ name: 'home' }">
        <div class="nav-wrapper">
          <i class="sun fa-brands fa-vuejs"></i>
          <p>Vuether</p>
        </div>
      </RouterLink>

      <div class="modal">
        <i class="fa-solid fa-question" @click="toggleModal"></i>
        <i
          class="fa-solid fa-plus"
          @click="addCity"
          v-if="route.query.preview"
        ></i>
      </div>

      <BaseModal :modalActive="modalActive" @close-modal="toggleModal">
        <div class="modal-wrapper">
          <h1>Choices</h1>
          <p>
            Decided to try a lot of new things at the same time. Not a good
            idea. Should've went with a safer approach.<br />
            Tried Vue 3 instead of Vue 2. <br />
            Tried learning Pinia but got confused. <br />
            Tried nesting with postcss and it broke the app. <br />
          </p>

          <h2>Challenges</h2>
          <ol>
            <li>CSS Mobile First</li>
            <li>Vue@latest</li>
            <li>Postcss (nesting setup)</li>
            <li>Transitions</li>
            <li>Teleport</li>
            <li>Params & Local Storage</li>
          </ol>
        </div>
      </BaseModal>
    </nav>
  </header>
</template>

<script setup>
import { RouterLink, useRoute, useRouter } from 'vue-router'
import { uid } from 'uid'
import { ref } from 'vue'
import BaseModal from './BaseModal.vue'

const savedCities = ref([])
const route = useRoute()
const router = useRouter()
const addCity = () => {
  if (localStorage.getItem('savedCities')) {
    savedCities.value = JSON.parse(localStorage.getItem('savedCities'))
  }
  const locationObj = {
    id: uid(),
    state: route.params.state,
    city: route.params.city,
    coords: {
      lat: route.query.lat,
      lng: route.query.lng,
    },
  }
  savedCities.value.push(locationObj)
  localStorage.setItem('savedCities', JSON.stringify(savedCities.value))
  let query = Object.assign({}, route.query)
  delete query.preview //  need to remove /preview from the url and the city page
  query.id = locationObj.id
  router.replace({ query })
}
const modalActive = ref(null)
const toggleModal = () => {
  modalActive.value = !modalActive.value
}
</script>

<style scoped>
header {
  position: sticky;
  top: 0;
}

nav {
  display: flex;
  flex-direction: row;
  align-items: center;
  gap: 1rem;
  padding: 1.5rem 0 1.5rem 0;
}

.nav-wrapper {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  color: var(--text-light);
}

.nav-wrapper .sun {
  font-size: 1.5rem;
  color: rgb(59, 252, 0);
}

.nav-wrapper p {
  font-size: 1.5rem;
  font-weight: 600;
}

.nav-wrapper:hover {
  animation: tilt-shaking 0.25s infinite;
}

/* animation because why not */

@keyframes tilt-shaking {
  0% {
    transform: rotate(0deg);
  }
  25% {
    transform: rotate(5deg);
  }
  50% {
    transform: rotate(0eg);
  }
  75% {
    transform: rotate(-5deg);
  }
  100% {
    transform: rotate(0deg);
  }
}

/* icon */
.modal {
  display: flex;
  justify-content: flex-end;
  flex: 1 1 0%;
  gap: 0.75rem;
}

.modal i {
  font-size: 1.25rem;
  transition-duration: 150ms;
  cursor: pointer;
}

/* wrapper */
.modal-wrapper {
  color: var(--text-dark);
}

.modal-wrapper h1 {
  margin-bottom: 0.25rem;
  font-size: 1.3rem;
}

.modal-wrapper p {
  margin-bottom: 1rem;
}

.modal-wrapper h2 {
  font-size: 1.3rem;
}

.modal-wrapper ol {
  margin-bottom: 1rem;
  list-style-type: decimal;
  list-style-position: inside;
}
</style>
