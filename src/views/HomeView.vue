<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input 
        type="text"
        v-model="searchQuery"
        @input="getSearchResults"
        placeholder="Chercher une ville"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0#004E71]"
      >
      <ul 
        class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
        v-if="mapboxSearchResults"  
      >
        <p v-if="searchError">Une erreur a été rencontrée, veuillez réessayer.</p>
        <p v-if="!searchError && mapboxSearchResults.length === 0">Pas de résultats pour ce terme, veuillez en essayer un autre.</p>
        <template v-else>
          <li
            v-for="searchResults in mapboxSearchResults"
            :key="searchResults.id"
            class="py-2 cursor-pointer"
            @click="previewCity(searchResults)"
          >
            {{ searchResults.place_name }}
          </li>
        </template>
      </ul>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
        <CityList />
        <template #fallback>
          <CityCardSkeleton/>
        </template>
      </Suspense>
    </div>
  </main>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';
import { useRouter } from "vue-router";
import CityList from "../components/CityList.vue";
import CityCardSkeleton from "../components/CityCardSkeleton.vue";

const router = useRouter();
const previewCity = (searchResults) => {
  console.log(searchResults);
  const [city, state] = searchResults.place_name.split(',');
  router.push({
    name: "cityView",
    params:{state: state.replaceAll(" ",""), city:city},
    query: {
      lat: searchResults.geometry.coordinates[1],
      lng: searchResults.geometry.coordinates[0],
      preview: true,
    }
  })
}

const mapboxAPIKey = "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q";
const searchQuery = ref("");
const queryTimeout = ref(null);
const mapboxSearchResults = ref(null);
const searchError = ref(null);

const getSearchResults = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if(searchQuery.value !== ""){
      try{
        const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`);
        mapboxSearchResults.value = result.data.features;
        return;
      }catch{
        searchError.value = true;
      }
    }
    mapboxSearchResults.value = null;    
  },300)
}
</script>
