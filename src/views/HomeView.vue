<script setup>
import { ref } from "vue";
import axios from 'axios';
import { useRouter } from 'vue-router';
import CityList from '../components/CityList.vue';

  const searchQuery = ref("");
  const queryTimeout = ref(null);
  const mapboxAPIKey = "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q";
  const mapboxSearchResults = ref(null);
  const searchError = ref(null);
  const router = useRouter();
  const getSearchResults = () => {
    clearTimeout(queryTimeout.value);
      queryTimeout = setTimeout(async () => {
        if(searchQuery.value !== ""){
            try{
              const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`);
              return mapboxSearchResults.value = result.data.features;
            }catch{
              searchError.value = true;
            }
        }
        mapboxSearchResults.value = null;
        return;
      }, 300);
  };

  const previewCity = (searchResult) => {
    const [city, state] = searchResult.place_name.split(",");
    console.log(city, state);
    router.push({
      name: "cityView",
      params: { state: state, city: city },
      query: {
        lat: searchResult.geometry.coordinates[1],
        lng: searchResult.geometry.coordinates[0],
        preview: true,
      }
    });
  };
</script>

<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input type="text" @input="getSearchResults" v-model="searchQuery" placeholder="Search for a city or state" class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shodow-[0px_1px_0_0_#004E71]">
      <ul class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]" v-if="mapboxSearchResults">
        <p v-if="searchError">
          Sorry, something went wrong, please try again.
        </p>
        <p v-if="!serverError && mapboxSearchResults.length === 0">
          No results match your query, try a different term.
        </p>
        <template v-else>
          <li v-for="searchResult in mapboxSearchResults" :key="searchResult.id" class="py-2 cursor-pointer" @click="previewCity(searchResult)"> {{ searchResult.place_name }}</li>
        </template>
      </ul>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
        <CityList>
          <template #fallback>
            <p>Loading...</p>
          </template>
        </CityList>
      </Suspense>
    </div>
  </main>
</template>
