<script lang="ts" setup>
import { ref, reactive, type Reactive, type Ref, onMounted } from "vue";

type Geolocation = {
  latitude?: number | 0;
  longitude?: number | 0;
};

let coords: Reactive<Geolocation | undefined> = reactive({});
const geolocationBlockedByUser: Ref<boolean> = ref(false);

const getGeolocation = async (): Promise<void> => {
  navigator.geolocation.getCurrentPosition(
    () => { },
    () => { },
    {}
  );
  navigator.geolocation.getCurrentPosition(
    async (position: { coords: Geolocation; }) => {
      coords = position.coords;
    },
    (error: { message: string; }) => {
      geolocationBlockedByUser.value = true;
      console.error(error.message);
    },
    { maximumAge: 60000, timeout: 5000, enableHighAccuracy: true }
  );
};

onMounted(async () => {
  await getGeolocation();
});
</script>

<template>
  <div v-if="coords && !geolocationBlockedByUser">{{ coords.latitude }} {{ coords.longitude }}</div>
  <div v-if="geolocationBlockedByUser">User denied access</div>
</template>
