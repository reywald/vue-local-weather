<script lang="ts" setup>
import { type Reactive, reactive, onMounted } from "vue";

type WeatherData = {
  location?: {
    localtime: Date;
    name: string;
    region: string;
  };
  current?: {
    temp_c: number;
    temp_f: number;
    precip_mm: number;
    condition: {
      text: string;
      icon: string;
    };
    wind_degree: number;
    wind_kph: number;
    wind_mph: number;
  };
};

type Coords = {
  latitude: number;
  longitude: number;
};

interface Props {
  coords: Coords;
}

const props = defineProps<Props>();
let data: Reactive<WeatherData | undefined> = reactive({});

const fetchWeather = async (coords: Coords): Promise<WeatherData> => {
  const { latitude, longitude } = coords;

  const query = `${latitude}, ${longitude}`;
  const response = await fetch(
    `https://api.weatherapi.com/v1/current.json?key=${
      import.meta.env.VITE_APP_WEATHER_API_KEY
    }&q=${query}`
  );
  const data = (await response) && response.json();
  return data;
};

onMounted(async () => {
  const { latitude, longitude } = props.coords;
  const weatherResponse = await fetchWeather({ latitude, longitude });
  data = weatherResponse;
});
</script>

<template>
  <div>
    <article v-if="data && data.current">{{ data.current }}</article>
    <div v-else>Loading...</div>
  </div>
</template>
