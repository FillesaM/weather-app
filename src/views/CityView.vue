<template>
  <div>
    <Suspense>
        <AsyncCityView />
        <template #fallback>
           <CityViewSceleton/>
        </template>
    </Suspense>
  </div>
</template>

<script setup>
import AsyncCityView from '../components/AsyncVityView.vue'
import CityViewSceleton from '../components/CityViewSceleton.vue';
</script>
