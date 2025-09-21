<script setup lang="ts">
import { computed, onMounted, ref, type ComputedRef, type Ref } from "vue";
import WeatherReport from "./WeatherReport.vue";

type Location = {
  status?: string;
  message?: string;
  lat?: number;
  lon?: number;
};
let location: Ref<Location> = ref({});

const coords = computed(() => {
  return {
    latitude: location.value?.lat ?? 0,
    longitude: location.value?.lon ?? 0,
  };
});

const endpoint = "http://ip-api.com/json/?fields=status,message,lat,lon";

const getGeolocation = async (): Promise<void> => {
  try {
    const response = await fetch(endpoint);
    location.value = await response.json();
  } catch (error) {
    console.error(error);
  }
};

onMounted(async () => {
  await getGeolocation();
});
</script>

<template>
  <div v-if="location?.status === 'success'">
    <!-- {{ location }} -->
    <weather-report :coords="coords" />
  </div>
  <div v-else>{{ location?.message }}</div>
</template>
