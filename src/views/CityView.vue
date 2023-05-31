<template>
  <div>
    <Suspense>
      <AsyncCityView />
      <template #fallaback>
        <p>Loading...</p>
      </template>
    </Suspense>
  </div>
</template>

<style scoped></style>

<script setup>
import AsyncCityView from '../components/AsyncCityView.vue'
</script>
