<template>
  <app-header>
    <span class="text-xl font-bold">Settings</span>
    <div
      class="cursor-pointer"
      @click="goBack"
    >
      <span class="material-symbols-outlined"> close </span>
    </div>
  </app-header>
  <section class="min-h-screen py-8">
    <div class="container">
      <city-search />

      <div class="space-y-2">
        <div
          v-if="store.loading"
          class="loading"
        >
          Loading...
        </div>
        <div
          v-if="store.error"
          class="error"
        >
          {{ store.error }}
        </div>
        <template v-if="store.cities">
          <city-card
            v-for="city in store.cities"
            :key="city.id"
            :city="city"
            @click="goToWeather(city.coords)"
          />
        </template>
      </div>
    </div>
  </section>
</template>

<script setup lang="ts">
import { defineAsyncComponent, onMounted } from 'vue';
import { useRouter } from 'vue-router';
import { useCitiesStore } from '@/stores/cities';
import type { Coordinates, Weather } from '@/models';

const AppHeader = defineAsyncComponent(() => import('@/components/AppHeader.vue'));
const CitySearch = defineAsyncComponent(() => import('@/components/city/CitySearch.vue'));
const CityCard = defineAsyncComponent(() => import('@/components/city/CityCard.vue'));

const store = useCitiesStore();

const router = useRouter();

const goBack = () => {
  const weather: Weather = JSON.parse(localStorage.getItem('weather') ?? '');
  router.push({ name: 'weather', query: { lat: weather.coord.lat, lon: weather.coord.lon } });
};

const goToWeather = (coords: Coordinates) => {
  router.push({ name: 'weather', query: { lat: coords.lat, lon: coords.lon } });
};

onMounted(() => {
  store.getCities();
});
</script>

