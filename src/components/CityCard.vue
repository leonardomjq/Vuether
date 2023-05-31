<template>
  <div class="wrapper">
    <div class="card-wrapper">
      <h2>{{ city.city }}</h2>
      <h3>{{ city.state }}</h3>
    </div>

    <div class="temp-wrapper">
      <p>{{ Math.round(city.weather.main.temp) }}&deg;</p>
    </div>
  </div>
</template>

<script setup>
defineProps({
  city: {
    type: Object,
    default: () => {},
  },
})
</script>

<style scoped>
.wrapper {
  display: flex;
  padding: 0.5rem;
  border: 1px solid var(--text-light);
  border-radius: 0.25rem;
  margin: 0.5rem 0 0.25rem 0;
  cursor: pointer;
}

.card-wrapper {
  display: flex;
  flex-direction: column;
  flex: 1 1 0%;
}

.card-wrapper h2 {
  font-size: 1.5rem;
}

.temp-wrapper {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.temp-wrapper p {
  font-size: 2rem;
}
</style>
